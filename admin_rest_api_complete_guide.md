# Admin Rest Api Complete Guide


---

## Rest Authentication And Scopes

---
source: shopify.dev/docs/apps/auth/oauth
version: 2024-10
topic: rest_auth
---
# REST Authentication & Scopes

## OAuth Flow
1. Redirect to `https://{shop}.myshopify.com/admin/oauth/authorize?client_id=...&scope=read_products&redirect_uri=...&state=...`
2. Exchange `code` for `access_token` at `/admin/oauth/access_token`
3. Store token securely. Refresh via OAuth if expired.

## Required Scopes
- `read_products`, `write_orders`, `read_customers`, `write_inventory`
- Use minimal scopes. Reject `read_all_orders` unless necessary.

## Headers
```bash
-H "X-Shopify-Access-Token: {token}"
-H "Content-Type: application/json"
```

## Security
- Validate `state` parameter against CSRF
- Store tokens encrypted. Rotate on uninstall.
- Never expose in client-side JS

🔗 Reference: https://shopify.dev/docs/apps/auth/oauth



---

## Rest Endpoints Overview

---
source: shopify.dev/docs/api/admin-rest
version: 2024-10
topic: rest_endpoints
---
# Admin REST API: Endpoints Overview

## Base URL
`https://{shop}.myshopify.com/admin/api/2024-10/`

## Common Endpoints
- `GET /admin/api/2024-10/products.json`
- `POST /admin/api/2024-10/orders.json`
- `PUT /admin/api/2024-10/customers/{id}.json`
- `DELETE /admin/api/2024-10/inventory_levels.json`

## Response Format
- JSON wrapped in resource key (`{ "product": { ... } }`)
- Includes `link` headers for pagination

## Deprecation Note
- Shopify recommends GraphQL for new development
- REST maintained for legacy compatibility
- Rate limits stricter than GraphQL (40 req/s burst)

🔗 Reference: https://shopify.dev/docs/api/admin-rest/latest



---

## Rest Pagination And Errors

---
source: shopify.dev/docs/api/admin-rest
version: 2024-10
topic: rest_pagination_errors
---
# REST Pagination & Error Handling

## Pagination
- `Link` header: `rel="next"` URL for next page
- Max 250 resources per page
- Example: `?limit=250&page_info=abc123`

## Error Codes
- `400`: Invalid JSON or missing required fields
- `401`: Invalid/missing token
- `403`: Missing scope
- `404`: Resource not found
- `422`: Validation error (check `errors` object)
- `429`: Rate limit exceeded

## Retry Strategy
```js
const retry = async (url, headers, retries = 3) => {
  for (let i = 0; i < retries; i++) {
    const res = await fetch(url, headers);
    if (res.status !== 429) return res;
    await new Promise(r => setTimeout(r, 1000 * (i + 1)));
  }
  throw new Error("Max retries exceeded");
};
```

🔗 Reference: https://shopify.dev/docs/api/admin-rest/latest/resources/product

