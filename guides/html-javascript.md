# HTML/JavaScript Implementation

This guide covers implementing AdUnblock in vanilla HTML and JavaScript websites without any frameworks.

## Basic Implementation

### Step 1: Add the Script Tag

Add the AdUnblock script to your HTML pages, preferably in the `<head>` section:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Your Website</title>
    
    <!-- AdUnblock SDK -->
    <script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID"></script>
</head>
<body>
    <!-- Your website content -->
</body>
</html>
```

### Step 2: Replace YOUR_SITE_ID

Replace `YOUR_SITE_ID` with your actual site ID from the AdUnblock dashboard:

```html
<script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="abc123def456"></script>
```

## Advanced Configuration

### Asynchronous Loading

For better performance, load AdUnblock asynchronously:

```html
<script>
(function() {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', 'YOUR_SITE_ID');
    document.head.appendChild(script);
})();
</script>
```

### Custom Configuration

Configure AdUnblock behavior with additional data attributes:

```html
<script 
    src="https://cdn.ad-unblock.com/sdk.js" 
    data-site-id="YOUR_SITE_ID"
    data-auto-detect="true"
    data-show-message="true"
    data-message-delay="2000"
    data-debug="false">
</script>
```

#### Configuration Options

| Attribute | Default | Description |
|-----------|---------|-------------|
| `data-auto-detect` | `true` | Automatically detect ad blockers |
| `data-show-message` | `true` | Show default message to ad blocker users |
| `data-message-delay` | `1000` | Delay before showing message (ms) |
| `data-debug` | `false` | Enable debug logging |

## Event Handling

### Basic Event Listeners

Listen for AdUnblock events:

```javascript
// Wait for AdUnblock to load
window.addEventListener('load', function() {
    if (window.AdUnblock) {
        // Ad blocker detected
        window.AdUnblock.onDetected = function(blocker) {
            console.log('Ad blocker detected:', blocker.name);
            showCustomMessage();
        };
        
        // No ad blocker detected
        window.AdUnblock.onNotDetected = function() {
            console.log('No ad blocker detected');
            showAds();
        };
        
        // Error occurred
        window.AdUnblock.onError = function(error) {
            console.error('AdUnblock error:', error);
        };
    }
});
```

### Advanced Event Handling

More sophisticated event handling:

```javascript
class AdBlockerHandler {
    constructor() {
        this.init();
    }
    
    init() {
        // Check if AdUnblock is loaded
        if (window.AdUnblock) {
            this.setupEventListeners();
        } else {
            // Wait for AdUnblock to load
            window.addEventListener('adunblock:loaded', () => {
                this.setupEventListeners();
            });
        }
    }
    
    setupEventListeners() {
        window.AdUnblock.onDetected = (blocker) => {
            this.handleAdBlockerDetected(blocker);
        };
        
        window.AdUnblock.onNotDetected = () => {
            this.handleNoAdBlocker();
        };
        
        window.AdUnblock.onError = (error) => {
            this.handleError(error);
        };
    }
    
    handleAdBlockerDetected(blocker) {
        // Custom logic for ad blocker users
        this.showAlternativeContent();
        this.trackAdBlockerUsage(blocker);
    }
    
    handleNoAdBlocker() {
        // Show regular ads
        this.loadAdvertisements();
    }
    
    handleError(error) {
        // Handle errors gracefully
        console.error('AdUnblock error:', error);
        // Fallback to showing regular content
        this.loadAdvertisements();
    }
    
    showAlternativeContent() {
        const adSlots = document.querySelectorAll('.ad-slot');
        adSlots.forEach(slot => {
            slot.innerHTML = `
                <div class="alternative-content">
                    <h3>Support Our Site</h3>
                    <p>We notice you're using an ad blocker. Please consider supporting us by:</p>
                    <ul>
                        <li>Whitelisting our site</li>
                        <li>Subscribing to our premium content</li>
                        <li>Making a donation</li>
                    </ul>
                </div>
            `;
        });
    }
    
    loadAdvertisements() {
        // Load your regular advertisements
        const adSlots = document.querySelectorAll('.ad-slot');
        adSlots.forEach(slot => {
            // Your ad loading logic here
            this.loadAd(slot);
        });
    }
    
    trackAdBlockerUsage(blocker) {
        // Send analytics data
        if (window.gtag) {
            gtag('event', 'ad_blocker_detected', {
                'blocker_name': blocker.name,
                'page_url': window.location.href
            });
        }
    }
    
    loadAd(container) {
        // Your ad loading implementation
        container.innerHTML = '<div class="advertisement">Ad content here</div>';
    }
}

// Initialize when DOM is ready
document.addEventListener('DOMContentLoaded', function() {
    new AdBlockerHandler();
});
```

## Custom Messages

### HTML Template for Custom Messages

Create a custom message template:

```html
<div id="adblock-message" style="display: none;">
    <div class="adblock-overlay">
        <div class="adblock-modal">
            <h2>We Notice You're Using an Ad Blocker</h2>
            <p>Our site relies on advertising to provide free content. Please consider:</p>
            <ul>
                <li>Adding us to your ad blocker's whitelist</li>
                <li>Subscribing to our premium ad-free experience</li>
                <li>Supporting us with a small donation</li>
            </ul>
            <div class="adblock-actions">
                <button onclick="whitelistSite()">Whitelist Site</button>
                <button onclick="subscribePremium()">Go Premium</button>
                <button onclick="dismissMessage()">Continue Anyway</button>
            </div>
        </div>
    </div>
</div>
```

### CSS for Custom Messages

Style your custom message:

```css
.adblock-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.8);
    z-index: 10000;
    display: flex;
    align-items: center;
    justify-content: center;
}

.adblock-modal {
    background: white;
    padding: 30px;
    border-radius: 10px;
    max-width: 500px;
    text-align: center;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.adblock-modal h2 {
    color: #333;
    margin-bottom: 15px;
}

.adblock-modal p {
    color: #666;
    margin-bottom: 20px;
}

.adblock-modal ul {
    text-align: left;
    margin-bottom: 25px;
}

.adblock-actions button {
    margin: 5px;
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 14px;
}

.adblock-actions button:first-child {
    background: #007cba;
    color: white;
}

.adblock-actions button:nth-child(2) {
    background: #28a745;
    color: white;
}

.adblock-actions button:last-child {
    background: #6c757d;
    color: white;
}
```

### JavaScript for Message Control

Control when and how messages are displayed:

```javascript
function showCustomMessage() {
    const message = document.getElementById('adblock-message');
    if (message) {
        message.style.display = 'block';
        
        // Track message shown
        if (window.AdUnblock && window.AdUnblock.track) {
            window.AdUnblock.track('message_shown');
        }
    }
}

function whitelistSite() {
    // Track whitelist click
    if (window.AdUnblock && window.AdUnblock.track) {
        window.AdUnblock.track('whitelist_clicked');
    }
    
    // Show instructions for whitelisting
    alert('Please add our site to your ad blocker\'s whitelist and refresh the page.');
}

function subscribePremium() {
    // Track premium click
    if (window.AdUnblock && window.AdUnblock.track) {
        window.AdUnblock.track('premium_clicked');
    }
    
    // Redirect to premium signup
    window.location.href = '/premium';
}

function dismissMessage() {
    const message = document.getElementById('adblock-message');
    if (message) {
        message.style.display = 'none';
        
        // Track message dismissed
        if (window.AdUnblock && window.AdUnblock.track) {
            window.AdUnblock.track('message_dismissed');
        }
        
        // Set cookie to remember dismissal
        document.cookie = 'adblock_message_dismissed=true; max-age=86400; path=/';
    }
}
```

## Performance Optimization

### Lazy Loading

Load AdUnblock only when needed:

```javascript
// Load AdUnblock only on pages with ads
if (document.querySelector('.ad-slot')) {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', 'YOUR_SITE_ID');
    document.head.appendChild(script);
}
```

### Conditional Loading

Load based on user behavior:

```javascript
// Load after user interaction
let userInteracted = false;

function loadAdUnblock() {
    if (!userInteracted) {
        userInteracted = true;
        const script = document.createElement('script');
        script.src = 'https://cdn.ad-unblock.com/sdk.js';
        script.async = true;
        script.setAttribute('data-site-id', 'YOUR_SITE_ID');
        document.head.appendChild(script);
    }
}

// Load on first user interaction
document.addEventListener('click', loadAdUnblock, { once: true });
document.addEventListener('scroll', loadAdUnblock, { once: true });
document.addEventListener('keydown', loadAdUnblock, { once: true });
```

## Testing Your Implementation

### Manual Testing

1. **Install an ad blocker** (uBlock Origin recommended)
2. **Visit your website**
3. **Check browser console** for any errors
4. **Verify detection** is working as expected
5. **Test custom messages** are displaying correctly

### Debug Mode

Enable debug mode for testing:

```html
<script 
    src="https://cdn.ad-unblock.com/sdk.js" 
    data-site-id="YOUR_SITE_ID"
    data-debug="true">
</script>
```

### Console Testing

Test AdUnblock methods in the browser console:

```javascript
// Check if AdUnblock is loaded
console.log(window.AdUnblock);

// Manually trigger detection
window.AdUnblock.detect();

// Check current status
console.log(window.AdUnblock.isBlocked());

// View configuration
console.log(window.AdUnblock.config);
```

## Troubleshooting

### Common Issues

**AdUnblock not loading:**
- Check your site ID is correct
- Verify script URL is accessible
- Check for Content Security Policy restrictions

**Detection not working:**
- Clear browser cache
- Try different ad blockers
- Check for JavaScript errors
- Verify event listeners are set up correctly

**Custom messages not showing:**
- Check HTML structure
- Verify CSS is loaded
- Ensure JavaScript functions are defined
- Check for DOM ready events

### Debug Checklist

- [ ] Script tag is present in HTML
- [ ] Site ID is correct
- [ ] No JavaScript errors in console
- [ ] Event listeners are properly set up
- [ ] Custom messages are properly configured
- [ ] CSS styles are loading correctly
- [ ] Testing with actual ad blockers

## Next Steps

After implementing AdUnblock:

1. **Monitor analytics** in your dashboard
2. **A/B test** different messages and approaches
3. **Optimize performance** based on real-world usage
4. **Explore advanced features** in our [API Reference](../api-reference/rest-api.md)

For more testing tools, check out our [Chrome Extension Testing Guide](test-with-chrome-extension.md). 