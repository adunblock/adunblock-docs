# Guides

Welcome to the AdUnblock implementation guides! This section contains detailed instructions for integrating AdUnblock with various platforms and frameworks.

## Testing and Validation

Start here to ensure your AdUnblock implementation is working correctly:

- [Test With Chrome Extension](test-with-chrome-extension.md) - Validate your setup using our Chrome extension

## Platform-Specific Integration Guides

Choose the guide that matches your website or application platform:

### Web Development Frameworks

- [HTML/JavaScript](html-javascript.md) - Basic implementation with vanilla JavaScript
- [React/Next.js](react-nextjs.md) - Integration with React applications and Next.js
- [Vue/Nuxt.js](vue-nuxtjs.md) - Implementation for Vue.js and Nuxt.js applications  
- [Angular](angular.md) - Setup guide for Angular applications

### Content Management Systems

- [WordPress](wordpress.md) - Plugin installation and custom implementation
- [Wix/Squarespace](wix-squarespace.md) - Setup for website builders

### E-commerce Platforms

- [Shopify](shopify.md) - Integration with Shopify stores

## Implementation Best Practices

### General Guidelines

1. **Test thoroughly** - Always test your implementation with multiple ad blockers
2. **User experience first** - Ensure your messages are helpful, not aggressive
3. **Monitor performance** - Track both technical performance and revenue metrics
4. **Gradual rollout** - Consider implementing AdUnblock gradually across your site

### Common Integration Patterns

#### Async Loading
Always load AdUnblock asynchronously to avoid blocking your page load:

```javascript
(function() {
    const script = document.createElement('script');
    script.src = 'https://cdn.ad-unblock.com/sdk.js';
    script.async = true;
    script.setAttribute('data-site-id', 'YOUR_SITE_ID');
    document.head.appendChild(script);
})();
```

#### Conditional Loading
Load AdUnblock only on pages where you want ad blocker detection:

```javascript
if (shouldLoadAdUnblock()) {
    // Load AdUnblock SDK
}
```

### Performance Considerations

- **Minimal impact**: AdUnblock is designed to have minimal impact on page load times
- **CDN delivery**: Our SDK is delivered via global CDN for fast loading
- **Caching**: The SDK is cached aggressively to reduce repeat load times

## Advanced Configuration

### Custom Event Handling

```javascript
window.AdUnblock = window.AdUnblock || {};
window.AdUnblock.onDetected = function(blocker) {
    // Custom logic when ad blocker is detected
    console.log('Ad blocker detected:', blocker.name);
};
```

### API Integration

For advanced use cases, integrate with our REST API:
- [REST API Documentation](../api-reference/rest-api.md)

## Support and Troubleshooting

If you encounter issues with any of these guides:

1. Check the [FAQ](../faq.md) for common solutions
2. Review the [System Status](../system-status.md) page
3. Contact our support team for personalized assistance

## Contributing

Found an issue with our guides or have suggestions for improvement? We welcome feedback to make our documentation better for everyone. 