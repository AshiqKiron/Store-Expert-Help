# Storefront Checkout And Functions Complete Guide


---

## Storefront Api And Buy Button

---
source: shopify.dev/docs/api/storefront
version: 2024-10
topic: storefront_api
---
# Storefront API & Buy Button

## Endpoint
`https://{shop}.myshopify.com/api/2024-10/graphql.json`

## Public Access
- Use Storefront Access Token (public, read-only)
- Scope: `read_products`, `read_inventory`
- Rate limit: 2 req/s per IP

## Example: Fetch Product
```graphql
query Product($handle: String!) {
  product(handle: $handle) {
    id title priceRange { minVariantPrice { amount } }
    variants(first: 1) { edges { node { id availableForSale } } }
  }
}
```

## Buy Button
- Generate via Partner Dashboard → Buy Button
- Embeds checkout on external sites
- Limited customization vs custom storefront

## Limitations
- No customer data, orders, or admin mutations
- Use GraphQL Admin API for backend operations
- Prefer Hydrogen/Remix for full custom storefronts

🔗 Reference: https://shopify.dev/docs/api/storefront



---

## Checkout Ui Extensions

---
source: shopify.dev/docs/apps/checkout
version: 2024-10
topic: checkout_extensions
---
# Checkout UI Extensions

## Extension Types
- `checkout.ui.extension` (custom blocks, validation, styling)
- `purchase.options.extension` (shipping, discount, payment)
- `functions` (server-side logic, no JS)

## Setup
```bash
shopify app generate extension --name my-checkout --type checkout_ui_extension
```

## Manifest (`shopify.extension.toml`)
```toml
[[extensions]]
name = "My Checkout Extension"
type = "checkout_ui_extension"
handle = "my-checkout-ext"
```

## Best Practices
- Keep payloads < 10KB
- Use `@shopify/ui-extensions-react`
- Test with `shopify app dev` + checkout preview
- Avoid blocking checkout flow

🔗 Reference: https://shopify.dev/docs/apps/checkout



---

## Functions And Customizations

---
source: shopify.dev/docs/apps/functions
version: 2024-10
topic: shopify_functions
---
# Shopify Functions (Server-Side Logic)

## Overview
- Run on Shopify infrastructure
- No JS/Node runtime
- Written in Rust/Wasm or Shopify CLI templates
- Deterministic, fast, stateless

## Types
- `product_discounts`
- `shipping_discounts`
- `payment_customizations`
- `order_routing`

## Development
```bash
shopify app generate function --name my-discount --type product_discounts
```

## Structure
```rust
// src/main.rs
use shopify_function::prelude::*;

shopify_function!
```

## Limits
- Max 500ms execution
- No external HTTP calls
- Strict input/output schemas

🔗 Reference: https://shopify.dev/docs/apps/functions

