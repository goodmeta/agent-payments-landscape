# UCP — Universal Commerce Protocol

**Creator:** Co-governed open standard (`ucp.dev`). Founded by Google and Shopify, which hold the two permanent Governing Council seats; the other three seats are elected, and Google holds the proxy vote for any open seat until Dec 2028. Google is custodian of the `ucp.dev` domain.
**Repo:** [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp) (independent org, not Google-owned)
**Version:** v2026-08-25 (latest tagged release; the first was v2026-01-11)
**License:** Apache 2.0
**Docs:** ucp.dev

## What It Is

Full-stack commerce orchestration layer. Defines capabilities (checkout, cart, catalog, identity linking, order management, location, natural-language Q&A, lodging booking) and extensions (discounts, fulfillment, AP2 mandates, payment terms). Transport-agnostic: supports REST, MCP, A2A, and embedded bindings. Broader in scope than ACP, which it competes with at the commerce layer.

## Core Concepts

- **Capabilities** — modular features a merchant can expose: Cart, Catalog, Checkout, Identity Linking, Order, Location (search + lookup), Ask (natural-language Q&A, `dev.ucp.common.ask`), and Lodging Booking (`dev.ucp.lodging.booking`)
- **Extensions** — optional add-ons: Discounts, Fulfillment, Buyer Consent, Loyalty, Split Payments, Payment Terms, Payment Authentication (device data collection, 3DS challenge), AP2 Mandates, and Cancellation Policy (lodging)
- **Payment Handlers** — pluggable payment methods. Rail-agnostic.
- **AP2 Mandates Extension** (`dev.ucp.common.payment.ap2_mandate`) — when negotiated, checkout becomes "Security Locked" with cryptographic mandate binding
- **Multi-transport** — same protocol over REST, MCP tool calls, A2A (checkout), or the Embedded Protocol (checkout, cart)

## What It Covers

- Full checkout lifecycle with multi-transport support (REST, MCP, A2A, Embedded Protocol)
- AP2 mandate integration (UCP defines where mandates are placed; the mandate semantics are AP2's)
- Payment handler framework (any rail)
- MCP bindings for checkout, cart, catalog, order, ask, location, and lodging booking
- Loyalty, Split Payments, Buyer Consent, Payment Terms, and Payment Authentication extensions
- Payment timing: deposits, balances, and fixed installment schedules (Payment Terms)

## What It Doesn't Cover

- Recurring or subscription billing. Payment Terms carries fixed schedules with absolute RFC 3339 dates the merchant computed — there is no recurrence engine and no anchor arithmetic in the spec.
- Spending limits of its own. The AP2 Mandates extension's "Specification Boundary" says it defines *where* mandates are placed, not their contents; any budget lives in the AP2 mandate it carries.
- Cross-merchant transaction coordination (not found in current spec)
- Settlement (delegates to payment handlers and underlying rails)
- Its own delegation model (uses AP2 mandates, not its own)

## Endorsed Partners

ucp.dev lists endorsed partners including Visa, Mastercard, PayPal, Adyen, Worldpay, Klarna, Affirm, Block, Fiserv, Checkout.com, Splitit, plus retailers and travel/food brands.

## Live Deployment

UCP powers checkout in Gemini and AI Mode in Google Search (live for US users, Jan 2026). The first live retailers (Jan 2026) were **Lowe's, Michael's, Poshmark, and Reebok** — Etsy and Wayfair are co-developers of the standard, not first live retailers. At Google Marketing Live (May 2026), Target, Walmart, Wayfair, Nike, Sephora, and Ulta were named for near-term checkout rollout, with expansion to Canada, Australia, and the UK.

## Recent Changes (since 2026-08-18)

- **Ask capability** merged ([PR #538](https://github.com/Universal-Commerce-Protocol/ucp/pull/538), 2026-10-07) — `dev.ucp.common.ask`: a platform asks a business a free-form question, optionally grounded in specific resources (a product, an order, a location), and gets a text answer plus related links. REST and MCP bindings.
- **Lodging Booking capability + Cancellation Policy extension** merged (PR #780, 2026-09-24) — `dev.ucp.lodging.booking`, the first spec from the lodging vertical announced in May.
- **v2026-08-25 released** (2026-08-25). The same day, payment constructs and their extensions moved from `shopping/` to `common/` (PR #741), which renamed Payment Terms to `dev.ucp.common.payment.terms` and the AP2 extension to `dev.ucp.common.payment.ap2_mandate`.
- **Location capability** merged (PR #589, 2026-08-25) — location search and lookup.

## Earlier Changes (2026-06-01 to 2026-08-18)

- **Payment Terms extension** merged ([PR #602](https://github.com/Universal-Commerce-Protocol/ucp/pull/602), 2026-08-14) — now `dev.ucp.common.payment.terms`. A merchant publishes the payment terms it accepts (pay in full, deposit plus balance, installments), the buyer selects one, and the merchant renegotiates the rest of checkout around it. Adds `payment.terms[]` and `payment.selected_term_id` on Checkout and `payment.accepted_term` on Order. Due dates (`due_at`) are absolute RFC 3339 date-times.

## Earlier Changes (2026-04-14 to 2026-06-01)

- **Loyalty extension** merged (2026-05-19) — tier recognition, member benefits, rewards across catalog/cart/checkout
- **Split Payments extension** merged (2026-05-23) — multi-instrument checkout (cards, gift cards, store credit, loyalty)
- **Universal Cart announced** (2026-05-19) — cross-merchant agentic shopping hub across Search, Gemini, YouTube, Gmail; US rollout "this summer"
- **Vertical expansion** announced to Lodging (Amadeus, Booking.com, Hilton, Marriott, Expedia) and Food (DoorDash, Square, Toast, Uber Eats)
- **Google Marketing Live 2026** (2026-05-20) — checkout expansion to CA/AU/UK, YouTube integration
- **Partner page overhaul** (2026-05-19) — endorsed partner logos added

## Relationship to Other Protocols

- **Competes with ACP** at the commerce layer (Google + Shopify vs OpenAI + Stripe)
- **Uses AP2** as its authorization extension
- **Independent of x402/MPP** at settlement (uses payment handlers instead)
- **Complementary to A2A** — A2A handles agent discovery, UCP handles the commerce flow

## Sources

- [Overview](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/overview/index.md)
- [AP2 mandates extension](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/payment/extensions/ap2-mandates.md)
- [Payment Terms](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/payment/extensions/terms.md)
- [Ask capability](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/common/ask/index.md)
- [MCP checkout binding](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/shopping/checkout/mcp.md)
- [Payment handler guide](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/payment/guide.md)
- [Governance](https://github.com/Universal-Commerce-Protocol/.github/blob/main/GOVERNANCE.md)
- [ucp.dev (partners)](https://ucp.dev)

*Last verified: 2026-10-12*
