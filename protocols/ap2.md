# AP2 — Agent Payments Protocol

**Creator:** Google
**Repo:** [github.com/google-agentic-commerce/AP2](https://github.com/google-agentic-commerce/AP2)
**Version:** V0.1 (spec + Python/Go samples, Android SDK planned)
**License:** Apache 2.0
**Partners:** 125 listed in `docs/partners.md` (Mastercard, Visa, Amex, PayPal, Shopify, Stripe, Coinbase, Adyen, Klarna, etc.)

## What It Is

Authorization and accountability layer for agent-initiated purchases. AP2 doesn't process payments. It defines verifiable credentials (mandates) that prove a human authorized an agent to buy something, and gives merchants and payment networks cryptographic evidence of that authorization.

## Core Concepts

Three mandate types form a chain:

1. **IntentMandate** — what the user wants ("buy red shoes under $200"). Signed by the user's device key. Contains natural language description, merchant allowlist, SKU constraints, expiry.

2. **CartMandate** — what the merchant is offering. Signed by the merchant (JWT). Contains specific items, prices, shipping. The user approves by signing a hash of the CartMandate in the PaymentMandate.

3. **PaymentMandate** — authorization to charge. Contains payment details, bound to the CartMandate hash. Shared with payment networks/issuers alongside standard transaction auth messages.

## What It Covers

- Human-present and human-not-present transaction flows
- Mandate signing and binding (user device key, merchant JWT)
- Accountability matrix (who's liable when things go wrong)
- Risk signals framework (Section 7)
- A2A protocol extension for agent-to-agent flows

## What It Doesn't Cover (Yet)

- Cross-merchant budget enforcement (roadmap: "multi-merchant transaction topologies")
- Mandate **verification** utilities (spec describes the model, no verification code exists)
- MCP integration (planned for v1.x)
- Push payments, real-time transfers, e-wallets (planned for v1.x)
- Budget as a structured field (discussed in Issue #133, not yet added)

## Key Open Issues

- [#150](https://github.com/google-agentic-commerce/AP2/issues/150) — Security inconsistencies between spec and implementation (no signature fields in code)
- [#133](https://github.com/google-agentic-commerce/AP2/issues/133) — Standardized budget limitation field
- [#207](https://github.com/google-agentic-commerce/AP2/issues/207) — Cross-merchant budget enforcement

## Relationship to Other Protocols

- **UCP** uses AP2 mandates as an extension (`dev.ucp.shopping.ap2_mandate`)
- **x402** has an A2A x402 extension for crypto settlement within AP2 flows
- **ACP** is an alternative approach to the same problem (OpenAI+Stripe vs Google)

## Sources

- Spec: `docs/specification.md` in repo
- Types: `src/ap2/types/mandate.py`
- Roadmap: `docs/roadmap.md`
- A2A extension: `docs/a2a-extension.md`

*Last verified: 2026-03-31*
