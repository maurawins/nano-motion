# Nano Motion demo storefront

A responsive, fictional premium-activewear ecommerce experience built to demonstrate an OpenAI Ads Measurement Pixel integration for a Solutions Engineering take-home assignment.

## Stack and architecture

- Vanilla HTML, CSS, and JavaScript; no build step or backend.
- Static-host compatible (Vercel, Netlify, or GitHub Pages with SPA fallback/rewrite).
- Product catalog and membership plan are demo data in `app.js`.
- Cart, consent preference, checkout journey guard, and demo confirmations use browser storage.
- No real payment processor, order API, customer account system, or recurring billing is connected.

## Run locally

From the repository root:

```bash
python3 -m http.server 4173
```

Open [http://localhost:4173](http://localhost:4173).

## Deploy to Vercel

1. Import this GitHub repository into Vercel.
2. Select **Other** as the framework preset.
3. Leave the build and install commands empty.
4. Set the output directory to `.`.
5. Deploy. The included `vercel.json` rewrites clean client-side routes to `/index.html`.

The app has no required environment variables. For a production deployment, connect the domain, analytics governance, real commerce backend, and consent-management platform separately.

## OpenAI Ads Measurement Pixel

- Pixel ID: `JbChK6ZzAdxdCN9vy6PKWR`.
- SDK is loaded in `index.html`; consent is set to denied before initialization. The saved visitor preference is applied before the first route is rendered and measured.
- The demo consent banner lets a visitor accept or decline optional measurement. The preference is persisted locally.
- This banner is a demonstration control, **not a certified consent-management platform** and not a legal determination for any market.
- SDK debug mode is enabled for development and should be reviewed/disabled according to the production release process.

### Event taxonomy and business rationale

| Event | Trigger in this demo | Why it matters |
|---|---|---|
| `page_viewed` | Each client-side route or query-state navigation (not UI-only re-renders) | Measures site traffic and navigation context. |
| `contents_viewed` | Product detail view and membership-plan view | Measures consideration of specific products and the membership offer. |
| `items_added` | Quick-add, product add, or cart quantity increase | Captures product-level shopping intent. Decreases/removals do not count as adds. |
| `checkout_started` | First entry into product checkout or Motion Club enrollment checkout for that journey | Measures high-intent progression toward purchase or membership; guarded against refresh duplicates. |
| `order_created` | Valid demo checkout form submission, at the simulated order-confirmation boundary | Represents a completed demo purchase, not a real payment or fulfilled order. |
| `subscription_created` | Visitor completes the membership enrollment form with the designated test card and accepts the demo terms | Represents a completed simulated enrollment, not a payment-provider-confirmed subscription. No payment method is transmitted, stored, or charged. |

### Measurement conventions

- Product identifiers are stable catalog IDs (for example, `NM-RUN-001`) and should remain consistent across markets and channels.
- Currency is currently USD. Event amounts are integer minor units (USD cents).
- `checkout_started` and `order_created` report the estimated/order total including the demo shipping charge, so the top-level event amount follows the same gross-order convention. Product-level `contents[].amount` remains the unit price in minor units; quantity is reported separately.
- Purchase and membership events include a unique `event_id`. If a future server-side integration sends the same conversion, it must reuse the corresponding event ID and follow OpenAI's documented deduplication contract.
- The browser implementation must never contain a server-side Conversions API token or secret.

### International expansion

Before launching in another market:

1. Use ISO 4217 currency codes and integer minor units appropriate to the currency; do not assume every currency has two decimal places.
2. Localize display formatting, checkout totals, taxes, shipping, product availability, and price books. Do not derive the measurement amount from a rounded display string.
3. Keep product IDs stable across currencies and storefront locales; use market-specific SKU/variant identifiers only when they represent genuinely distinct purchasable variants.
4. Integrate the brand's consent-management platform and apply region-specific consent signals before measurement. Review local privacy, cookie, advertising, and data-transfer requirements with counsel.
5. Test event delivery, currency, amount, and consent behavior independently in every launch market.

### Membership and lifecycle measurement

The demo treats the initial Motion Club enrollment as a distinct conversion from a one-time ecommerce order. The enrollment form simulates contact details, billing address, and test-card validation entirely in the browser; it does not authorize a payment, call a billing provider, persist personal/payment data, or create a recurring subscription. In production, fire `subscription_created` only after the billing provider confirms an active subscription—not on a button click. Keep plan IDs and billing currency stable.

Renewals, failed payments, cancellations, pauses, refunds, and retention should be measured from the authoritative subscription/billing system in first-party analytics or a warehouse. Do not count a renewal as a new acquisition. Only add further Pixel events if they are supported by the current OpenAI documentation and mapped to a clearly defined business outcome.

## QA checklist

- [ ] Open the site with no saved preference; verify the consent banner appears and measurement is denied.
- [ ] Decline consent, navigate, and verify optional measurement remains disabled.
- [ ] Reset the demo consent preference, accept, and verify SDK debug activity in the browser console.
- [ ] Visit home, shop, a product detail page, and membership; verify the relevant page/content events.
- [ ] Add a product and increase cart quantity; verify `items_added` includes product ID, quantity, currency, and amount.
- [ ] Open checkout; verify `checkout_started` fires once despite a refresh.
- [ ] Complete the demo order; verify `order_created` fires at the simulated confirmation boundary and the checkout guard resets for a subsequent order.
- [ ] Join the Motion Club; verify the simulated `subscription_created` event.
- [ ] Test direct refresh on `/shop`, `/cart`, `/checkout`, and `/membership` after deployment.
- [ ] Check OpenAI Ads Manager / Monitoring Events for event receipt. Browser-console SDK activity alone does not prove server-side receipt.

## Known limitations

- Checkout, payment authorization, tax, shipping, inventory, customer identity, orders, and membership billing are simulated. Membership enrollment includes contact, billing-address, and test-card fields for a realistic UX; the only accepted card is the documented demo number, and all form data stays in the browser and is not persisted or transmitted.
- There is no backend or Conversions API integration.
- The consent banner is not a certified CMP.
- Product imagery is loaded from Unsplash and requires an internet connection.
- This demo is suitable for explaining the measurement design and user journey, not for processing real customer data or transactions.
