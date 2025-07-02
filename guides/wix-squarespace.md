# Wix/Squarespace Implementation

This guide covers implementing AdUnblock in Wix and Squarespace websites.

## Wix Implementation

### Method 1: HTML Embed

#### Step 1: Add HTML Embed Element

1. Open your Wix editor
2. Click **Add (+)** → **Embed** → **HTML Embed**
3. Drag the HTML embed element to your desired location
4. Click **Enter Code**

#### Step 2: Add AdUnblock Script

```html
<!-- AdUnblock Script for Wix -->
<script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID" async></script>

<div id="wix-adunblock-message" style="display: none;">
  <div style="background: #f8f9fa; border: 1px solid #dee2e6; padding: 20px; margin: 20px 0; border-radius: 8px; text-align: center;">
    <h3 style="margin: 0 0 10px 0; color: #495057;">Ad Blocker Detected</h3>
    <p style="margin: 0 0 15px 0; color: #6c757d;">Please disable your ad blocker to support our website.</p>
    <button onclick="wixAdunblockWhitelist()" style="background: #007bff; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer; margin-right: 10px;">
      Whitelist Site
    </button>
    <button onclick="wixAdunblockDismiss()" style="background: #6c757d; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer;">
      Continue
    </button>
  </div>
</div>

<script>
function wixAdunblockWhitelist() {
  alert('Please add our site to your ad blocker\'s whitelist and refresh the page.');
}

function wixAdunblockDismiss() {
  document.getElementById('wix-adunblock-message').style.display = 'none';
}

// AdUnblock event handlers
function initWixAdUnblock() {
  if (window.AdUnblock) {
    window.AdUnblock.onDetected = function(blocker) {
      document.getElementById('wix-adunblock-message').style.display = 'block';
    };
    
    window.AdUnblock.onNotDetected = function() {
      document.getElementById('wix-adunblock-message').style.display = 'none';
    };
  } else {
    setTimeout(initWixAdUnblock, 100);
  }
}

document.addEventListener('DOMContentLoaded', initWixAdUnblock);
</script>
```

### Method 2: Custom Code in Site Header

#### Step 1: Access Site Settings

1. In Wix Editor, click **Settings** → **Advanced** → **Custom Code**
2. Click **+ Add Custom Code**
3. Choose **Head** for placement
4. Set to load on **All Pages**

#### Step 2: Add Header Code

```html
<!-- AdUnblock Header Code -->
<script>
// AdUnblock Configuration
window.WixAdUnblockConfig = {
  siteId: 'YOUR_SITE_ID',
  showMessage: true,
  messageTitle: 'Ad Blocker Detected',
  messageText: 'Please disable your ad blocker to support our website.',
  debug: false
};

// Load AdUnblock SDK
(function() {
  var script = document.createElement('script');
  script.src = 'https://cdn.ad-unblock.com/sdk.js';
  script.async = true;
  script.setAttribute('data-site-id', window.WixAdUnblockConfig.siteId);
  document.head.appendChild(script);
})();
</script>
```

#### Step 3: Add Body Code

Add another custom code block for the body:

```html
<!-- AdUnblock Body Code -->
<div id="wix-adunblock-overlay" style="display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 9999; display: flex; align-items: center; justify-content: center;">
  <div style="background: white; padding: 30px; border-radius: 10px; max-width: 500px; text-align: center; margin: 20px;">
    <h2 id="wix-adunblock-title">Ad Blocker Detected</h2>
    <p id="wix-adunblock-text">Please disable your ad blocker to support our website.</p>
    <div style="margin-top: 20px;">
      <button onclick="wixAdunblockWhitelist()" style="background: #007bff; color: white; border: none; padding: 12px 24px; border-radius: 4px; cursor: pointer; margin-right: 10px;">
        Whitelist Site
      </button>
      <button onclick="wixAdunblockDismiss()" style="background: #6c757d; color: white; border: none; padding: 12px 24px; border-radius: 4px; cursor: pointer;">
        Continue Anyway
      </button>
    </div>
  </div>
</div>

<script>
function wixAdunblockWhitelist() {
  alert('Please add ' + window.location.hostname + ' to your ad blocker\'s whitelist and refresh the page.');
}

function wixAdunblockDismiss() {
  document.getElementById('wix-adunblock-overlay').style.display = 'none';
}

function initWixAdUnblock() {
  if (window.AdUnblock && window.WixAdUnblockConfig) {
    window.AdUnblock.onDetected = function(blocker) {
      if (window.WixAdUnblockConfig.showMessage) {
        document.getElementById('wix-adunblock-title').textContent = window.WixAdUnblockConfig.messageTitle;
        document.getElementById('wix-adunblock-text').textContent = window.WixAdUnblockConfig.messageText;
        document.getElementById('wix-adunblock-overlay').style.display = 'flex';
      }
    };
    
    window.AdUnblock.onNotDetected = function() {
      document.getElementById('wix-adunblock-overlay').style.display = 'none';
    };
  } else {
    setTimeout(initWixAdUnblock, 100);
  }
}

document.addEventListener('DOMContentLoaded', initWixAdUnblock);
</script>
```

### Wix Velo Integration

For Wix sites using Velo (formerly Corvid):

```javascript
// In your page code or site code
import { adunblock } from 'public/adunblock.js';

$w.onReady(function () {
  // Initialize AdUnblock
  adunblock.init('YOUR_SITE_ID');
  
  // Handle ad blocker detection
  adunblock.onDetected((blocker) => {
    $w('#adunblockMessage').show();
    $w('#adunblockTitle').text = 'Ad Blocker Detected';
    $w('#adunblockText').text = 'Please disable your ad blocker to support our website.';
  });
  
  adunblock.onNotDetected(() => {
    $w('#adunblockMessage').hide();
  });
});

// Button event handlers
export function whitelistButton_click(event) {
  $w('#adunblockMessage').hide();
  // Show whitelist instructions
}

export function dismissButton_click(event) {
  $w('#adunblockMessage').hide();
}
```

## Squarespace Implementation

### Method 1: Code Injection

#### Step 1: Access Code Injection

1. Go to **Settings** → **Advanced** → **Code Injection**
2. Add code to **Header** section

#### Step 2: Add Header Code

```html
<!-- AdUnblock Header Code for Squarespace -->
<script>
// AdUnblock Configuration
window.SquarespaceAdUnblockConfig = {
  siteId: 'YOUR_SITE_ID',
  showBanner: true,
  bannerPosition: 'top', // 'top' or 'bottom'
  messageTitle: 'Ad Blocker Detected',
  messageText: 'Please disable your ad blocker to support our content.',
  debug: false
};

// Load AdUnblock SDK
(function() {
  var script = document.createElement('script');
  script.src = 'https://cdn.ad-unblock.com/sdk.js';
  script.async = true;
  script.setAttribute('data-site-id', window.SquarespaceAdUnblockConfig.siteId);
  document.head.appendChild(script);
})();
</script>

<style>
/* AdUnblock Styles for Squarespace */
#squarespace-adunblock-banner {
  position: fixed;
  left: 0;
  right: 0;
  background: #f8f9fa;
  border: 1px solid #dee2e6;
  padding: 15px;
  z-index: 9999;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  display: none;
}

#squarespace-adunblock-banner.top {
  top: 0;
}

#squarespace-adunblock-banner.bottom {
  bottom: 0;
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

.adunblock-btn {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  text-decoration: none;
  display: inline-block;
}

.adunblock-btn-primary {
  background: #007bff;
  color: white;
}

.adunblock-btn-secondary {
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
```

#### Step 3: Add Footer Code

```html
<!-- AdUnblock Footer Code for Squarespace -->
<div id="squarespace-adunblock-banner" class="top">
  <div class="adunblock-banner-content">
    <div class="adunblock-banner-text">
      <h3 id="adunblock-banner-title">Ad Blocker Detected</h3>
      <p id="adunblock-banner-text">Please disable your ad blocker to support our content.</p>
    </div>
    <div class="adunblock-banner-actions">
      <button onclick="squarespaceAdunblockWhitelist()" class="adunblock-btn adunblock-btn-primary">
        Whitelist Site
      </button>
      <button onclick="squarespaceAdunblockDismiss()" class="adunblock-btn adunblock-btn-secondary">
        Continue
      </button>
    </div>
  </div>
</div>

<script>
function squarespaceAdunblockWhitelist() {
  alert('Please add ' + window.location.hostname + ' to your ad blocker\'s whitelist and refresh the page.');
}

function squarespaceAdunblockDismiss() {
  document.getElementById('squarespace-adunblock-banner').style.display = 'none';
  
  // Remember dismissal
  localStorage.setItem('adunblock_dismissed', Date.now());
}

function initSquarespaceAdUnblock() {
  if (window.AdUnblock && window.SquarespaceAdUnblockConfig) {
    // Check if recently dismissed
    const dismissed = localStorage.getItem('adunblock_dismissed');
    if (dismissed && Date.now() - parseInt(dismissed) < 24 * 60 * 60 * 1000) {
      return; // Don't show for 24 hours
    }
    
    window.AdUnblock.onDetected = function(blocker) {
      if (window.SquarespaceAdUnblockConfig.showBanner) {
        const banner = document.getElementById('squarespace-adunblock-banner');
        const title = document.getElementById('adunblock-banner-title');
        const text = document.getElementById('adunblock-banner-text');
        
        title.textContent = window.SquarespaceAdUnblockConfig.messageTitle;
        text.textContent = window.SquarespaceAdUnblockConfig.messageText;
        
        banner.className = window.SquarespaceAdUnblockConfig.bannerPosition;
        banner.style.display = 'block';
        
        // Adjust body padding to accommodate banner
        if (window.SquarespaceAdUnblockConfig.bannerPosition === 'top') {
          document.body.style.paddingTop = '80px';
        } else {
          document.body.style.paddingBottom = '80px';
        }
      }
    };
    
    window.AdUnblock.onNotDetected = function() {
      document.getElementById('squarespace-adunblock-banner').style.display = 'none';
      document.body.style.paddingTop = '';
      document.body.style.paddingBottom = '';
    };
  } else {
    setTimeout(initSquarespaceAdUnblock, 100);
  }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', initSquarespaceAdUnblock);

// Also try to initialize immediately in case DOM is already loaded
if (document.readyState === 'loading') {
  document.addEventListener('DOMContentLoaded', initSquarespaceAdUnblock);
} else {
  initSquarespaceAdUnblock();
}
</script>
```

### Method 2: Code Block

#### Step 1: Add Code Block

1. Edit your page
2. Add a **Code Block** where you want the AdUnblock message
3. Add the following code:

```html
<div id="squarespace-adunblock-message" style="display: none;">
  <div style="background: #f8f9fa; border: 1px solid #dee2e6; padding: 20px; border-radius: 8px; text-align: center; margin: 20px 0;">
    <h3 style="margin: 0 0 10px 0; color: #495057;">Ad Blocker Detected</h3>
    <p style="margin: 0 0 15px 0; color: #6c757d;">Please disable your ad blocker to support our content.</p>
    <button onclick="squarespaceWhitelist()" style="background: #007bff; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer; margin-right: 10px;">
      Whitelist Site
    </button>
    <button onclick="squarespaceDismiss()" style="background: #6c757d; color: white; border: none; padding: 10px 20px; border-radius: 4px; cursor: pointer;">
      Continue
    </button>
  </div>
</div>

<script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID" async></script>

<script>
function squarespaceWhitelist() {
  alert('Please add our site to your ad blocker\'s whitelist and refresh the page.');
}

function squarespaceDismiss() {
  document.getElementById('squarespace-adunblock-message').style.display = 'none';
}

function initSquarespaceAdUnblock() {
  if (window.AdUnblock) {
    window.AdUnblock.onDetected = function(blocker) {
      document.getElementById('squarespace-adunblock-message').style.display = 'block';
    };
    
    window.AdUnblock.onNotDetected = function() {
      document.getElementById('squarespace-adunblock-message').style.display = 'none';
    };
  } else {
    setTimeout(initSquarespaceAdUnblock, 100);
  }
}

document.addEventListener('DOMContentLoaded', initSquarespaceAdUnblock);
</script>
```

## Advanced Customization

### Wix Dynamic Content

For Wix sites with dynamic content:

```javascript
// In Wix Velo, create a data collection for AdUnblock settings
import wixData from 'wix-data';

$w.onReady(function () {
  // Load AdUnblock settings from database
  wixData.query('AdUnblockSettings')
    .find()
    .then((results) => {
      const settings = results.items[0];
      
      // Initialize AdUnblock with dynamic settings
      initAdUnblock(settings);
    });
});

function initAdUnblock(settings) {
  const script = document.createElement('script');
  script.src = 'https://cdn.ad-unblock.com/sdk.js';
  script.setAttribute('data-site-id', settings.siteId);
  document.head.appendChild(script);
  
  script.onload = function() {
    window.AdUnblock.onDetected = function(blocker) {
      $w('#adunblockMessage').show();
      $w('#adunblockTitle').text = settings.messageTitle;
      $w('#adunblockText').text = settings.messageText;
    };
  };
}
```

### Squarespace Member Areas

For Squarespace member areas:

```javascript
// Check if user is logged in
if (window.Static && window.Static.SQUARESPACE_CONTEXT && window.Static.SQUARESPACE_CONTEXT.authenticatedAccount) {
  // User is logged in - different message
  window.SquarespaceAdUnblockConfig.messageTitle = 'Ad Blocker Detected - Member';
  window.SquarespaceAdUnblockConfig.messageText = 'As a member, please consider whitelisting our site to support our content.';
}
```

## Template-Specific Implementation

### Wix Template Integration

For specific Wix templates:

```javascript
// Detect template type and adjust accordingly
function detectWixTemplate() {
  const template = document.body.className;
  
  if (template.includes('blog')) {
    // Blog template - show inline message
    return 'blog';
  } else if (template.includes('store')) {
    // Store template - show banner
    return 'store';
  } else {
    // Default template
    return 'default';
  }
}

function initTemplateSpecificAdUnblock() {
  const templateType = detectWixTemplate();
  
  if (templateType === 'blog') {
    // Show inline message in blog posts
    showInlineBlogMessage();
  } else if (templateType === 'store') {
    // Show banner in store
    showStoreBanner();
  } else {
    // Default behavior
    showDefaultMessage();
  }
}
```

### Squarespace Template Integration

For Squarespace family templates:

```css
/* Squarespace Family-specific styles */
.squarespace-adunblock-bedford {
  font-family: 'Proxima Nova', sans-serif;
}

.squarespace-adunblock-avenue {
  font-family: 'Gotham A', 'Gotham B', sans-serif;
}

.squarespace-adunblock-pacific {
  font-family: 'Freight Text Pro', serif;
}
```

## Testing and Debugging

### Wix Testing

```javascript
// Wix debug mode
function debugWixAdUnblock() {
  console.log('Wix AdUnblock Debug:', {
    adUnblockLoaded: !!window.AdUnblock,
    config: window.WixAdUnblockConfig,
    template: detectWixTemplate()
  });
}

// Test in Wix preview mode
if (window.location.hostname.includes('wix.com')) {
  debugWixAdUnblock();
}
```

### Squarespace Testing

```javascript
// Squarespace debug mode
function debugSquarespaceAdUnblock() {
  console.log('Squarespace AdUnblock Debug:', {
    adUnblockLoaded: !!window.AdUnblock,
    config: window.SquarespaceAdUnblockConfig,
    context: window.Static ? window.Static.SQUARESPACE_CONTEXT : null
  });
}

// Test in Squarespace preview mode
if (window.location.hostname.includes('squarespace.com')) {
  debugSquarespaceAdUnblock();
}
```

## Best Practices

### 1. Performance
- Use async loading for all scripts
- Minimize DOM manipulation
- Cache settings when possible

### 2. User Experience
- Use platform-appropriate styling
- Provide clear value propositions
- Respect user dismissal preferences

### 3. Platform Compliance
- Follow Wix and Squarespace guidelines
- Avoid conflicts with platform features
- Test across different browsers

## Troubleshooting

### Common Issues

**Script not loading in Wix:**
- Check HTML embed placement
- Verify custom code is enabled
- Test in published site, not preview

**Banner not showing in Squarespace:**
- Check code injection is enabled
- Verify z-index values
- Test with different templates

**Mobile responsiveness:**
- Test on actual mobile devices
- Check viewport meta tags
- Verify touch interactions

## Next Steps

After implementing AdUnblock in Wix/Squarespace:

1. **Publish and test** your site thoroughly
2. **Monitor analytics** in your AdUnblock dashboard
3. **A/B test different messages** and placements
4. **Optimize based on user feedback**
5. **Consider platform-specific premium features**

For more advanced customization, check out our [API Reference](../api-reference/rest-api.md) guide. 