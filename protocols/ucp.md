# UCP — Universal Commerce Protocol

**Creator:** Co-governed open standard (`ucp.dev`); co-developers include Google, Shopify, Etsy, Walmart, Amazon, Microsoft, Meta, Salesforce, Stripe + lodging/food brands
**Repo:** [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp) (independent org, not Google-owned)
**Version:** v2026-04-08 (latest tagged release; the first was v2026-01-11)
**License:** Apache 2.0
**Docs:** ucp.dev

## What It Is

Full-stack commerce orchestration layer. Defines capabilities (checkout, identity linking, order management, payment token exchange) and extensions (discounts, fulfillment, AP2 mandates). Transport-agnostic: supports REST, MCP, and A2A bindings. Think of UCP as Google's answer to ACP, but more ambitious in scope.

## Core Concepts

- **Capabilities** — modular features a merchant can expose: Cart, Catalog, Checkout, Identity Linking, Order
- **Extensions** — optional add-ons: Discounts, Fulfillment, Buyer Consent, Loyalty, Split Payments, AP2 Mandates
- **Payment Handlers** — pluggable payment methods (Google Pay, Shop Pay, cards). Rail-agnostic.
- **AP2 Mandates Extension** — when negotiated, checkout becomes "Security Locked" with cryptographic mandate binding
- **Multi-transport** — same protocol over REST, MCP tool calls, A2A agent delegation, or the Embedded Protocol (OpenRPC)

## What It Covers

- Full checkout lifecycle with multi-transport support (REST, MCP, A2A, Embedded Protocol)
- AP2 mandate integration (authorization layer built in)
- Payment handler framework (any rail)
- MCP bindings for both checkout and cart capabilities
- Loyalty, Split Payments, and Buyer Consent extensions

## What It Doesn't Cover

- Cross-merchant transaction coordination (not found in current spec)
- Settlement (delegates to payment handlers and underlying rails)
- Its own delegation model (uses AP2 mandates, not its own)

## Endorsed Partners

~70+ endorsed partners listed on ucp.dev, including Visa, Mastercard, PayPal, Adyen, Worldpay, Klarna, Affirm, Block, Fiserv, Checkout.com, Splitit, plus retailers (The Home Depot, Best Buy, Kroger, Macy's, Sephora, Ulta, Gap, Zalando) and travel/food brands. (Was ~6 at last verification.)

## Live Deployment

UCP powers checkout in Gemini and AI Mode in Google Search (live for US users, Jan 2026). The first live retailers (Jan 2026) were **Lowe's, Michael's, Poshmark, and Reebok** — Etsy and Wayfair are co-developers of the standard, not first live retailers. At Google Marketing Live (May 2026), Target, Walmart, Wayfair, Nike, Sephora, and Ulta were named for near-term checkout rollout, with expansion to Canada, Australia, and the UK.

## Recent Changes (since 2026-04-14)

- **Loyalty extension** merged (2026-05-19) — tier recognition, member benefits, rewards across catalog/cart/checkout
- **Split Payments extension** merged (2026-05-23) — multi-instrument checkout (cards, gift cards, store credit, loyalty)
- **Universal Cart announced** (2026-05-19) — cross-merchant agentic shopping hub across Search, Gemini, YouTube, Gmail; US rollout "this summer"
- **Vertical expansion** announced to Lodging (Amadeus, Booking.com, Hilton, Marriott, Expedia) and Food (DoorDash, Square, Toast, Uber Eats); specs pending
- **Google Marketing Live 2026** (2026-05-20) — checkout expansion to CA/AU/UK, YouTube integration
- **Partner page overhaul** (2026-05-19) — ~70+ endorsed partner logos added

## Relationship to Other Protocols

- **Competes with ACP** at the commerce layer (Google vs OpenAI+Stripe)
- **Uses AP2** as its authorization extension
- **Independent of x402/MPP** at settlement (uses payment handlers instead)
- **Complementary to A2A** — A2A handles agent discovery, UCP handles the commerce flow

## Sources

- [Overview](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/overview.md)
- [AP2 mandates extension](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/ap2-mandates.md)
- [MCP checkout binding](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/checkout-mcp.md)
- [Payment handler guide](https://github.com/Universal-Commerce-Protocol/ucp/blob/main/docs/specification/payment-handler-guide.md)
- [ucp.dev (partners, co-developers)](https://ucp.dev)

*Last verified: 2026-06-01*
