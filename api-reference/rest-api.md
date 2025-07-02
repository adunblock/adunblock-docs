# REST API Reference

Complete reference for the AdUnblock REST API endpoints.

## Base URL

```
https://api.ad-unblock.com/v1
```

## Authentication

All API requests require authentication using your API key:

```http
Authorization: Bearer YOUR_API_KEY
```

## Detection API

### POST /detect

Detect ad blockers using server-side analysis of user agent, headers, and other signals.

#### Request

```http
POST /detect
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
  "ip_address": "192.168.1.1",
  "headers": {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "Accept-Language": "en-US,en;q=0.5",
    "DNT": "1"
  },
  "site_id": "site_abc123"
}
```

#### Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `user_agent` | string | Yes | User agent string from the request |
| `ip_address` | string | No | Client IP address |
| `headers` | object | No | HTTP headers from the request |
| `site_id` | string | No | Site ID for tracking purposes |

#### Response

```json
{
  "success": true,
  "data": {
    "is_blocked": true,
    "blocker": {
      "name": "uBlock Origin",
      "type": "extension",
      "version": "1.46.0",
      "confidence": 0.95
    },
    "detection_method": "user_agent_analysis",
    "timestamp": "2024-01-01T00:00:00Z"
  },
  "meta": {
    "request_id": "req_abc123",
    "processing_time": 45
  }
}
```

### GET /detect/rules

Get current detection rules and patterns used by the system.

#### Request

```http
GET /detect/rules
Authorization: Bearer YOUR_API_KEY
```

#### Response

```json
{
  "success": true,
  "data": {
    "version": "2024.1.15",
    "rules": [
      {
        "id": "rule_001",
        "name": "uBlock Origin Detection",
        "pattern": ".*uBlock.*",
        "type": "user_agent",
        "confidence": 0.9
      }
    ],
    "last_updated": "2024-01-15T10:30:00Z"
  }
}
```

## Analytics API

### GET /analytics/summary

Get analytics summary for your sites.

#### Request

```http
GET /analytics/summary?site_id=site_abc123&period=7d
Authorization: Bearer YOUR_API_KEY
```

#### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `site_id` | string | No | Filter by specific site |
| `period` | string | No | Time period: 1d, 7d, 30d, 90d |
| `start_date` | string | No | Start date (YYYY-MM-DD) |
| `end_date` | string | No | End date (YYYY-MM-DD) |

#### Response

```json
{
  "success": true,
  "data": {
    "period": "7d",
    "total_visitors": 15420,
    "ad_blocker_users": 3085,
    "blocker_percentage": 20.0,
    "top_blockers": [
      {
        "name": "uBlock Origin",
        "count": 1542,
        "percentage": 50.0
      },
      {
        "name": "AdBlock Plus",
        "count": 926,
        "percentage": 30.0
      }
    ],
    "daily_breakdown": [
      {
        "date": "2024-01-01",
        "visitors": 2200,
        "blocked": 440,
        "percentage": 20.0
      }
    ]
  }
}
```

### GET /analytics/events

Retrieve detailed event data and logs.

#### Request

```http
GET /analytics/events?site_id=site_abc123&limit=100
Authorization: Bearer YOUR_API_KEY
```

#### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `site_id` | string | - | Filter by site ID |
| `event_type` | string | - | Filter by event type |
| `limit` | integer | 50 | Number of events to return (max 1000) |
| `offset` | integer | 0 | Pagination offset |
| `start_date` | string | - | Start date filter |
| `end_date` | string | - | End date filter |

#### Response

```json
{
  "success": true,
  "data": {
    "events": [
      {
        "id": "evt_abc123",
        "type": "ad_blocker_detected",
        "site_id": "site_abc123",
        "timestamp": "2024-01-01T12:30:00Z",
        "data": {
          "blocker": "uBlock Origin",
          "user_agent": "Mozilla/5.0...",
          "ip_address": "192.168.1.1",
          "page_url": "/products/123"
        }
      }
    ],
    "pagination": {
      "total": 5420,
      "limit": 100,
      "offset": 0,
      "has_more": true
    }
  }
}
```

### POST /analytics/track

Track custom events and conversions.

#### Request

```http
POST /analytics/track
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "site_id": "site_abc123",
  "event": "conversion",
  "properties": {
    "value": 29.99,
    "currency": "USD",
    "product_id": "prod_123",
    "category": "electronics"
  },
  "user_id": "user_456",
  "session_id": "sess_789"
}
```

#### Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `site_id` | string | Yes | Site identifier |
| `event` | string | Yes | Event name |
| `properties` | object | No | Event properties |
| `user_id` | string | No | User identifier |
| `session_id` | string | No | Session identifier |

#### Response

```json
{
  "success": true,
  "data": {
    "event_id": "evt_def456",
    "tracked_at": "2024-01-01T12:30:00Z"
  }
}
```

## Sites API

### GET /sites

List all registered sites in your account.

#### Request

```http
GET /sites
Authorization: Bearer YOUR_API_KEY
```

#### Response

```json
{
  "success": true,
  "data": {
    "sites": [
      {
        "id": "site_abc123",
        "name": "My Website",
        "domain": "example.com",
        "status": "active",
        "created_at": "2024-01-01T00:00:00Z",
        "settings": {
          "auto_detect": true,
          "show_message": true,
          "tracking_enabled": true
        }
      }
    ],
    "total": 1
  }
}
```

### POST /sites

Register a new site for ad blocker detection.

#### Request

```http
POST /sites
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "name": "My New Website",
  "domain": "newsite.com",
  "settings": {
    "auto_detect": true,
    "show_message": true,
    "message_title": "Ad Blocker Detected",
    "message_text": "Please disable your ad blocker"
  }
}
```

#### Parameters

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Site display name |
| `domain` | string | Yes | Site domain |
| `settings` | object | No | Site configuration |

#### Response

```json
{
  "success": true,
  "data": {
    "id": "site_def456",
    "name": "My New Website",
    "domain": "newsite.com",
    "status": "active",
    "created_at": "2024-01-01T12:30:00Z",
    "settings": {
      "auto_detect": true,
      "show_message": true,
      "message_title": "Ad Blocker Detected",
      "message_text": "Please disable your ad blocker"
    }
  }
}
```

### PUT /sites/{site_id}

Update site configuration and settings.

#### Request

```http
PUT /sites/site_abc123
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "name": "Updated Website Name",
  "settings": {
    "auto_detect": false,
    "show_message": true,
    "message_title": "Please Support Us"
  }
}
```

#### Response

```json
{
  "success": true,
  "data": {
    "id": "site_abc123",
    "name": "Updated Website Name",
    "domain": "example.com",
    "status": "active",
    "updated_at": "2024-01-01T12:35:00Z",
    "settings": {
      "auto_detect": false,
      "show_message": true,
      "message_title": "Please Support Us"
    }
  }
}
```

### DELETE /sites/{site_id}

Remove a site from your account.

#### Request

```http
DELETE /sites/site_abc123
Authorization: Bearer YOUR_API_KEY
```

#### Response

```json
{
  "success": true,
  "data": {
    "message": "Site successfully deleted"
  }
}
```

## Configuration API

### GET /config/{site_id}

Get configuration for a specific site.

#### Request

```http
GET /config/site_abc123
Authorization: Bearer YOUR_API_KEY
```

#### Response

```json
{
  "success": true,
  "data": {
    "site_id": "site_abc123",
    "detection": {
      "enabled": true,
      "method": "hybrid",
      "sensitivity": "medium"
    },
    "messaging": {
      "enabled": true,
      "type": "modal",
      "title": "Ad Blocker Detected",
      "message": "Please disable your ad blocker",
      "buttons": {
        "whitelist": true,
        "dismiss": true,
        "premium": false
      }
    },
    "analytics": {
      "enabled": true,
      "track_conversions": true,
      "custom_events": true
    }
  }
}
```

### PUT /config/{site_id}

Update configuration for a specific site.

#### Request

```http
PUT /config/site_abc123
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "detection": {
    "sensitivity": "high"
  },
  "messaging": {
    "title": "Support Our Content",
    "message": "Help us by whitelisting our site"
  }
}
```

#### Response

```json
{
  "success": true,
  "data": {
    "site_id": "site_abc123",
    "updated_at": "2024-01-01T12:40:00Z",
    "detection": {
      "enabled": true,
      "method": "hybrid",
      "sensitivity": "high"
    },
    "messaging": {
      "enabled": true,
      "type": "modal",
      "title": "Support Our Content",
      "message": "Help us by whitelisting our site"
    }
  }
}
```

## Webhooks API

### GET /webhooks

List all configured webhooks.

#### Request

```http
GET /webhooks
Authorization: Bearer YOUR_API_KEY
```

#### Response

```json
{
  "success": true,
  "data": {
    "webhooks": [
      {
        "id": "wh_abc123",
        "url": "https://example.com/webhook",
        "events": ["ad_blocker_detected", "conversion"],
        "status": "active",
        "created_at": "2024-01-01T00:00:00Z"
      }
    ]
  }
}
```

### POST /webhooks

Create a new webhook.

#### Request

```http
POST /webhooks
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "url": "https://example.com/webhook",
  "events": ["ad_blocker_detected"],
  "secret": "your_webhook_secret"
}
```

#### Response

```json
{
  "success": true,
  "data": {
    "id": "wh_def456",
    "url": "https://example.com/webhook",
    "events": ["ad_blocker_detected"],
    "status": "active",
    "created_at": "2024-01-01T12:45:00Z"
  }
}
```

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "invalid_request",
    "message": "The request is missing required parameters",
    "details": {
      "missing_fields": ["user_agent"],
      "provided_fields": ["ip_address"]
    }
  },
  "meta": {
    "timestamp": "2024-01-01T00:00:00Z",
    "request_id": "req_abc123"
  }
}
```

### Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `invalid_request` | 400 | Request is malformed or missing required parameters |
| `authentication_failed` | 401 | API key is missing or invalid |
| `forbidden` | 403 | Access denied for this resource |
| `not_found` | 404 | Requested resource does not exist |
| `rate_limited` | 429 | Rate limit exceeded |
| `internal_error` | 500 | Internal server error |
| `service_unavailable` | 503 | Service temporarily unavailable |

## Rate Limiting

### Rate Limit Headers

All responses include rate limit information:

```http
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1633024800
X-RateLimit-Window: 60
```

### Rate Limit Response

When rate limited, you'll receive:

```json
{
  "success": false,
  "error": {
    "code": "rate_limited",
    "message": "Rate limit exceeded. Try again in 60 seconds",
    "details": {
      "limit": 1000,
      "window": 60,
      "reset_at": "2024-01-01T12:46:00Z"
    }
  }
}
```

## Pagination

For endpoints that return lists, pagination is handled with query parameters:

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `limit` | integer | 50 | Number of items per page (max 1000) |
| `offset` | integer | 0 | Number of items to skip |

### Response

```json
{
  "success": true,
  "data": {
    "items": [...],
    "pagination": {
      "total": 1500,
      "limit": 50,
      "offset": 0,
      "has_more": true,
      "next_offset": 50
    }
  }
}
```

## SDK Integration Examples

### JavaScript/Node.js

```javascript
const AdUnblock = require('@adunblock/node');

const client = new AdUnblock('YOUR_API_KEY');

// Detect ad blocker
const detection = await client.detect({
  user_agent: req.headers['user-agent'],
  ip_address: req.ip
});

console.log('Ad blocker detected:', detection.is_blocked);
```

### Python

```python
import adunblock

client = adunblock.Client('YOUR_API_KEY')

# Get analytics summary
summary = client.analytics.summary(
    site_id='site_abc123',
    period='7d'
)

print(f"Blocker usage: {summary.blocker_percentage}%")
```

### PHP

```php
<?php
use AdUnblock\Client;

$client = new Client('YOUR_API_KEY');

// Track custom event
$client->analytics->track([
    'site_id' => 'site_abc123',
    'event' => 'purchase',
    'properties' => [
        'value' => 99.99,
        'currency' => 'USD'
    ]
]);
?>
```

## Testing

### Sandbox Environment

Use the sandbox for testing:

```
https://api-sandbox.ad-unblock.com/v1
```

### Test Data

The sandbox includes test data:
- Simulated ad blocker detections
- Sample analytics data
- Test webhook events

### Example Test

```bash
curl -X POST https://api-sandbox.ad-unblock.com/v1/detect \
  -H "Authorization: Bearer test_key_123" \
  -H "Content-Type: application/json" \
  -d '{
    "user_agent": "Mozilla/5.0 (test-ublock-origin)",
    "ip_address": "127.0.0.1"
  }'
```

For more examples and integration guides, visit our [main documentation](../README.md) or explore our [platform-specific guides](../guides/README.md). 