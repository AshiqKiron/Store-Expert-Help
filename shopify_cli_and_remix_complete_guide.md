# Shopify Cli And Remix Complete Guide


---

## Cli V3 Setup And Workflow

---
source: shopify.dev/docs/apps/tools/cli
version: 2024-10
topic: cli_v3
---
# Shopify CLI v3: Setup & Workflow

## Prerequisites
- Node.js 18+
- npm 9+
- Shopify Partner account

## Initialize App
```bash
npm create @shopify/app@latest -- --name my-shopify-app
cd my-shopify-app
```

## Core Commands
```bash
shopify app dev              # Local dev + ngrok tunnel
shopify app generate schema  # Refresh GraphQL schema
shopify app deploy           # Push to Shopify
shopify app versions list    # Check deployed versions
```

## Project Structure
```
my-app/
├── app/              # Remix frontend + routes
├── extensions/       # UI extensions, functions
├── shopify.app.toml  # Config
└── package.json
```

## Best Practices
- Use `shopify app dev` for local OAuth + tunnel
- Commit `shopify.app.toml`, ignore `.env`
- Test in development stores before production

🔗 Reference: https://shopify.dev/docs/apps/tools/cli



---

## Remix App Structure And Routing

---
source: shopify.dev/docs/apps/getting-started
version: 2024-10
topic: remix_structure
---
# Remix App Structure & Routing

## File-Based Routing
```
app/routes/
├── _index.tsx          # App home
├── $.tsx               # Catch-all for embedded routes
├── webhooks.app.uninstalled.ts
└── api.products.ts     # Custom API route
```

## Auth Integration
```ts
// app/shopify.server.ts
import { shopifyApp, LATEST_API_VERSION } from "@shopify/shopify-app-remix/server";
export const shopify = shopifyApp({
  api: { apiVersion: LATEST_API_VERSION, scopes: ["read_products"] },
  auth: { path: "/api/auth", callbackPath: "/api/auth/callback" },
  webhooks: { path: "/webhooks" }
});
```

## Embedded App Setup
- Use `@shopify/app-bridge-react`
- Route via `admin.shopify.com/store/{shop}/apps/{app-handle}/`
- Enable `forceRedirect` for legacy compatibility

🔗 Reference: https://shopify.dev/docs/apps/tools/cli/remix



---

## Shopify App Toml And Config

---
source: shopify.dev/docs/apps/tools/cli/configuration
version: 2024-10
topic: app_config
---
# `shopify.app.toml` Configuration

## Base Config
```toml
name = "my-shopify-app"
handle = "my-app-handle"
client_id = "your_client_id"
application_url = "https://localhost:3456"
embedded = true

[access_scopes]
scopes = "read_products,write_orders"

[auth]
redirect_urls = ["https://localhost:3456/api/auth/callback"]

[webhooks]
api_version = "2024-10"

[build]
automatically_update_urls_on_dev = true
dev_store_url = "my-dev-store.myshopify.com"
```

## Important Rules
- `handle` must be URL-safe, unique across Shopify
- `scopes` must match OAuth request
- `automatically_update_urls_on_dev = true` enables localhost tunnel
- Never commit `.env` or `client_secret`

🔗 Reference: https://shopify.dev/docs/apps/tools/cli/configuration



---

## App Proxies And Embedded Apps

---
source: shopify.dev/docs/apps/app-extensions
version: 2024-10
topic: app_proxies
---
# App Proxies & Embedded Apps

## App Proxy Setup
- Route: `/apps/{app-handle}/{subpath}`
- Returns HTML, JSON, or Liquid
- Bypasses CORS for storefront integration

## Proxy Response Format
```json
{
  "status": 200,
  "headers": { "Content-Type": "application/json" },
  "body": { "message": "Hello from proxy" }
}
```

## Embedded App Best Practices
- Use `@shopify/polaris` for UI consistency
- Load via `admin.shopify.com` iframe
- Communicate with parent via App Bridge `dispatch`
- Handle `shop` parameter securely

## Security
- Verify `X-Shopify-Shop-Domain` on proxy routes
- Sign responses if sensitive data
- Never expose admin tokens to storefront

🔗 Reference: https://shopify.dev/docs/apps/app-extensions/app-proxy

