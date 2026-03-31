# UCP — Universal Commerce Protocol

**Creator:** Google (with Shopify contributions)
**Repo:** [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp)
**Version:** Pre-release draft (date-based versioning, no release tags yet)
**License:** Apache 2.0
**Docs:** ucp.dev

## What It Is

Full-stack commerce orchestration layer. Defines capabilities (checkout, identity linking, order management, payment token exchange) and extensions (discounts, fulfillment, AP2 mandates). Transport-agnostic: supports REST, MCP, and A2A bindings. Think of UCP as Google's answer to ACP, but more ambitious in scope.

## Core Concepts

- **Capabilities** — modular features a merchant can expose: Checkout, Identity Linking, Order, Payment Token Exchange
- **Extensions** — optional add-ons: Discounts, Fulfillment, AP2 Mandates
- **Payment Handlers** — pluggable payment methods (Google Pay, Shop Pay, cards). Rail-agnostic.
- **AP2 Mandates Extension** — when negotiated, checkout becomes "Security Locked" with cryptographic mandate binding
- **Multi-transport** — same protocol over REST, MCP tool calls, or A2A agent delegation

## What It Covers

- Full checkout lifecycle with multi-transport support
- AP2 mandate integration (authorization layer built in)
- Payment handler framework (any rail)
- MCP binding (tool calls for create_checkout, update_checkout, etc.)
- A2A binding (agent-to-agent commerce)

## What It Doesn't Cover

- Cross-merchant transaction coordination (not found in current spec)
- Settlement (delegates to payment handlers and underlying rails)
- Its own delegation model (uses AP2 mandates, not its own)

## Live Deployment

UCP powers checkout in Gemini and AI Mode in Google Search (live for US users, Jan 2026). First retailers: Etsy, Wayfair. Coming: Shopify, Target, Walmart. Google charges 0% transaction fees (for now).

## Relationship to Other Protocols

- **Competes with ACP** at the commerce layer (Google vs OpenAI+Stripe)
- **Uses AP2** as its authorization extension
- **Independent of x402/MPP** at settlement (uses payment handlers instead)
- **Complementary to A2A** — A2A handles agent discovery, UCP handles the commerce flow

## Sources

- Overview: `docs/specification/overview.md`
- AP2 extension: `docs/specification/ap2-mandates.md`
- MCP binding: `docs/specification/checkout-mcp.md`
- Payment handlers: `docs/specification/payment-handler-guide.md`

*Last verified: 2026-03-31*
