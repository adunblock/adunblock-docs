# System Status

Real-time status of AdUnblock services and infrastructure.

## Current Status

🟢 **All Systems Operational**

Last updated: Real-time

## Service Status

### Core Services

| Service | Status | Uptime | Response Time |
|---------|--------|---------|---------------|
| AdUnblock SDK | 🟢 Operational | 99.98% | 45ms |
| Detection Engine | 🟢 Operational | 99.97% | 12ms |
| Analytics API | 🟢 Operational | 99.95% | 78ms |
| Dashboard | 🟢 Operational | 99.99% | 156ms |

### CDN & Infrastructure

| Component | Status | Uptime | Response Time |
|-----------|--------|---------|---------------|
| Global CDN | 🟢 Operational | 99.99% | 23ms |
| US East | 🟢 Operational | 100% | 18ms |
| US West | 🟢 Operational | 99.98% | 21ms |
| Europe | 🟢 Operational | 99.97% | 28ms |
| Asia Pacific | 🟢 Operational | 99.96% | 34ms |

### Third-Party Integrations

| Integration | Status | Last Check |
|-------------|--------|------------|
| WordPress Plugin | 🟢 Operational | 2 minutes ago |
| Shopify App | 🟢 Operational | 1 minute ago |
| Chrome Extension | 🟢 Operational | 30 seconds ago |

## Recent Incidents

### Resolved

**November 15, 2024** - *Brief API Latency*
- **Duration**: 14:32 - 14:47 UTC (15 minutes)
- **Impact**: Increased response times for Analytics API
- **Resolution**: Load balancer optimization completed
- **Status**: Fully resolved

**November 8, 2024** - *CDN Cache Refresh*
- **Duration**: 09:15 - 09:22 UTC (7 minutes)
- **Impact**: Temporary SDK loading delays in APAC region
- **Resolution**: Cache warming completed
- **Status**: Fully resolved

## Maintenance Windows

### Scheduled

No scheduled maintenance at this time.

### Recent

**November 12, 2024** - *Database Optimization*
- **Duration**: 02:00 - 02:30 UTC (30 minutes)
- **Impact**: Brief read-only mode for dashboard
- **Status**: Completed successfully

## Performance Metrics

### SDK Performance (Last 24 Hours)

- **Average Load Time**: 127ms
- **99th Percentile**: 245ms
- **Error Rate**: 0.02%
- **Cache Hit Rate**: 97.8%

### Detection Accuracy

- **Overall Accuracy**: 99.7%
- **False Positive Rate**: 0.1%
- **False Negative Rate**: 0.2%
- **Coverage**: 150+ ad blockers

### API Performance

| Endpoint | Avg Response | 99th Percentile | Success Rate |
|----------|--------------|-----------------|--------------|
| /detect | 34ms | 78ms | 99.98% |
| /track | 12ms | 45ms | 99.99% |
| /analytics | 89ms | 234ms | 99.95% |
| /config | 23ms | 67ms | 100% |

## Geographic Performance

### Response Times by Region

| Region | Average | 95th Percentile |
|--------|---------|-----------------|
| North America | 45ms | 89ms |
| Europe | 52ms | 98ms |
| Asia Pacific | 67ms | 134ms |
| South America | 78ms | 156ms |
| Africa | 89ms | 178ms |

## Service Level Objectives (SLO)

### Availability Targets

- **SDK Availability**: 99.9% uptime
- **API Availability**: 99.9% uptime
- **Dashboard Availability**: 99.5% uptime

### Performance Targets

- **SDK Load Time**: < 200ms (95th percentile)
- **API Response Time**: < 100ms (95th percentile)
- **Detection Accuracy**: > 99.5%

### Current SLO Compliance

✅ **SDK Availability**: 99.98% (Target: 99.9%)
✅ **API Availability**: 99.96% (Target: 99.9%)
✅ **Dashboard Availability**: 99.99% (Target: 99.5%)
✅ **SDK Load Time**: 127ms (Target: < 200ms)
✅ **API Response Time**: 67ms (Target: < 100ms)
✅ **Detection Accuracy**: 99.7% (Target: > 99.5%)

## Monitoring & Alerting

### Real-Time Monitoring

We continuously monitor our systems using:

- **Synthetic Monitoring**: Proactive testing from 25+ global locations
- **Real User Monitoring**: Performance data from actual user interactions
- **Infrastructure Monitoring**: Server health, database performance, CDN status
- **Application Performance Monitoring**: Code-level insights and error tracking

### Alert Thresholds

| Metric | Warning | Critical |
|--------|---------|----------|
| API Response Time | > 200ms | > 500ms |
| Error Rate | > 1% | > 5% |
| CPU Usage | > 70% | > 90% |
| Memory Usage | > 80% | > 95% |

## Data Center Locations

### Primary Data Centers

- **US East (Virginia)** - Primary
- **US West (Oregon)** - Primary
- **Europe (Frankfurt)** - Primary
- **Asia Pacific (Singapore)** - Primary

### Edge Locations

AdUnblock utilizes a global CDN with 200+ edge locations worldwide for optimal performance.

## Status Page Information

### How We Calculate Uptime

Uptime is calculated based on successful health checks performed every 30 seconds from multiple geographic locations. A service is considered "down" if it fails health checks from 3 or more locations simultaneously.

### Incident Classification

- **🟢 Operational**: All systems functioning normally
- **🟡 Degraded Performance**: Some users may experience slower response times
- **🟠 Partial Outage**: Some functionality unavailable
- **🔴 Major Outage**: Service completely unavailable

### Notification Preferences

Subscribe to status updates:
- **Email Notifications**: Get alerts for incidents and maintenance
- **SMS Alerts**: Critical incidents only
- **Webhook Integration**: Integrate with your monitoring tools
- **RSS Feed**: Machine-readable status updates

[Subscribe to Status Updates](https://status.ad-unblock.com/subscribe)

## Historical Data

### Uptime History (Last 90 Days)

| Month | SDK Uptime | API Uptime | Overall Incidents |
|-------|------------|------------|-------------------|
| November 2024 | 99.98% | 99.96% | 2 |
| October 2024 | 99.97% | 99.95% | 3 |
| September 2024 | 99.99% | 99.98% | 1 |

### Incident Trends

- **Total Incidents (90 days)**: 6
- **Average Resolution Time**: 12 minutes
- **Planned Maintenance**: 3 events
- **Unplanned Outages**: 3 events

## Contact Information

### Status Page Support

If you have questions about this status page or need to report an issue:

- **Status Page**: [status.ad-unblock.com](https://status.ad-unblock.com)
- **Support Email**: status@ad-unblock.com
- **Twitter**: [@AdUnblockStatus](https://twitter.com/AdUnblockStatus)

### Emergency Contact

For critical issues affecting your implementation:

- **Priority Support**: support@ad-unblock.com
- **Response Time**: < 30 minutes for critical issues
- **Available**: 24/7 for enterprise customers

## Transparency Report

We believe in transparency about our service performance. This status page is updated automatically by our monitoring systems and provides real-time visibility into our infrastructure health.

### Data Retention

- **Real-time metrics**: Last 24 hours
- **Daily aggregates**: Last 90 days
- **Monthly summaries**: Last 24 months
- **Incident history**: Permanent record

### Third-Party Validation

Our uptime and performance metrics are validated by:
- External monitoring services
- Third-party performance testing
- Customer feedback and reports

---

*This page is updated automatically. Last refresh: [Real-time]*

For technical documentation, visit our [API Reference](api-reference/rest-api.md) or check our [FAQ](faq.md) for common questions. 