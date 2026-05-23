# Admin Graphql Api Complete Guide


---

## Graphql Basics And Auth

---
source: shopify.dev/docs/api/admin-graphql
version: 2024-10
topic: graphql_auth
---
# GraphQL Admin API: Basics & Authentication

## Endpoint
`https://{shop}.myshopify.com/admin/api/2024-10/graphql.json`

## Auth Methods
1. **Private App / Custom App:** `X-Shopify-Access-Token: shpat_xxx`
2. **OAuth App:** Bearer token from `shopify.app.toml` + Remix `authenticate.admin()`
3. **Admin API Access Tokens:** Scoped, time-limited (recommended)

## Example Request
```bash
curl -X POST "https://{shop}.myshopify.com/admin/api/2024-10/graphql.json" \
  -H "Content-Type: application/graphql" \
  -H "X-Shopify-Access-Token: {ACCESS_TOKEN}" \
  -d '{ products(first: 5) { edges { node { id title } } } }'
```

## Versioning
- Use `2024-10` in URL or `Shopify-Api-Version` header
- Breaking changes announced quarterly. Subscribe to `shopify.dev/changelog`

🔗 Reference: https://shopify.dev/docs/api/admin-graphql/latest



---

## Common Queries And Mutations

---
source: shopify.dev/docs/api/admin-graphql
version: 2024-10
topic: graphql_queries_mutations
---
# Common GraphQL Queries & Mutations

## Fetch Products
```graphql
query GetProducts($first: Int!, $cursor: String) {
  products(first: $first, after: $cursor) {
    edges {
      node { id title status variants(first: 5) { edges { node { id price sku } } } }
      cursor
    }
    pageInfo { hasNextPage }
  }
}
```

## Create Discount
```graphql
mutation CreateDiscountCode($code: String!, $value: Decimal!, $type: DiscountApplicationStrategy!) {
  discountAutomaticBasicCreate(input: {
    title: "Summer Sale",
    startsAt: "2024-06-01T00:00:00Z",
    endsAt: "2024-06-30T23:59:59Z",
    customerSelection: { all: true },
    discountType: { code: { code: $code, value: $value, appliesTo: $type } }
  }) {
    userErrors { field message }
    automaticBasicDiscount { id title }
  }
}
```

## Remix Integration
```ts
import { authenticate } from "../shopify.server";
const { admin } = await authenticate.admin(request);
const res = await admin.graphql(`{ products(first: 5) { edges { node { id } } } }`);
```

🔗 Reference: https://shopify.dev/docs/api/admin-graphql/latest/queries/products



---

## Pagination And Rate Limits

---
source: shopify.dev/docs/api/usage-limits
version: 2024-10
topic: graphql_rate_limits
---
# Pagination & Rate Limits (GraphQL)

## Pagination
- Use cursor-based pagination (`after`, `first`)
- `pageInfo.hasNextPage` determines next fetch
- Max `first` = 250 for most resources

## Rate Limit System
- **Points-based:** 1000 points per minute, replenished at 50/second
- Each field/mutation costs points (check `X-GraphQL-Cost`)
- Headers: `X-Shopify-Shop-Api-Call-Limit`, `X-Shopify-GraphQL-Cost`

## Retry Logic
```js
if (cost > 800) await new Promise(r => setTimeout(r, 1000));
if (response.errors?.some(e => e.extensions?.code === "THROTTLED")) {
  await new Promise(r => setTimeout(r, 2000));
}
```

## Best Practices
- Request only needed fields
- Use bulk operations for >10k records
- Monitor `X-GraphQL-Cost` in production

🔗 Reference: https://shopify.dev/docs/api/usage-limits



---

## Webhooks And Subscriptions

---
source: shopify.dev/docs/apps/webhooks
version: 2024-10
topic: webhooks
---
# Webhooks & Event Subscriptions

## Subscription Mutation
```graphql
mutation CreateWebhook($topic: WebhookSubscriptionTopic!, $callbackUrl: URL!) {
  webhookSubscriptionCreate(topic: $topic, webhookSubscription: { callbackUrl: $callbackUrl, format: JSON }) {
    userErrors { field message }
    webhookSubscription { id topic }
  }
}
```

## Common Topics
- `ORDERS_CREATE`, `ORDERS_UPDATED`, `PRODUCTS_UPDATE`, `CUSTOMERS_CREATE`
- `APP_UNINSTALLED` (critical for cleanup)

## Verification
- HMAC: `X-Shopify-Hmac-SHA256` header
- Compute: `HMAC-SHA256(secret, raw_body)`
- Respond `200 OK` within 5s or retry occurs

## CLI Setup
```bash
shopify app webhook register --topic orders/create --url https://your-app.com/webhooks/orders
```

🔗 Reference: https://shopify.dev/docs/apps/webhooks

