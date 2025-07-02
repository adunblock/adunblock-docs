# API Reference

Welcome to the AdUnblock API documentation. Our APIs provide programmatic access to AdUnblock's ad blocker detection and analytics capabilities.

## Overview

The AdUnblock API enables you to:

- **Detect ad blockers** server-side
- **Track and analyze** ad blocker usage
- **Manage your account** and sites
- **Access detailed analytics** and reporting
- **Configure detection rules** and responses

## Authentication

All API requests require authentication using your API key. Include your API key in the request header:

```http
Authorization: Bearer YOUR_API_KEY
```

Get your API key from the [AdUnblock Dashboard](https://ad-unblock.com/dashboard).

## Base URL

All API endpoints are available at:

```
https://api.ad-unblock.com/v1
```

## API Versions

| Version | Status | Documentation |
|---------|--------|---------------|
| v1 | ✅ Current | This documentation |
| v2 | 🚧 Beta | [v2 Preview](https://docs.ad-unblock.com/api/v2) |

## Rate Limits

API requests are rate limited to ensure fair usage:

| Plan | Requests per minute | Burst limit |
|------|-------------------|-------------|
| Free | 100 | 200 |
| Pro | 1,000 | 2,000 |
| Enterprise | 10,000 | 20,000 |

Rate limit headers are included in all responses:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1633024800
```

## API Endpoints

### Detection API

#### POST /detect
Detect ad blockers server-side using user agent and other signals.

```http
POST /detect
Content-Type: application/json

{
  "user_agent": "Mozilla/5.0...",
  "ip_address": "192.168.1.1",
  "headers": {
    "Accept": "text/html,application/xhtml+xml..."
  }
}
```

#### GET /detect/rules
Get current detection rules and patterns.

### Analytics API

#### GET /analytics/summary
Get analytics summary for your sites.

#### GET /analytics/events
Retrieve detailed event data and logs.

#### POST /analytics/track
Track custom events and conversions.

### Sites API

#### GET /sites
List all your registered sites.

#### POST /sites
Register a new site for ad blocker detection.

#### PUT /sites/{site_id}
Update site configuration and settings.

#### DELETE /sites/{site_id}
Remove a site from your account.

### Configuration API

#### GET /config/{site_id}
Get configuration for a specific site.

#### PUT /config/{site_id}
Update configuration for a specific site.

## SDK Integration

### JavaScript SDK

```javascript
// Initialize the SDK
AdUnblock.init('YOUR_SITE_ID', {
  apiEndpoint: 'https://api.ad-unblock.com/v1',
  debug: false
});

// Track events
AdUnblock.track('page_view', {
  page: '/product/123',
  category: 'ecommerce'
});
```

### Server-Side SDKs

We provide SDKs for popular programming languages:

- **Node.js**: `npm install @adunblock/node`
- **Python**: `pip install adunblock`
- **PHP**: `composer require adunblock/php-sdk`
- **Ruby**: `gem install adunblock`
- **Go**: `go get github.com/adunblock/go-sdk`

## Response Formats

All API responses use JSON format:

```json
{
  "success": true,
  "data": {
    // Response data here
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_abc123"
  }
}
```

### Error Responses

Error responses include detailed information:

```json
{
  "success": false,
  "error": {
    "code": "invalid_request",
    "message": "The request is missing required parameters",
    "details": {
      "missing_fields": ["user_agent"]
    }
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_abc123"
  }
}
```

## HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 429 | Rate Limited |
| 500 | Internal Server Error |

## Webhooks

Configure webhooks to receive real-time notifications:

```json
{
  "event": "ad_blocker_detected",
  "data": {
    "site_id": "site_abc123",
    "blocker": {
      "name": "uBlock Origin",
      "type": "extension"
    },
    "timestamp": "2024-01-01T00:00:00Z"
  }
}
```

Webhook events:
- `ad_blocker_detected`
- `site_created`
- `site_updated`
- `analytics_threshold_reached`

## Testing

### Sandbox Environment

Test your integration using our sandbox:

```
https://api-sandbox.ad-unblock.com/v1
```

Sandbox features:
- Same API interface as production
- Test data and scenarios
- No rate limits
- Free to use

### Test Ad Blocker

Use our test endpoints to simulate ad blocker detection:

```http
POST /test/simulate-blocker
{
  "blocker_type": "ublock",
  "site_id": "your_site_id"
}
```

## Code Examples

### Detect Ad Blocker (cURL)

```bash
curl -X POST https://api.ad-unblock.com/v1/detect \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
    "ip_address": "192.168.1.1"
  }'
```

### Get Analytics (Python)

```python
import requests

headers = {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
}

response = requests.get(
    'https://api.ad-unblock.com/v1/analytics/summary',
    headers=headers,
    params={'site_id': 'your_site_id', 'period': '7d'}
)

data = response.json()
print(f"Ad blocker usage: {data['data']['blocker_percentage']}%")
```

### Track Event (JavaScript)

```javascript
fetch('https://api.ad-unblock.com/v1/analytics/track', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    site_id: 'your_site_id',
    event: 'conversion',
    properties: {
      value: 29.99,
      currency: 'USD'
    }
  })
});
```

## Best Practices

### API Usage

1. **Cache responses** when possible to reduce API calls
2. **Handle rate limits** gracefully with exponential backoff
3. **Use appropriate timeouts** (recommended: 5-10 seconds)
4. **Validate input data** before sending requests
5. **Monitor API usage** in your dashboard

### Security

1. **Keep API keys secure** - never expose in client-side code
2. **Use HTTPS only** for all API requests
3. **Implement proper error handling** to avoid exposing sensitive data
4. **Rotate API keys regularly** for enhanced security
5. **Use webhook signatures** to verify webhook authenticity

### Performance

1. **Batch requests** when possible
2. **Use appropriate HTTP methods** (GET for retrieval, POST for creation)
3. **Implement client-side caching** for frequently accessed data
4. **Monitor response times** and optimize accordingly
5. **Use CDN** for static resources

## Support

### Documentation

- **[REST API Reference](rest-api.md)** - Detailed endpoint documentation
- **[SDK Documentation](https://docs.ad-unblock.com/sdks)** - Language-specific guides
- **[Integration Examples](https://github.com/adunblock/examples)** - Code samples

### Getting Help

- **Documentation**: This API reference
- **Support Email**: api-support@ad-unblock.com
- **Community**: [Discord](https://discord.gg/adunblock)
- **GitHub**: [Issues](https://github.com/adunblock/api-issues)

### SLA

- **Uptime**: 99.9% guaranteed
- **Response Time**: < 100ms (95th percentile)
- **Support Response**: < 24 hours

## Changelog

### v1.3.0 - November 2024
- Added webhook signature verification
- Improved detection accuracy for mobile browsers
- New analytics aggregation endpoints

### v1.2.0 - October 2024
- Added batch detection endpoint
- Enhanced error responses with more details
- Performance improvements

### v1.1.0 - September 2024
- Added support for custom detection rules
- New analytics filtering options
- Rate limit increases for Pro plans

See the full [API Changelog](https://docs.ad-unblock.com/api/changelog) for complete version history. 