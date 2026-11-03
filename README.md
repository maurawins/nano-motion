# Nano Motion demo storefront

A static, responsive, fictional premium activewear ecommerce experience built to demonstrate the OpenAI Ads Measurement Pixel.

## Stack
- Vanilla HTML, CSS, and JavaScript (no build step or backend)
- Static hosting compatible with Vercel, Netlify, or GitHub Pages
- Browser storage for a simulated cart, demo checkout, and membership enrollment

## Run locally
From this directory:

```bash
python3 -m http.server 4173
```

Open http://localhost:4173.

## Deploy to Vercel
1. Create a GitHub repository and upload the contents of this folder to its root.
2. In Vercel, import the repository.
3. Select **Other** as the framework preset.
4. Leave the build command empty and set the output directory to `.`.
5. Deploy. The site is a static app; no environment variables or build step are required.

For GitHub Pages, enable Pages for the repository's main branch and root directory. Since this demo uses clean client-side paths, configure the host to rewrite unknown paths to `/index.html` (Vercel supports this with the included `vercel.json`).

## Pixel implementation
- Pixel ID: `JbChK6ZzAdxdCN9vy6PKWR`
- SDK is initialized in `index.html`.
- Consent is set to `false` before initialization; visitors can accept or decline optional measurement via the banner.
- Standard events: `page_viewed`, `contents_viewed`, `items_added`, `checkout_started`, `order_created`, `subscription_created`.
- `order_created` is sent only after the simulated order confirmation boundary; `subscription_created` is sent after simulated enrollment.
- Purchase and subscription events include unique `event_id` values to support future browser/server deduplication if a server integration is added.
- Monetary values use integer minor units (USD cents).

## Demo limitations and production follow-ups
- No real checkout, payment authorization, order management, account creation, or subscription billing exists.
- No Conversions API integration exists; never expose a CAPI token in frontend code. A production server should send confirmed order/subscription events with the same event IDs as browser events if both channels are used.
- The consent banner is a demo preference control, not a certified CMP. Production consent requirements must be reviewed for each market and integrated with the advertiser's CMP.
- Product photography is loaded from Unsplash URLs and requires internet access.
- Product inventory, tax, shipping, and customer identity are simulated.

## QA checklist
- Accept consent, then inspect the browser console for Pixel SDK debug activity.
- Decline consent and verify events are not sent.
- Visit a product detail page (`contents_viewed`).
- Add a product (`items_added`).
- Open checkout (`checkout_started`, once per checkout session).
- Complete the demo order (`order_created`, once at confirmation).
- Join the Motion Club (`subscription_created`, once after enrollment).
- Inspect OpenAI Ads Manager / Monitoring Events for receipt; browser console activity alone does not prove server-side receipt.
