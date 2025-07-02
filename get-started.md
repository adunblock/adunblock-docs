# Get Started

Get up and running with AdUnblock in just a few minutes. This guide will walk you through the basic setup process.

## Prerequisites

Before you begin, make sure you have:
- A website or web application
- Basic knowledge of HTML/JavaScript (for custom implementations)
- An active AdUnblock account (sign up at [ad-unblock.com](https://ad-unblock.com))

## Quick Setup Steps

### 1. Create Your Account

1. Visit [ad-unblock.com](https://ad-unblock.com) and sign up for an account
2. Verify your email address
3. Log in to your dashboard

### 2. Add Your Website

1. In your dashboard, click "Add Website"
2. Enter your website URL
3. Configure your basic settings
4. Copy your unique tracking code

### 3. Install the Code

Add the AdUnblock tracking code to your website. The installation method depends on your platform:

#### For HTML/JavaScript websites:
```html
<script src="https://cdn.ad-unblock.com/sdk.js" data-site-id="YOUR_SITE_ID"></script>
```

#### For WordPress:
Install our WordPress plugin or add the code to your theme.

#### For React/Next.js, Vue/Nuxt.js, Angular:
Use our framework-specific integration guides.

### 4. Test Your Implementation

1. Install an ad blocker (like uBlock Origin) in your browser
2. Visit your website
3. Verify that AdUnblock is detecting the ad blocker
4. Check your dashboard for tracking data

## Configuration Options

### Basic Settings
- **Site URL**: Your website's primary domain
- **Detection Method**: Choose how AdUnblock detects ad blockers
- **Response Type**: Configure what happens when ad blockers are detected

### Advanced Settings
- **Custom Messages**: Personalize messages shown to ad blocker users
- **Whitelist/Blacklist**: Control which pages AdUnblock is active on
- **Analytics**: Set up conversion tracking and revenue metrics

## Platform-Specific Guides

For detailed implementation instructions for your specific platform, check out our platform guides:

- [HTML/JavaScript](guides/html-javascript.md)
- [WordPress](guides/wordpress.md) 
- [React/Next.js](guides/react-nextjs.md)
- [Vue/Nuxt.js](guides/vue-nuxtjs.md)
- [Angular](guides/angular.md)
- [Shopify](guides/shopify.md)
- [Wix/Squarespace](guides/wix-squarespace.md)

## Next Steps

Once you've completed the basic setup:

1. **Test thoroughly** - Use the [Chrome Extension test](guides/test-with-chrome-extension.md)
2. **Customize your messages** - Make them align with your brand
3. **Monitor analytics** - Track your revenue recovery performance
4. **Optimize settings** - Fine-tune based on your analytics data

## Troubleshooting

If you encounter issues during setup:

1. Check the [FAQ](faq.md) for common solutions
2. Verify your tracking code is correctly installed
3. Ensure your website URL is correctly configured
4. Contact support if you need additional assistance

Ready to dive deeper? Explore our comprehensive [Guides](guides/README.md) section for advanced implementation strategies. 