# ACP — Agentic Commerce Protocol

**Creator:** OpenAI + Stripe (founding maintainers) + Meta. `MAINTAINERS.md` now lists all three as lead maintainers.
**Repo:** [github.com/agentic-commerce-protocol/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)
**Version:** Beta (5 versioned releases, latest still 2026-04-17; 18 changes queued in `changelog/unreleased`)
**License:** Apache 2.0 (CLA required)
**Signatories:** 10 corporate CLA signatories — Stripe, OpenAI, Adyen, Wix, commercetools, OpenCommerce Network, Affirm, Meta Platforms, Agentic Commerce Inc. (Catalog), PayPal. No new signatory since PayPal on 2026-04-16. Razorpay is *not* a signatory (it has open SEP proposals only).

## What It Is

Commerce checkout layer for AI agent shopping flows. Defines REST APIs for creating, updating, completing, and canceling checkout sessions. Payments stay on the merchant's existing rails (Stripe, Adyen, etc.). ACP handles the shopping session, not the payment itself.

## Core Concepts

- **Checkout Session** — lifecycle for an agent-initiated purchase (create, update items, apply payment, complete)
- **Delegate Payment** — agent receives a scoped payment token from the user's payment provider, with an Allowance limiting how it can be used
- **Allowance** — spending constraints: `max_amount`, `currency`, `expires_at`, `merchant_id`, `checkout_session_id`. `reason` **MUST** be `one_time` (`rfc.delegate_payment.md` §3.5, unchanged) — so there is still no recurring or multi-use delegate token.
- **Payment Handlers** — extensible framework for different payment methods (cards, digital wallets, agentic tokens)

## What It Covers

- Full checkout lifecycle (session management, cart updates, fulfillment)
- Delegate payment tokenization with Allowance constraints
- Capability negotiation between agents and merchants
- MCP transport binding (5 checkout tools over JSON-RPC, merged 2026-02-19)
- Extensions system (discounts, payment handlers, product feed)

## What It Doesn't Cover

- Cross-merchant coordination (Allowance is scoped to single merchant_id)
- Settlement (relies on merchant's existing PSP)
- Mandate-style cryptographic authorization (uses delegate tokens, not signed mandates)

## Recent Changes (since 2026-06-01)

- **Product URL on checkout Item** ([SEP #281](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/pull/281), merged 2026-07-18) — optional product URL on the checkout item
- **Feed Update ID on Feed API upsert response** ([SEP #260](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/pull/260), merged 2026-06-04)
- **Meta added to the corporate CLA signatories file** ([#263](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/pull/263), merged 2026-06-04)
- **Example and schema corrections** — delegate payment error envelope (#266), MessageInfo/Warning/Error examples (#262), IntentTrace and CancelSessionRequest examples (#253)
- **No new versioned release.** The spec is still at 2026-04-17. Everything above sits in `changelog/unreleased`, which now holds 18 entries.
- **Repo quiet since 2026-07-18** — that is the last merge and the last push. 51 open PRs and 84 open issues as of 2026-08-18.
- **Razorpay SEPs (#215–218) and the Feed Extensions Framework (#223) are all still open**, unchanged since the last check.

## Earlier Changes (2026-04-14 to 2026-06-01)

- **2026-04-17 release** (5th versioned release) — promoted the MCP transport binding, Feed API, Cart Capability, Marketing Consent, and Markdown Specification SEPs
- **Meta joined the TSC (2026-04-24)** — Seat 3, alongside OpenAI (Seat 1) and Stripe (Seat 2); **PayPal signed the CLA (2026-04-16)**
- **Razorpay SEPs** — India payment handlers (Magic Checkout, UPI Circle Delegated, UPI Intent, S2S Cards) opened as PRs #215–218; the original PR #213 was closed without merge (not yet shipped)
- **Fulfillment-on-complete (#200)** and **suggested-pricing-on-create (#201)** merged 2026-05-22
- **Feed Extensions Framework** (PR #223) still an open SEP — distinct from the Feed API, which did merge
- **`risk_signals` relaxation** — empty arrays now allowed (Braintree/PayPal production discovery, Apr 9)

## Relationship to Other Protocols

- **Competes with UCP** at the commerce layer (OpenAI+Stripe vs Google+Shopify)
- **Independent of AP2** — uses its own delegation model (Allowance), not AP2 mandates
- **Independent of x402/MPP** — not designed for per-request micropayments

## Sources

- [Spec: agentic_checkout](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/rfcs/rfc.agentic_checkout.md)
- [Delegate Payment](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/rfcs/rfc.delegate_payment.md)
- [Payment Handlers](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/rfcs/rfc.payment_handlers.md)
- [MCP transport binding](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/docs/mcp-binding.md)
- [Governance / MAINTAINERS](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/MAINTAINERS.md)
- [CLA signatories](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/blob/main/legal/cla/SIGNATORIES.md)
- [Unreleased changelog](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol/tree/main/changelog/unreleased)

*Last verified: 2026-08-18*
