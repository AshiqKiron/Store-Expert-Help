# Security And Best Practices Complete Guide


---

## Auth Oauth And Sessions

---
source: shopify.dev/docs/apps/auth
version: 2024-10
topic: oauth_security
---
# OAuth & Session Security

## Flow
1. Redirect to Shopify authorization URL
2. Verify `state` matches stored value
3. Exchange `code` for access token
4. Store token securely (encrypted DB or secret manager)
5. Refresh before expiration

## Session Management
- Use `@shopify/shopify-app-session-storage-*`
- Never store tokens in localStorage or cookies without `Secure`, `HttpOnly`, `SameSite=Strict`
- Rotate on uninstall or scope change

## CSRF & XSS Prevention
- Validate `state` parameter
- Sanitize user input before rendering
- Use Polaris components for admin UI
- Disable `eval()` and inline scripts

🔗 Reference: https://shopify.dev/docs/apps/auth/oauth


---

## Api Rate Limits And Retry Logic

---
source: shopify.dev/docs/api/usage-limits
version: 2024-10
topic: retry_logic
---
# Rate Limits & Retry Logic

## Headers
- `X-Shopify-Shop-Api-Call-Limit`: `40/40` (REST)
- `X-GraphQL-Cost`: `12/1000`
- `Retry-After`: Seconds to wait (429 response)

## Exponential Backoff
```js
async function withRetry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try { return await fn(); }
    catch (e) {
      if (e.status !== 429 || i === maxRetries - 1) throw e;
      await new Promise(r => setTimeout(r, 1000 * Math.pow(2, i)));
    }
  }
}
```

## Optimization
- Batch requests where possible
- Cache responses with TTL
- Use bulk operations for large datasets
- Monitor limits in production dashboard

🔗 Reference: https://shopify.dev/docs/api/usage-limits


---

## Data Privacy And Compliance

---
source: shopify.dev/docs/apps/data-privacy
version: 2024-10
topic: privacy_compliance
---
# Data Privacy & Compliance

## Regulations
- GDPR (EU), CCPA (California), PIPEDA (Canada)
- Shopify acts as data processor; app is data controller for collected data

## Requirements
- Provide data access/deletion webhooks
- Store only necessary customer data
- Encrypt PII at rest and in transit
- Publish privacy policy with data retention details

## Webhooks
- `customers/data_request`
- `customers/redact`
- `shop/redact`
- Must respond within 30 days

## Audit Trail
- Log data access events
- Implement role-based access control
- Document data flow architecture

🔗 Reference: https://shopify.dev/docs/apps/data-privacy
