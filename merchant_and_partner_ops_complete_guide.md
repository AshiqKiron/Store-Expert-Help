# Merchant And Partner Ops Complete Guide


---

## Partner Dashboard And Stores

---
source: help.shopify.com/en/partners
version: 2024-10
topic: partner_dashboard
---
# Partner Dashboard & Development Stores

## Dev Stores
- Free, unlimited for development
- Reset via Partner Dashboard
- Cannot process live payments (use Bogus Gateway)
- Max 10 GB storage

## Partner Dashboard
- Manage apps, themes, clients
- View analytics, payouts, reviews
- Generate API credentials
- Transfer stores to merchants

## Best Practices
- Use separate dev stores per client/project
- Enable test mode for payments
- Document app scopes and webhooks per store
- Archive unused dev stores to avoid clutter

🔗 Reference: https://help.shopify.com/en/partners/dashboard


---

## Billing And Pricing Rules

---
source: shopify.dev/docs/apps/billing
version: 2024-10
topic: app_billing
---
# App Billing & Pricing Rules

## Billing API
- GraphQL mutations: `appSubscriptionCreate`, `appRecurringPricingDetails`
- Requires `write_merchant_shop_billing` scope
- Merchant must approve charge in admin

## Pricing Models
- Recurring (monthly/annual)
- One-time (setup fee)
- Usage-based (metered billing)

## Implementation
```graphql
mutation CreateCharge($name: String!, $price: Decimal!) {
  appSubscriptionCreate(name: $name, lineItems: [{ plan: { pricingDetails: { recurring: { price: $price } } } }]) {
    userErrors { field message }
    appSubscription { id confirmationUrl }
  }
}
```

## Compliance
- Transparent pricing
- Refund policies defined
- Webhook `APP_SUBSCRIPTIONS_UPDATE` for status changes
- Never bypass Shopify checkout for billing

🔗 Reference: https://shopify.dev/docs/apps/billing


---

## Deployment And App Review

---
source: shopify.dev/docs/apps/store
version: 2024-10
topic: app_review
---
# Deployment & App Review Process

## Pre-Review Checklist
- [ ] Pass `shopify app lint`
- [ ] Implement OAuth flow securely
- [ ] Handle `APP_UNINSTALLED` webhook
- [ ] Provide privacy policy & terms URL
- [ ] Test in multiple dev stores
- [ ] Use Polaris UI components

## Submission
- Partner Dashboard → Apps → Request Review
- Provide demo video, test credentials, documentation
- Review takes 3-7 business days

## Rejections
- Fix security, UX, or compliance issues
- Resubmit with changelog notes
- Contact partner support if blocked

🔗 Reference: https://shopify.dev/docs/apps/store/submission-guidelines
