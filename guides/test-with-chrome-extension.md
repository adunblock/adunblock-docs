# Test With Chrome Extension

The AdUnblock Chrome Extension is a powerful testing tool that helps you validate your AdUnblock implementation and troubleshoot any issues.

## Installing the Chrome Extension

1. Visit the [Chrome Web Store](https://chrome.google.com/webstore) and search for "AdUnblock Testing Tool"
2. Click "Add to Chrome" to install the extension
3. Pin the extension to your toolbar for easy access

## Using the Extension

### Basic Testing

1. **Navigate to your website** where AdUnblock is installed
2. **Click the AdUnblock extension icon** in your browser toolbar
3. **View the status** - The extension will show:
   - ✅ AdUnblock detected and working
   - ❌ AdUnblock not detected
   - ⚠️ AdUnblock detected but with issues

### Detailed Analysis

The extension provides detailed information about your implementation:

#### Detection Status
- **Site ID**: Confirms your site ID is correctly configured
- **SDK Version**: Shows which version of the AdUnblock SDK is loaded
- **Load Time**: Displays how long the SDK took to initialize

#### Ad Blocker Simulation
- **Test Detection**: Simulate different ad blockers to test your setup
- **Response Testing**: Verify your custom messages and responses work correctly
- **Event Logging**: See all AdUnblock events in real-time

## Testing Scenarios

### 1. Basic Detection Test

**Steps:**
1. Install a popular ad blocker (uBlock Origin, AdBlock Plus, etc.)
2. Visit your website
3. Check the extension status
4. Verify detection is working

**Expected Result:** Extension shows "Ad blocker detected" status

### 2. Multi-Browser Testing

Test across different browsers and ad blockers:

**Chrome:**
- uBlock Origin
- AdBlock Plus  
- AdGuard

**Firefox:**
- uBlock Origin
- AdBlock Plus
- Ghostery

**Safari:**
- AdGuard for Safari
- 1Blocker

### 3. Mobile Testing

Use Chrome DevTools device simulation:
1. Open Chrome DevTools (F12)
2. Click the device toolbar icon
3. Select a mobile device
4. Test your implementation

## Troubleshooting Common Issues

### Extension Shows "Not Detected"

**Possible Causes:**
- AdUnblock SDK not loaded
- Incorrect site ID
- Script blocked by Content Security Policy

**Solutions:**
1. Verify the SDK script tag is present
2. Check your site ID in the dashboard
3. Review browser console for errors
4. Check CSP headers allow the AdUnblock domain

### Extension Shows "Issues Detected"

**Common Issues:**
- Outdated SDK version
- Configuration errors
- Network connectivity problems

**Debugging Steps:**
1. Check the browser console for error messages
2. Verify your internet connection
3. Try refreshing the page
4. Clear browser cache and cookies

### False Positives/Negatives

**False Positive (detects when no ad blocker is present):**
- Check for conflicting scripts
- Verify SDK configuration
- Review custom detection logic

**False Negative (doesn't detect existing ad blocker):**
- Update to latest SDK version
- Check ad blocker settings
- Verify detection method configuration

## Advanced Testing Features

### Event Monitoring

The extension can monitor all AdUnblock events:

```javascript
// Events you can monitor
window.AdUnblock.events = {
    'detected': function(data) { /* Ad blocker detected */ },
    'not_detected': function(data) { /* No ad blocker found */ },
    'error': function(data) { /* Error occurred */ }
};
```

### Performance Testing

Monitor performance impact:
- **Load Time**: Time to initialize AdUnblock
- **Memory Usage**: RAM consumption
- **Network Requests**: API calls made

### Custom Scenarios

Create custom test scenarios:
1. **A/B Testing**: Test different configurations
2. **Geolocation**: Test from different locations
3. **Device Types**: Test on various devices

## Validation Checklist

Use this checklist to ensure complete testing:

- [ ] Extension shows "Working" status
- [ ] Ad blocker detection works correctly
- [ ] Custom messages display properly
- [ ] No JavaScript errors in console
- [ ] Page load performance is acceptable
- [ ] Mobile devices work correctly
- [ ] Multiple ad blockers tested
- [ ] Analytics tracking is working

## Reporting Issues

If you discover issues during testing:

1. **Take screenshots** of the extension status
2. **Copy error messages** from the browser console
3. **Note your browser version** and operating system
4. **Document reproduction steps**
5. **Contact support** with all details

## Best Practices

### Regular Testing
- Test after any website changes
- Test monthly to catch issues early
- Test across different devices and browsers

### Documentation
- Keep a testing log
- Document known issues and solutions
- Share results with your team

### Automation
Consider automating tests for:
- Continuous integration pipelines
- Regular monitoring
- Performance regression testing

## Next Steps

Once your testing is complete:
1. Review the [Analytics Dashboard](https://ad-unblock.com/dashboard) for real-world data
2. Fine-tune your configuration based on test results
3. Set up monitoring for ongoing validation
4. Explore advanced features in our [API Reference](../api-reference/rest-api.md) 