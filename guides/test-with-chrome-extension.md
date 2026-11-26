# Test With Chrome Extension

The AdUnblock DevTool extension shows AdUnblock recovering the ads on your own
site, in your own browser, before you add the tag to your site. Only your
browser sees the test: your visitors aren't affected.

> **There's nothing to copy or paste.** The extension connects to your
> AdUnblock account through your dashboard.

## 1. Get ready

- An AdUnblock account. [Sign up free](https://ad-unblock.com/signup) if you
  don't have one.
- Chrome with an ad blocker installed and switched on for your site: AdBlock,
  Adblock Plus, uBlock Origin or AdGuard.

AdUnblock only steps in when it finds an ad blocker. Without one, your ads show
as they always do, and there is nothing to recover.

## 2. Install the extension

1. Open [AdUnblock DevTool](https://chromewebstore.google.com/detail/adunblock-devtool/gnlaoijjmmehcmccdkmbbcmmlpegcpmi)
   in the Chrome Web Store and click **Add to Chrome**.
2. Pin it to your toolbar, so its icon is one click away.

## 3. Connect your account

1. Open your website in Chrome and click the **AdUnblock DevTool** icon.
2. Click **Connect your AdUnblock account**. Your dashboard opens in a new tab;
   sign in if you need to.
3. If your site isn't in your account yet, add it there. You don't need to
   install the tag first.

The dashboard tab then closes on its own, you're back on your site, and the test
starts. You only connect once: after that, click the icon on any of your sites
and choose **Test ad recovery**.

## 4. Read the result

The page reloads with AdUnblock on, and the extension checks three things:

- ✅ **Recovery allowed**: your account can recover ads on this site
- ✅ **Ad blocker found**: your ad blocker is on for this site
- ✅ **Ads showing**: how many ads are on the page despite your ad blocker

The number of ads also shows on the extension's icon. When all three pass,
install the tag from your dashboard's setup guide to recover ads for every
visitor.

> **Test impressions.** Before your tag is installed, test page views use your
> account's impressions, like any other recovered impression. The free plan
> includes 10,000 impressions a month.

## If a check fails

| What the extension says | What to do |
| --- | --- |
| Your site isn't in this account | Add the site in your dashboard under **Manage Sites**, then click **Test again**. With more than one AdUnblock account, check that the extension uses the one that has this site. |
| Recovery is switched off for this site | Switch it on in **Manage Sites**, then test again. |
| Your account has used all its impressions | Upgrade your plan from your dashboard, then test again. |
| No ad blocker found | Turn your ad blocker on for this site. A site you have allowlisted in your ad blocker counts as off. |
| No ads showing yet | Some sites load their ads as you scroll. Scroll down the page, then click **Count ads again**. Still none? Write to us with your site's address. |

## Testing a client's site

Agencies can test a site in a client's account without connecting: click
**Enter an account ID instead** and type the client's 8-digit Account ID, shown
at the top of their AdUnblock dashboard.

## Good to know

- The extension adds AdUnblock after your page starts loading, while an
  installed tag loads with the page. On sites with complex ad setups, results
  can differ a little from what your installed tag will do.
- The extension keeps only your Account ID and your sites' domains, in Chrome;
  your AdUnblock dashboard passes them to it. It works on a page only when you
  click its icon there. See the
  [privacy policy](https://ad-unblock.com/privacy/chrome-ext).

## Need help?

If anything doesn't work as described, contact our support team at
[support@ad-unblock.com](mailto:support@ad-unblock.com).

## Next steps

- [HTML/JavaScript integration](html-javascript.md): install the AdUnblock tag
  on any website
- [WordPress integration](wordpress.md): use our WordPress plugin for easy setup
- [Server-side integration](server-side.md): resolve the script URL on your
  server, so host rotations reach your site without a code change
