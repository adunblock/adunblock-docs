# Frequently Asked Questions (FAQ)

Find answers to the most commonly asked questions about AdUnblock.

## General Questions

### What is AdUnblock?

AdUnblock is a service that helps website owners detect when visitors are using ad blockers and provides tools to recover lost ad revenue. It offers accurate detection, customizable messaging, and detailed analytics to help you understand and respond to ad blocker usage on your site.

### How does AdUnblock work?

AdUnblock uses advanced detection techniques to identify when users have ad blockers enabled. When detected, it can display customizable messages asking users to whitelist your site, disable their ad blocker, or consider premium alternatives. The service provides real-time analytics to track ad blocker usage and revenue impact.

### Is AdUnblock compatible with my website?

AdUnblock works with virtually any website or web application. We provide integrations for:
- Vanilla HTML/JavaScript sites
- WordPress websites
- React and Next.js applications
- Vue.js and Nuxt.js projects
- Angular applications
- Shopify stores
- Wix and Squarespace sites

### How accurate is AdUnblock's detection?

AdUnblock maintains a 99.7% detection accuracy rate across 150+ different ad blockers. Our system is continuously updated to detect new ad blockers and browser extensions as they emerge.

## Technical Questions

### Will AdUnblock slow down my website?

No. AdUnblock is designed for minimal performance impact:
- The SDK is only ~15KB gzipped
- Loads asynchronously without blocking page render
- Served from a global CDN for fast loading
- Average load time is under 200ms

### Does AdUnblock work on mobile devices?

Yes, AdUnblock fully supports mobile detection including:
- Mobile browser ad blockers
- iOS content blockers
- Android ad blocking apps
- DNS-based ad blocking

### Can I customize the ad blocker messages?

Absolutely! AdUnblock provides full customization options:
- Custom titles and text
- Branded styling and colors
- Multiple display formats (modal, banner, inline)
- Customizable buttons and actions
- HTML and CSS customization

### Does AdUnblock support GDPR compliance?

Yes, AdUnblock is GDPR compliant:
- No personal data collection without consent
- Privacy-first detection methods
- EU data processing compliance
- Cookie-free operation available
- Privacy policy transparency

## Setup and Installation

### How long does it take to set up AdUnblock?

Setup typically takes 5-10 minutes:
1. Create your account (2 minutes)
2. Add your website (1 minute)
3. Install the tracking code (5 minutes)
4. Test your implementation (2 minutes)

### Do I need technical knowledge to install AdUnblock?

Basic HTML knowledge is helpful but not required. We provide:
- Simple copy-paste code snippets
- WordPress plugin for easy installation
- Platform-specific guides
- Technical support when needed

### Can I test AdUnblock before going live?

Yes! We recommend testing your implementation:
- Use our Chrome Extension testing tool
- Test with popular ad blockers (uBlock Origin, AdBlock Plus)
- Check the preview in your dashboard
- Use our sandbox environment for API testing

### What if I have multiple websites?

AdUnblock supports multiple sites:
- Manage all sites from one dashboard
- Individual analytics for each site
- Bulk configuration options
- Volume pricing for multiple sites

## Analytics and Reporting

### What analytics does AdUnblock provide?

AdUnblock offers comprehensive analytics:
- Ad blocker usage percentage
- Top ad blockers used by visitors
- Geographic breakdown
- Conversion impact analysis
- Revenue recovery metrics
- Custom event tracking

### How often is analytics data updated?

Analytics data is updated in real-time:
- Live visitor tracking
- Instant detection logging
- Dashboard updates every 30 seconds
- Historical data available

### Can I export my analytics data?

Yes, you can export data in multiple formats:
- CSV exports for spreadsheet analysis
- JSON data via API
- PDF reports for presentations
- Integration with Google Analytics

### How long is data retained?

Data retention depends on your plan:
- **Free Plan**: 30 days
- **Pro Plan**: 1 year
- **Enterprise Plan**: 2 years + custom retention

## Pricing and Plans

### Is there a free plan?

Yes! Our free plan includes:
- Up to 10,000 monthly detections
- Basic analytics
- Standard ad blocker messages
- Community support

### What's included in paid plans?

Paid plans offer additional features:
- **Pro Plan**: Unlimited detections, custom messaging, priority support
- **Enterprise Plan**: White-label options, SLA, dedicated support

### Can I upgrade or downgrade my plan?

Yes, you can change plans anytime:
- Instant upgrades with prorated billing
- Downgrades take effect at next billing cycle
- No long-term contracts required

### Do you offer discounts for non-profits?

Yes, we offer 50% discounts for:
- Registered non-profit organizations
- Educational institutions
- Open source projects

## Privacy and Security

### What data does AdUnblock collect?

AdUnblock collects minimal data:
- Ad blocker detection results
- Basic visitor analytics (anonymized)
- Performance metrics
- No personal information unless explicitly provided

### Is AdUnblock data secure?

Security is a top priority:
- All data encrypted in transit and at rest
- SOC 2 Type II compliant
- Regular security audits
- GDPR and CCPA compliant

### Can visitors opt out of tracking?

Yes, AdUnblock respects user privacy:
- Honors "Do Not Track" browser settings
- Provides opt-out mechanisms
- Cookie-free operation available
- Transparent privacy practices

### Where is data stored?

Data is stored securely:
- Primary servers in US and EU
- Edge caching globally
- GDPR-compliant EU data residency options
- Enterprise customers can choose regions

## Integration and Development

### Do you have an API?

Yes! AdUnblock provides a comprehensive REST API:
- Server-side ad blocker detection
- Analytics data access
- Site management
- Real-time webhooks
- Multiple programming language SDKs

### Can I integrate with my existing analytics?

Absolutely! AdUnblock integrates with:
- Google Analytics
- Adobe Analytics
- Mixpanel
- Custom analytics platforms via API
- Webhook notifications

### Is there a WordPress plugin?

Yes, we offer a WordPress plugin:
- Easy installation from WordPress.org
- Dashboard configuration
- Automatic updates
- Compatible with popular themes and plugins

### Do you support headless/JAMstack sites?

Yes, AdUnblock works great with:
- Gatsby
- Next.js
- Nuxt.js
- Gridsome
- Any static site generator

## Troubleshooting

### AdUnblock isn't detecting ad blockers

Common solutions:
1. Verify your site ID is correct
2. Check that the script is loading properly
3. Clear browser cache and test again
4. Ensure Content Security Policy allows AdUnblock
5. Test with different ad blockers

### The message isn't displaying

Check these items:
1. Verify JavaScript event listeners are set up
2. Check CSS z-index values
3. Ensure no JavaScript errors in console
4. Test with ad blocker actually enabled

### Analytics data seems incorrect

Possible causes and solutions:
1. Multiple tracking codes installed - remove duplicates
2. Ad blocker blocking analytics - this is expected
3. Time zone differences in reporting
4. Cached data - wait 5-10 minutes for updates

### My website layout is broken

Quick fixes:
1. Check for CSS conflicts with AdUnblock styles
2. Adjust z-index values if elements overlap
3. Test responsive design on mobile devices
4. Review any custom CSS modifications

## Support and Resources

### How can I get help?

Multiple support channels available:
- **Documentation**: Comprehensive guides and tutorials
- **Email Support**: support@ad-unblock.com
- **Live Chat**: Available during business hours
- **Community Forum**: Connect with other users
- **GitHub**: For technical issues and feature requests

### What are your support hours?

Support availability:
- **Free Plan**: Community support, email (48-hour response)
- **Pro Plan**: Email support (24-hour response)
- **Enterprise Plan**: Priority support (4-hour response) + phone support

### Do you offer implementation assistance?

Yes, we provide:
- Free setup guidance for all plans
- Pro plan includes 1-hour consultation
- Enterprise plan includes dedicated onboarding
- Custom implementation services available

### Where can I find code examples?

Code examples are available:
- Platform-specific guides in documentation
- GitHub repository with sample implementations
- Community-contributed examples
- Support team can provide custom examples

## Business Questions

### Can AdUnblock increase my revenue?

Many customers see significant improvements:
- 10-20% increase in ad revenue on average
- Higher user engagement from message interactions
- Improved subscription conversion rates
- Better understanding of ad blocker impact

### How do I measure ROI?

Track these metrics:
- Ad blocker detection rates
- Message interaction rates
- Whitelist conversion percentage
- Revenue recovery from ad blocker users
- Premium subscription conversions

### Is AdUnblock suitable for small websites?

Yes! AdUnblock works for sites of all sizes:
- Free plan perfect for small sites
- Scales automatically with traffic
- No minimum traffic requirements
- Same features regardless of site size

### What about user experience concerns?

AdUnblock prioritizes user experience:
- Non-intrusive message options
- Respectful, educational messaging
- Easy dismissal options
- Mobile-optimized designs
- A/B testing for optimal messaging

## Advanced Features

### Can I A/B test different messages?

Yes, A/B testing is available:
- Test different message formats
- Compare conversion rates
- Automatic traffic splitting
- Statistical significance tracking

### Does AdUnblock support multiple languages?

AdUnblock supports internationalization:
- Customizable messages in any language
- Right-to-left (RTL) language support
- Geographic targeting for localized messages
- Unicode character support

### Can I whitelist specific users?

Yes, you can exclude users:
- By IP address
- By user agent
- By geographic location
- By user role (for logged-in users)
- Custom exclusion rules

### Is white-label branding available?

White-label options available for Enterprise plans:
- Custom branding and logos
- Branded dashboard
- Custom domain options
- Remove AdUnblock branding from messages

---

## Still have questions?

If you didn't find your answer here:

- **Check our documentation**: Comprehensive guides for all platforms
- **Contact support**: support@ad-unblock.com
- **Join our community**: [Discord](https://discord.gg/adunblock)
- **Browse examples**: [GitHub](https://github.com/adunblock/examples)

We're here to help you succeed with AdUnblock! 