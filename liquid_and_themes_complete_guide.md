# Liquid And Themes Complete Guide


---

## Liquid Syntax Reference

---
source: shopify.dev/docs/api/liquid
version: 2024-10
topic: liquid_reference
---
# Liquid Syntax Reference (Online Store 2.0)

## Core Concepts
- Liquid is a templating language. Runs server-side. No JS execution.
- Objects: `product`, `collection`, `customer`, `cart`, `section`, `request`
- Tags: `{% if %}`, `{% for %}`, `{% assign %}`, `{% render %}`, `{% include %}` (deprecated)
- Filters: `| date`, `| json`, `| escape`, `| money`, `| image_url`, `| link_to`

## Modern Syntax (OS 2.0)
```liquid
{% render 'product-card', product: product, show_price: true %}
{{ product.metafields.custom.badge | metafield_tag }}
{{ section.settings.hero_image | image_url: width: 1200 | image_tag }}
```

## Common Patterns
```liquid
{% for block in section.blocks %}
  {% case block.type %}
    {% when 'heading' %}
      <h2>{{ block.settings.title }}</h2>
    {% when 'image' %}
      {{ block.settings.image | image_url: width: 600 | image_tag }}
  {% endcase %}
{% endfor %}
```

## Variable Assignment & Logic
```liquid
{% assign max_price = 100 %}
{% if product.price < max_price %}
  <span class="badge">Under ${{ max_price }}</span>
{% endif %}

{% for variant in product.variants %}
  {% if variant.available %}
    <option value="{{ variant.id }}">{{ variant.title }}</option>
  {% endif %}
{% endfor %}
```

## Deprecations & Warnings
- ❌ `{% include 'snippet' %}` → Use `{% render 'snippet' %}`
- ❌ `{{ product.title | default: "Untitled" }}` → Use `{% assign title = product.title | default: "Untitled" %}`
- ❌ Inline JS in Liquid → Move to `assets/theme.js` and use `data-` attributes

🔗 Reference: https://shopify.dev/docs/api/liquid


---

## Os2 Theme Architecture

---
source: shopify.dev/docs/themes
version: 2024-10
topic: theme_architecture
---
# Online Store 2.0 Theme Architecture

## Directory Structure
```
theme/
├── assets/          # CSS, JS, images
├── config/          # settings_schema.json, settings_data.json
├── layout/          # theme.liquid
├── locales/         # en.default.json, fr.json
├── sections/        # JSON templates, reusable blocks
├── snippets/        # {% render %} partials
├── templates/       # JSON templates (product.json, index.json)
└── shopify.theme.toml
```

## JSON Templates
- Replace legacy `.liquid` templates in `/templates/`
- Define section stacking in Theme Editor
```json
{
  "sections": {
    "main": { "type": "main-product", "blocks": { "title": {}, "price": {} } },
    "recommendations": { "type": "related-products" }
  },
  "order": ["main", "recommendations"]
}
```

## Best Practices
- Use `theme.liquid` only for `<head>`, `<body>`, and global assets
- Load scripts with `defer` or `type="module"`
- Use `settings_schema.json` for global theme settings
- Validate with `shopify theme check`

🔗 Reference: https://shopify.dev/docs/themes/architecture


---

## Section And Block Schema

---
source: shopify.dev/docs/themes/sections
version: 2024-10
topic: section_schema
---
# Section & Block Schema Configuration

## Schema Structure
```liquid
{% schema %}
{
  "name": "Hero Banner",
  "tag": "section",
  "class": "hero",
  "settings": [
    { "type": "text", "id": "heading", "label": "Heading", "default": "Welcome" },
    { "type": "image_picker", "id": "bg_image", "label": "Background" },
    { "type": "range", "id": "opacity", "label": "Overlay", "min": 0, "max": 1, "step": 0.1, "default": 0.5 }
  ],
  "blocks": [
    {
      "type": "slide",
      "name": "Slide",
      "settings": [
        { "type": "text", "id": "title", "label": "Title" },
        { "type": "url", "id": "link", "label": "Button URL" }
      ]
    }
  ],
  "presets": [{ "name": "Hero Banner", "category": "Custom" }]
}
{% endschema %}
```

## Dynamic Sources (OS 2.0)
- Use `"dynamic_sources": true` to allow merchants to pull metafields
- Reference: `product.metafields.custom.*`, `collection.metafields.*`

## Validation Tips
- Test schema in Theme Editor → Add Section
- Use `shopify theme check` for JSON/Liquid errors
- Avoid nested blocks >3 levels deep for performance

🔗 Reference: https://shopify.dev/docs/themes/sections


---

## Metafields And Dynamic Sources

---
source: shopify.dev/docs/metafields
version: 2024-10
topic: metafields
---
# Metafields & Dynamic Sources

## Definition & Namespaces
- Format: `namespace.key` (e.g., `custom.badge`, `specs.dimensions`)
- Types: `single_line_text_field`, `number_integer`, `file_reference`, `list.collection`, `json`

## Liquid Access
```liquid
{{ product.metafields.custom.badge.value }}
{{ product.metafields.custom.materials | metafield_tag }}
{{ collection.metafields.seo.description }}
```

## Admin Setup
- Settings → Custom Data → Products → Add definition
- Enable "Used in theme" for dynamic source availability in Theme Editor

## Best Practices
- Use `custom` namespace for merchant-defined fields
- Store complex data as `json` type
- Validate with `shopify theme check` and Theme Editor preview
- Avoid metafields for high-frequency filtering (use tags or search instead)

🔗 Reference: https://shopify.dev/docs/apps/custom-data/metafields
