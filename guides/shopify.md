# Shopify Implementation

This guide covers implementing AdUnblock in Shopify stores to recover lost ad revenue from customers using ad blockers.

## Installation Methods

### Method 1: Theme Code Injection (Recommended)

#### Step 1: Access Theme Editor

1. Go to your Shopify admin panel
2. Navigate to **Online Store** → **Themes**
3. Click **Actions** → **Edit code** on your active theme

#### Step 2: Add to Theme.liquid

Add the AdUnblock script to your `theme.liquid` file in the `<head>` section:

```liquid
<!-- In layout/theme.liquid -->
<head>
  <!-- Your existing head content -->
  
  <!-- AdUnblock Script -->
  <script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="{{ settings.adunblock_site_id }}" async></script>
</head>
```

#### Step 3: Add Theme Settings

Add AdUnblock settings to your theme configuration:

```json
// In config/settings_schema.json
{
  "name": "AdUnblock",
  "settings": [
    {
      "type": "text",
      "id": "adunblock_site_id",
      "label": "AdUnblock Site ID",
      "info": "Enter your AdUnblock site ID from your dashboard"
    },
    {
      "type": "checkbox",
      "id": "adunblock_enabled",
      "label": "Enable AdUnblock",
      "default": true
    },
    {
      "type": "text",
      "id": "adunblock_message_title",
      "label": "Ad Blocker Message Title",
      "default": "Ad Blocker Detected"
    },
    {
      "type": "textarea",
      "id": "adunblock_message_text",
      "label": "Ad Blocker Message Text",
      "default": "Please disable your ad blocker to support our store and see all available products."
    }
  ]
}
```

#### Step 4: Conditional Loading

Update the script to only load when enabled:

```liquid
{% if settings.adunblock_enabled %}
  <script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="{{ settings.adunblock_site_id }}" async></script>
{% endif %}
```

### Method 2: Custom App Development

#### Create a Shopify App

For more advanced integration, create a custom Shopify app:

```javascript
// app.js
const express = require('express');
const { Shopify } = require('@shopify/shopify-api');

const app = express();

app.get('/install-adunblock', async (req, res) => {
  const { shop } = req.query;
  
  // Add AdUnblock script to shop theme
  const themeId = await getActiveTheme(shop);
  await addAdUnblockScript(shop, themeId);
  
  res.json({ success: true });
});

async function addAdUnblockScript(shop, themeId) {
  const script = `
    <script src="https://cdn.ad-unblock.com/sdk.js" 
            data-site-id="{{ shop.metafields.adunblock.site_id }}" 
            async></script>
  `;
  
  // Add to theme.liquid
  await Shopify.rest.Asset.save({
    session: shopifySession,
    theme_id: themeId,
    key: 'layout/theme.liquid',
    value: updatedThemeContent
  });
}
```

## Custom Implementation

### AdBlocker Detection Banner

Create a custom banner for ad blocker users:

```liquid
<!-- In snippets/adunblock-banner.liquid -->
<div id="adunblock-banner" style="display: none;">
  <div class="adunblock-banner-content">
    <div class="adunblock-banner-text">
      <h3>{{ settings.adunblock_message_title }}</h3>
      <p>{{ settings.adunblock_message_text }}</p>
    </div>
    <div class="adunblock-banner-actions">
      <button onclick="adunblockWhitelist()" class="btn btn-primary">
        Whitelist Store
      </button>
      <button onclick="adunblockDismiss()" class="btn btn-secondary">
        Continue Shopping
      </button>
    </div>
  </div>
</div>

<style>
#adunblock-banner {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  background: #f8f9fa;
  border-bottom: 1px solid #dee2e6;
  padding: 15px;
  z-index: 9999;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.adunblock-banner-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  max-width: 1200px;
  margin: 0 auto;
}

.adunblock-banner-text h3 {
  margin: 0 0 5px 0;
  font-size: 16px;
  color: #495057;
}

.adunblock-banner-text p {
  margin: 0;
  font-size: 14px;
  color: #6c757d;
}

.adunblock-banner-actions {
  display: flex;
  gap: 10px;
}

.btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.btn-primary {
  background: #007bff;
  color: white;
}

.btn-secondary {
  background: #6c757d;
  color: white;
}

@media (max-width: 768px) {
  .adunblock-banner-content {
    flex-direction: column;
    text-align: center;
    gap: 15px;
  }
}
</style>

<script>
function adunblockWhitelist() {
  alert('Please add {{ shop.domain }} to your ad blocker\'s whitelist and refresh the page.');
}

function adunblockDismiss() {
  document.getElementById('adunblock-banner').style.display = 'none';
  document.cookie = 'adunblock_dismissed=true; max-age=86400; path=/';
}

// Check if already dismissed
if (document.cookie.includes('adunblock_dismissed=true')) {
  document.getElementById('adunblock-banner').style.display = 'none';
}
</script>
```

### Include in Theme

Add the banner to your theme layout:

```liquid
<!-- In layout/theme.liquid -->
<body>
  {% include 'adunblock-banner' %}
  
  <!-- Your existing body content -->
</body>
```

### JavaScript Integration

Add custom JavaScript to handle AdUnblock events:

```liquid
<!-- In layout/theme.liquid before closing </body> -->
<script>
document.addEventListener('DOMContentLoaded', function() {
  // Wait for AdUnblock to load
  function initAdUnblock() {
    if (window.AdUnblock) {
      window.AdUnblock.onDetected = function(blocker) {
        // Show banner
        document.getElementById('adunblock-banner').style.display = 'block';
        
        // Track in Shopify analytics
        if (typeof gtag !== 'undefined') {
          gtag('event', 'ad_blocker_detected', {
            'custom_parameter': blocker.name
          });
        }
        
        // Adjust page layout
        document.body.style.paddingTop = '80px';
      };
      
      window.AdUnblock.onNotDetected = function() {
        // Hide banner if shown
        document.getElementById('adunblock-banner').style.display = 'none';
      };
    } else {
      // Retry after a short delay
      setTimeout(initAdUnblock, 100);
    }
  }
  
  initAdUnblock();
});
</script>
```

## Product Page Integration

### Show Alternative Content

Replace ad slots with alternative content for ad blocker users:

```liquid
<!-- In templates/product.liquid -->
<div class="product-recommendations">
  <div class="ad-slot" data-adunblock-alt="recommended-products">
    <!-- Regular ad content -->
    <div class="advertisement">
      <!-- Your ad code here -->
    </div>
    
    <!-- Alternative content for ad blocker users -->
    <div class="alternative-content" style="display: none;">
      <h3>You Might Also Like</h3>
      {% assign related_products = collections.all.products | where: 'type', product.type | limit: 4 %}
      <div class="recommended-products">
        {% for related_product in related_products %}
          <div class="recommended-product">
            <a href="{{ related_product.url }}">
              <img src="{{ related_product.featured_image | img_url: '150x150' }}" alt="{{ related_product.title }}">
              <h4>{{ related_product.title }}</h4>
              <p>{{ related_product.price | money }}</p>
            </a>
          </div>
        {% endfor %}
      </div>
    </div>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  if (window.AdUnblock) {
    window.AdUnblock.onDetected = function() {
      // Show alternative content instead of ads
      document.querySelectorAll('.ad-slot .advertisement').forEach(function(ad) {
        ad.style.display = 'none';
      });
      document.querySelectorAll('.ad-slot .alternative-content').forEach(function(alt) {
        alt.style.display = 'block';
      });
    };
  }
});
</script>
```

## Cart and Checkout Integration

### Cart Page Messaging

Add messaging to cart pages:

```liquid
<!-- In templates/cart.liquid -->
<div id="cart-adunblock-message" style="display: none;">
  <div class="cart-message">
    <h3>Support {{ shop.name }}</h3>
    <p>We rely on advertising to offer competitive prices. Please consider disabling your ad blocker.</p>
    <button onclick="location.reload()" class="btn btn-primary">Refresh Page</button>
  </div>
</div>

<script>
if (window.AdUnblock) {
  window.AdUnblock.onDetected = function() {
    document.getElementById('cart-adunblock-message').style.display = 'block';
  };
}
</script>
```

### Checkout Analytics

Track ad blocker usage during checkout:

```liquid
<!-- In checkout.liquid -->
<script>
{% if checkout.completed_at %}
  // Track checkout completion with ad blocker data
  if (window.AdUnblock && window.AdUnblock.isBlocked()) {
    // Send to analytics
    fetch('/apps/adunblock/checkout-completed', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        order_id: {{ checkout.order_id }},
        ad_blocker_detected: true,
        total_price: {{ checkout.total_price }}
      })
    });
  }
{% endif %}
</script>
```

## Liquid Filters and Functions

### Custom Liquid Filters

Create custom filters for AdUnblock:

```liquid
<!-- In snippets/adunblock-helpers.liquid -->
{% comment %}
  AdUnblock helper functions
{% endcomment %}

{% assign adunblock_enabled = settings.adunblock_enabled %}
{% assign adunblock_site_id = settings.adunblock_site_id %}

{% if adunblock_enabled and adunblock_site_id != blank %}
  {% assign show_adunblock = true %}
{% else %}
  {% assign show_adunblock = false %}
{% endif %}
```

### Conditional Content

Show different content based on AdUnblock settings:

```liquid
{% include 'adunblock-helpers' %}

{% if show_adunblock %}
  <div class="adunblock-section">
    <!-- AdUnblock-enabled content -->
  </div>
{% else %}
  <div class="regular-section">
    <!-- Regular content -->
  </div>
{% endif %}
```

## Testing in Shopify

### Preview Theme

1. **Enable Preview Mode**: Use Shopify's theme preview to test
2. **Install Ad Blocker**: Test with uBlock Origin or similar
3. **Check Functionality**: Verify detection and messaging work
4. **Test Responsiveness**: Ensure mobile compatibility

### Debug Mode

Add debug logging:

```liquid
<script>
{% if settings.adunblock_debug %}
  window.AdUnblockDebug = true;
  
  if (window.AdUnblock) {
    console.log('AdUnblock loaded successfully');
    
    window.AdUnblock.onDetected = function(blocker) {
      console.log('Ad blocker detected:', blocker);
      // Your regular detection code
    };
  }
{% endif %}
</script>
```

## Advanced Features

### Metafields Integration

Store AdUnblock data in Shopify metafields:

```javascript
// Store ad blocker detection in customer metafields
if (window.AdUnblock) {
  window.AdUnblock.onDetected = function(blocker) {
    // Store in customer metafields via AJAX
    fetch('/apps/adunblock/customer-data', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        customer_id: {{ customer.id }},
        ad_blocker: blocker.name,
        timestamp: new Date().toISOString()
      })
    });
  };
}
```

### Shopify Plus Features

For Shopify Plus stores, use Scripts:

```javascript
// In scripts/adunblock-checkout.js
if (window.AdUnblock && window.AdUnblock.isBlocked()) {
  // Modify checkout behavior for ad blocker users
  Shopify.Checkout.OrderStatus.addContentBox(
    '<div class="adunblock-checkout-message">' +
    '<h3>Thank you for your purchase!</h3>' +
    '<p>Consider whitelisting our store to support us.</p>' +
    '</div>'
  );
}
```

## Performance Optimization

### Lazy Loading

Load AdUnblock only when needed:

```liquid
<script>
// Only load on product and collection pages
{% if template contains 'product' or template contains 'collection' %}
  (function() {
    var script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', '{{ settings.adunblock_site_id }}');
    document.head.appendChild(script);
  })();
{% endif %}
</script>
```

### CDN Optimization

Ensure scripts are cached:

```liquid
<script>
// Cache AdUnblock script
var adunblockScript = localStorage.getItem('adunblock-script');
if (!adunblockScript) {
  fetch('https://cdn.ad-unblock.com/sdk.js')
    .then(response => response.text())
    .then(script => {
      localStorage.setItem('adunblock-script', script);
      eval(script);
    });
} else {
  eval(adunblockScript);
}
</script>
```

## Troubleshooting

### Common Issues

**Script not loading:**
- Check theme.liquid includes the script
- Verify site ID is correct
- Ensure no Content Security Policy blocks

**Banner not showing:**
- Check CSS z-index values
- Verify JavaScript event listeners
- Test with different ad blockers

**Mobile issues:**
- Test responsive design
- Check touch event handling
- Verify mobile-specific styling

### Debug Tools

```liquid
<script>
// Debug helper
function debugAdUnblock() {
  console.log('AdUnblock Status:', {
    loaded: !!window.AdUnblock,
    siteId: '{{ settings.adunblock_site_id }}',
    enabled: {{ settings.adunblock_enabled }},
    template: '{{ template }}'
  });
}

// Call debug on page load
document.addEventListener('DOMContentLoaded', debugAdUnblock);
</script>
```

## Best Practices

### 1. User Experience
- Use non-intrusive banner styles
- Provide clear value proposition
- Offer easy dismissal options

### 2. Performance
- Load scripts asynchronously
- Use conditional loading
- Minimize impact on page speed

### 3. Testing
- Test across different themes
- Verify mobile compatibility
- Check with multiple ad blockers

## Next Steps

After implementing AdUnblock in Shopify:

1. **Monitor store analytics** for ad blocker impact
2. **A/B test different messages** and placements
3. **Track conversion rates** for ad blocker users
4. **Optimize based on data** collected
5. **Consider premium features** for advanced stores

For more advanced customization, check out our [API Reference](../api-reference/rest-api.md) guide. 