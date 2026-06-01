# AP2 — Agent Payments Protocol

**Creator:** Google (core spec donated to the FIDO Alliance, 2026-04-28)
**Repo:** [github.com/google-agentic-commerce/AP2](https://github.com/google-agentic-commerce/AP2) (now samples + SDK only; spec development moved to FIDO)
**Version:** v0.2.0 (2026-04-28) — adds Human-Not-Present flows, Python SDK, Android samples
**License:** Apache 2.0
**Governance:** FIDO Alliance Payments Technical Working Group (chaired by Mastercard + Visa members) develops the spec going forward

## What It Is

Authorization and accountability layer for agent-initiated purchases. AP2 doesn't process payments. It defines verifiable credentials (mandates) that prove a human authorized an agent to buy something, and gives merchants and payment networks cryptographic evidence of that authorization.

## Core Concepts

The v0.2 spec defines two mandate types, each with a "closed" (direct human approval) and "open" (Human-Not-Present, pre-authorized constraints) form:

1. **Checkout Mandate** — what the user authorizes for a purchase (merchant, items, constraints). In the open form, carries pre-authorized constraints for autonomous purchases.

2. **Payment Mandate** — authorization to charge, bound to the checkout. Carries a structured `budget` constraint (`max` amount + `currency`) plus recurrence rules. Shared with payment networks/issuers alongside standard transaction auth messages.

(The earlier IntentMandate / CartMandate / PaymentMandate naming from v0.1 survives only in the Python sample models, not the v0.2 spec text.)

## What It Covers

- Human-present and human-not-present transaction flows
- Mandate signing and binding (user device key, merchant JWT)
- Accountability matrix (who's liable when things go wrong)
- Risk signals framework (Section 7)
- A2A protocol extension for agent-to-agent flows

## What It Doesn't Cover (Yet)

- Cross-merchant budget enforcement (a single-mandate `budget` field now exists, but cross-merchant coordination does not — Issue #207)
- A production verification SDK (v0.2 ships sample MCP servers for every role; the FAQ notes a production SDK + MCP server are still "in progress")
- Push payments, real-time transfers, e-wallets

## Key Open Issues

- [#150](https://github.com/google-agentic-commerce/AP2/issues/150) — Security inconsistencies between spec and implementation (no signature fields in code)
- [#133](https://github.com/google-agentic-commerce/AP2/issues/133) — Standardized budget limitation field
- [#207](https://github.com/google-agentic-commerce/AP2/issues/207) — Cross-merchant budget enforcement
- [#211](https://github.com/google-agentic-commerce/AP2/issues/211) — CartMandate/PaymentMandate binding gap (cart contents not re-verified at final confirmation)
- [#214](https://github.com/google-agentic-commerce/AP2/issues/214) — AP2/A2A challenge flow underdefined at contract layer
- [#215](https://github.com/google-agentic-commerce/AP2/issues/215) — Human-present approval chain underdefined

## Recent Activity (since 2026-04-14)

- **v0.2.0 released (2026-04-28)** — Human-Not-Present flows specified; Python SDK shipped (`code/sdk/python/`); Android samples added; structured `budget` constraint added to the spec + JSON schema
- **Core spec donated to the FIDO Alliance (2026-04-28)** — a new FIDO Payments Technical Working Group (chaired by Mastercard + Visa members) takes over spec development; Mastercard's "Verifiable Intent" framework co-donated
- **MCP sample servers shipped** for all AP2 roles (credentials provider, merchant agent, payment processor, x402 variants)
- **AP2 + x402 Human-Not-Present scenario** added as runnable sample code
- **CONTRIBUTING.md** now scopes the GitHub repo to samples + SDK only; spec contributions go to FIDO

## Relationship to Other Protocols

- **UCP** uses AP2 mandates as an extension (`dev.ucp.shopping.ap2_mandate`)
- **x402** has an A2A x402 extension for crypto settlement within AP2 flows
- **ACP** is an alternative approach to the same problem (OpenAI+Stripe vs Google)

## Sources

- [Specification (v0.2)](https://github.com/google-agentic-commerce/AP2/blob/main/docs/ap2/specification.md)
- [Payment Mandate + budget](https://github.com/google-agentic-commerce/AP2/blob/main/docs/ap2/payment_mandate.md)
- [Mandate models (Python SDK)](https://github.com/google-agentic-commerce/AP2/blob/main/code/sdk/python/ap2/models/mandate.py)
- [FIDO Alliance announcement](https://fidoalliance.org/fido-alliance-to-develop-standards-for-trusted-ai-agent-interactions/)

*Last verified: 2026-06-01*
