---
source: internal
version: 2024-10
topic: scaffolding_patterns
---
# Standard Scaffolding Patterns

## Theme Section Boilerplate (OS 2.0)
```liquid
{{ section.settings.heading }}
{{ section.settings.description }}
{% for block in section.blocks %}
  {% case block.type %}
    {% when 'image' %}
      {{ block.settings.image | image_url: width: 600 | image_tag }}
    {% when 'text' %}
      <p>{{ block.settings.body }}</p>
  {% endcase %}
{% endfor %}

{% schema %}
{
  "name": "Custom Block",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Welcome" },
    { "type": "richtext", "id": "description", "label": "Description" }
  ],
  "blocks": [
    { "type": "image", "name": "Image", "settings": [{ "type": "image_picker", "id": "image", "label": "Image" }] },
    { "type": "text", "name": "Text", "settings": [{ "type": "richtext", "id": "body", "label": "Body" }] }
  ],
  "presets": [{ "name": "Custom Block", "category": "Custom" }]
}
{% endschema %}
```

## App CLI v3 Structure
```
my-app/
├── app/
│   ├── routes/
│   │   ├── _index.tsx
│   │   └── webhooks.app.uninstalled.ts
│   ├── shopify.server.ts
│   └── root.tsx
├── extensions/
├── shopify.app.toml
└── package.json
```

## Webhook Handler (Remix)
```ts
// app/routes/webhooks.app.uninstalled.ts
import { authenticate } from "../shopify.server";
export async function action({ request }: { request: Request }) {
  const { payload, topic, shop, session, admin } = await authenticate.webhook(request);
  if (topic === "APP_UNINSTALLED") {
    // Cleanup logic here
  }
  return new Response("OK", { status: 200 });
}
```

## App Proxy Route (JSON)
```ts
// app/routes/apps.proxy.tsx
import { authenticate } from "../shopify.server";
export async function loader({ request }: { request: Request }) {
  const { admin } = await authenticate.admin(request);
  const data = { success: true, shop: "test.myshopify.com" };
  return Response.json(data);
}
```

## CLI v3 Commands Reference
```bash
npm create @shopify/app@latest -- --name my-app
cd my-app
shopify app dev
shopify app generate extension --name my-ext --type checkout_ui_extension
shopify app deploy
```

🔗 Reference: https://shopify.dev/docs/apps/tools/cli, https://shopify.dev/docs/themes/sections