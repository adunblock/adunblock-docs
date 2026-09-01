# Server-Side Integration

This guide covers resolving the AdUnblock script URL on your server at render
time instead of hardcoding it in your pages.

A server-side integration fetches the current script source from AdUnblock,
then prints the tag with that URL. It buys you two things a hardcoded tag can
never have:

1. **Host rotations reach your site without a code change.** When a filter list
   blocks the host your script is served from, we publish a new one and your
   next page render picks it up.
2. **Your site can be moved on its own.** Send your domain with the request and
   we can rotate *your site alone* onto a fresh host when a filter rule targets
   you specifically — instead of moving every publisher who shares that host.

> **Always send your domain.**
>
> ```
> https://public.adunblocker.com/api/vendor_scripts?domain=example.com
> ```
>
> The parameter is optional and backwards compatible, but an integration that
> omits it can only ever receive the shared default URL.

## Why the domain matters

Ad blocking filter rules are not always written against a host on its own. Many
are scoped to the publishers they were reported for:

```
||example-cdn.workers.dev^$domain=yoursite.com|othersite.com
```

That rule blocks the host **only on the sites it names**. Everyone else serving
from the same host is unaffected.

- **Without `?domain=`**, every site on that host gets the same answer, so the
  only way to escape the rule is to move *all* of them to a new host. That is
  slower, costs a new domain, and drags sites that were never blocked onto an
  unproven host — so we do it reluctantly, and your recovery stays broken in the
  meantime.
- **With `?domain=`**, we publish an override for your site alone. Your pages
  start rendering a host the rule does not name, usually within one cache
  cycle, and nobody else's integration changes.

A site whose integration does not identify itself cannot receive an override.
It keeps being served the default URL — which, for a rule scoped to that site,
is exactly the host that is blocked for it. This is the single most useful
thing you can add to a server-side integration.

## The sources endpoint

```
GET https://public.adunblocker.com/api/vendor_scripts?domain=<your site domain>
```

Your dashboard's install page shows the exact sources URL for your account — it
may carry a vendor segment (`/api/vendor_scripts/<vendor>`). Use the URL shown
there and append the `domain` parameter to it.

**Response** — a JSON array of script URLs, most preferred first. Use the first
entry:

```bash
curl 'https://public.adunblocker.com/api/vendor_scripts?domain=example.com'
```

```json
[
  "https://ad-blocker-7.onyx-drift-cdn-15.workers.dev/d2gbkh6c.js"
]
```

The response shape is the same with or without `domain` — only the URLs inside
it can differ. If you already parse `sources[0]`, adding the parameter changes
nothing about how you read the answer.

| Header | Value |
|--------|-------|
| `Content-Type` | `application/json` |
| `Cache-Control` | `public, max-age=0, must-revalidate` |
| `ETag` | Changes only when the published list changes |

The endpoint honours `If-None-Match` and answers `304 Not Modified`, so
revalidating is cheap if your HTTP client keeps the ETag.

### What to send in `domain`

- **The domain your visitors see** — the host of the page being rendered
  (`$_SERVER['HTTP_HOST']`, `req.headers.host`), not your origin server, your
  CDN hostname, or your ad server.
- **Just the host.** `example.com` is the canonical form. A full URL, uppercase,
  a port, or a trailing dot are all normalised for you.
- **Subdomains resolve upward.** `www.example.com` matches an override placed on
  `example.com`, most specific first — the same subdomain semantics the filter
  rule that caused the override has.
- **Multi-site installs work per request.** Read the host off the request being
  served and every site in the network gets its own answer.
- **A missing, unknown or malformed domain is never an error.** It falls back to
  the default list, which is what integrations deployed before this parameter
  existed already receive. Adding it is purely additive — you can ship it on its
  own, at any time, with no coordination.

## Implementation

Whatever the language, four rules matter:

1. **Cache the resolved URL for about 5 minutes.** This lookup sits on the
   pageview path.
2. **Cache the failure too**, briefly (~30 seconds), so an outage cannot put a
   blocking request on every pageview. Use a short timeout, ~3 seconds.
3. **Never hardcode a fallback URL.** A source we have rotated away from renders
   a tag that recovers nothing, and nothing on the page would say so. Render no
   tag and log it instead.
4. **Print the tag early**, before your ad code, and keep the
   `data-cfasync="false"` and `nowprocket` attributes ahead of `src`. See
   [HTML/JavaScript](html-javascript.md) for what those opt out of.

Keep whatever `data-` attributes the tag on your dashboard carries — only the
`src` is resolved at render time.

### PHP

```php
<?php
// Read the current script source, and cache it for ~5 minutes with whatever
// cache you already run — this sits on the pageview path. The domain says which
// site is asking, so we can move THIS site alone if its host is ever blocked.
$url = 'https://public.adunblocker.com/api/vendor_scripts?domain='
     . urlencode($_SERVER['HTTP_HOST'] ?? '');
$sources = json_decode(@file_get_contents($url), true);
$src = $sources[0] ?? null;

// No fallback URL: a source we have rotated away from recovers nothing, and
// nothing on the page would say so. Render no tag instead.
if ($src === null) {
    error_log('AdUnblock: no script source available');
} else { ?>
<script data-cfasync="false" nowprocket
        src="<?= htmlspecialchars($src, ENT_QUOTES) ?>"
        async data-site-id="YOUR_SITE_ID"></script>
<?php } ?>
```

### WordPress

The [WordPress plugin](wordpress.md) resolves the source for you. If you install
the tag by hand, do it in your child theme's `functions.php` and keep the result
in a transient:

```php
<?php
// functions.php of your CHILD theme — survives theme updates.
add_action('wp_head', function () {
    $src = get_transient('adunblock_script_src');

    if ($src === false) {
        // The domain lets us move THIS site alone if its host is ever blocked.
        $url = 'https://public.adunblocker.com/api/vendor_scripts?domain='
             . urlencode(wp_parse_url(home_url(), PHP_URL_HOST));
        $response = wp_remote_get($url, ['timeout' => 3]);
        $sources = json_decode(wp_remote_retrieve_body($response), true);
        $src = $sources[0] ?? '';
        // Cache the miss too, briefly, so an outage can't put a blocking
        // request on every pageview.
        set_transient('adunblock_script_src', $src, $src ? 5 * MINUTE_IN_SECONDS : 30);
    }

    // No fallback URL — see the PHP snippet.
    if ($src === '') {
        error_log('AdUnblock: no script source available');
        return;
    }
    ?>
  <script data-cfasync="false" nowprocket
          src="<?= esc_url($src) ?>" async data-site-id="YOUR_SITE_ID"></script>
<?php }, 1); // priority 1 → prints before ad scripts
```

### Node.js / Express

```javascript
const SOURCES_URL = 'https://public.adunblocker.com/api/vendor_scripts';
const TTL_MS = 5 * 60 * 1000;
const MISS_TTL_MS = 30 * 1000;

const cache = new Map(); // domain → { src, expires }

async function scriptSourceFor(domain) {
  const hit = cache.get(domain);
  if (hit && hit.expires > Date.now()) return hit.src;

  let src = null;
  try {
    const response = await fetch(
      `${SOURCES_URL}?domain=${encodeURIComponent(domain)}`,
      { signal: AbortSignal.timeout(3000) }
    );
    if (response.ok) {
      const sources = await response.json();
      if (Array.isArray(sources) && typeof sources[0] === 'string') {
        src = sources[0];
      }
    }
  } catch (error) {
    console.error('AdUnblock: could not read script sources', error);
  }

  // Cache the miss too, so an outage can't block every pageview.
  cache.set(domain, { src, expires: Date.now() + (src ? TTL_MS : MISS_TTL_MS) });
  return src;
}

app.use(async (req, res, next) => {
  // The host of the page being served — that is the domain an override keys on.
  res.locals.adunblockSrc = await scriptSourceFor(req.headers.host || '');
  next();
});
```

Render no tag when `adunblockSrc` is null:

```html
<% if (adunblockSrc) { %>
<script data-cfasync="false" nowprocket src="<%= adunblockSrc %>"
        async data-site-id="YOUR_SITE_ID"></script>
<% } %>
```

### Next.js (App Router)

`fetch`'s `revalidate` gives you the 5-minute cache for free:

```jsx
// app/layout.jsx
import { headers } from 'next/headers';

const SOURCES_URL = 'https://public.adunblocker.com/api/vendor_scripts';

async function scriptSource() {
  // The requested host, so a multi-domain deployment resolves per site.
  const host = (await headers()).get('host') ?? '';

  try {
    const response = await fetch(
      `${SOURCES_URL}?domain=${encodeURIComponent(host)}`,
      { next: { revalidate: 300 } }
    );
    if (!response.ok) return null;
    const sources = await response.json();
    return typeof sources?.[0] === 'string' ? sources[0] : null;
  } catch (error) {
    console.error('AdUnblock: could not read script sources', error);
    return null;
  }
}

export default async function RootLayout({ children }) {
  const src = await scriptSource();

  return (
    <html lang="en">
      <head>
        {/* No fallback URL — render nothing rather than a retired host. */}
        {src && (
          <script
            data-cfasync="false"
            nowprocket=""
            src={src}
            async
            data-site-id="YOUR_SITE_ID"
          />
        )}
      </head>
      <body>{children}</body>
    </html>
  );
}
```

`nowprocket=""` rather than `nowprocket` — React drops a bare `true` on an
attribute it does not know, and an empty value renders the attribute intact.

On Next.js 14 and earlier, `headers()` is synchronous: use
`headers().get('host')` without `await`.

## Adding the domain to an existing integration

If you already resolve the script source server-side, this is a one-line change:

```diff
- $url = 'https://public.adunblocker.com/api/vendor_scripts';
+ $url = 'https://public.adunblocker.com/api/vendor_scripts?domain='
+      . urlencode($_SERVER['HTTP_HOST'] ?? '');
```

Nothing else has to change. Until an override exists for your site you get the
same list you got before, so there is no flag day and nothing to roll back if
you deploy it early — which is the point: the parameter has to already be in
place on the day a rule names your site.

Verify from a shell after deploying:

```bash
curl 'https://public.adunblocker.com/api/vendor_scripts?domain=yoursite.com'
```

If that returns a URL and your pages render it, you are done.

## Checklist

- [ ] The request carries `?domain=` with the host of the page being rendered
- [ ] You read `sources[0]`, and treat an empty or non-array response as "no source"
- [ ] The result is cached for ~5 minutes, failures for ~30 seconds
- [ ] The lookup has a short timeout and never blocks a pageview on a retry
- [ ] There is no hardcoded fallback script URL anywhere in the integration
- [ ] The tag prints before your ad code, with `data-cfasync="false"` and
      `nowprocket` ahead of `src`
- [ ] A missing source logs something you will see

## Related

- [HTML/JavaScript](html-javascript.md) — the client-side tag and optimizer opt-outs
- [WordPress](wordpress.md) — plugin and manual installs
- [React/Next.js](react-nextjs.md) — client-side React integration
- [Test With Chrome Extension](test-with-chrome-extension.md) — verify the tag is live
