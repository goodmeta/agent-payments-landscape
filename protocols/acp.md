# ACP — Agentic Commerce Protocol

**Creator:** OpenAI + Stripe (co-maintained)
**Repo:** [github.com/agentic-commerce-protocol/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)
**Version:** Beta (4 releases: 2025-09-29, 2025-12-12, 2026-01-16, 2026-01-30)
**License:** Apache 2.0 (CLA required)
**Signatories:** Stripe, OpenAI, Adyen, Wix, commercetools, OpenCommerce Network, Affirm, Meta Platforms

## What It Is

Commerce checkout layer for AI agent shopping flows. Defines REST APIs for creating, updating, completing, and canceling checkout sessions. Payments stay on the merchant's existing rails (Stripe, Adyen, etc.). ACP handles the shopping session, not the payment itself.

## Core Concepts

- **Checkout Session** — lifecycle for an agent-initiated purchase (create, update items, apply payment, complete)
- **Delegate Payment** — agent receives a scoped payment token from the user's payment provider, with an Allowance limiting how it can be used
- **Allowance** — spending constraints: `max_amount`, `currency`, `expires_at`, `merchant_id`, `checkout_session_id`. Currently `reason` must be `one_time`.
- **Payment Handlers** — extensible framework for different payment methods (cards, digital wallets, agentic tokens)

## What It Covers

- Full checkout lifecycle (session management, cart updates, fulfillment)
- Delegate payment tokenization with Allowance constraints
- Capability negotiation between agents and merchants
- Extensions system (discounts, payment handlers)

## What It Doesn't Cover

- Cross-merchant coordination (Allowance is scoped to single merchant_id)
- MCP integration (REST-only, no MCP transport binding)
- Settlement (relies on merchant's existing PSP)
- Mandate-style cryptographic authorization (uses delegate tokens, not signed mandates)

## Relationship to Other Protocols

- **Competes with UCP** at the commerce layer (OpenAI+Stripe vs Google+Shopify)
- **Independent of AP2** — uses its own delegation model (Allowance), not AP2 mandates
- **Independent of x402/MPP** — not designed for per-request micropayments

## Sources

- Spec: `rfcs/rfc.agentic_checkout.md`
- Delegate Payment: `rfcs/rfc.delegate_payment.md`
- Payment Handlers: `rfcs/rfc.payment_handlers.md`
- OpenAPI specs: `spec/2026-01-30/openapi/`

*Last verified: 2026-03-31*
