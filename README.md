# Agent Payments Landscape

A living comparison of protocols shaping how AI agents transact: AP2, ACP, x402, MPP, and UCP.

This is a neutral, practitioner-maintained reference. No protocol advocacy. Every claim cites a primary source.

## Protocol Comparison

| Dimension | AP2 | ACP | x402 | MPP | UCP |
|-----------|-----|-----|------|-----|-----|
| **Creator** | Google (spec → FIDO Alliance) | OpenAI + Stripe (founding); Meta lead maintainer since 2026-04 | Coinbase; stewarded by the x402 Foundation | Tempo Labs + Stripe | Google + Shopify (founders, permanent council seats) |
| **Maturity** | v0.2.0 (spec + Python SDK; repo unmerged since 2026-04-29) | Beta (latest spec version 2026-04-17; no tagged releases) | V2 (SDKs: TS 2.28.0, Go v2.28.0, Python 2.25.0) | Individual Internet-Draft -01 (expired) | v2026-08-25 (latest tagged) |
| **Layer** | Authorization | Commerce (checkout) | Settlement | Settlement | Commerce (full-stack) |
| **Payment Rails** | Cards; all rails planned | Cards (delegate payment tokens); Razorpay UPI and S2S card handlers proposed (open SEPs) | Crypto rails: `exact` specs for EVM, Solana, Algorand, Aptos, Canton, Cardano, Casper, Concordium, Hedera, Keeta, Lightning (BTC), NEAR, Starknet, Stellar, Sui, TON, XRPL | Multi-rail: method specs on main for Tempo, Stripe, Lightning, Card, Solana, Stellar, EVM, Hedera, Near Intents, USDC, XRPL | Rail-agnostic (pluggable payment handlers) |
| **Agent Delegation** | Yes. Two mandate types: Checkout Mandate + Payment Mandate [1] | Yes. Delegate Payment API with Allowance constraints [2] | Not defined in the spec [3] | Not in the core spec; the `subscription` intent is a recurring authorization for a fixed amount per period [4] | Yes (via AP2 mandates extension) [5] |
| **Budget / Spending Limits** | Yes. `payment.budget` on an open Payment Mandate: a running total across repeated uses of that one mandate [1] | Yes. Allowance object: max_amount, currency, expires_at, scoped to merchant_id [2] | Not in the protocol (client-side budget management is out of scope [3]). All three SDKs: `spendControls`, on by default, $1 per-payment USD cap + asset allowlist, no running total [10] | Not in the core spec. `subscription` caps each billing period at a fixed amount; sessions cap exposure with a prepaid deposit [4] | Not defined by UCP; carries AP2 mandate claims only [5] |
| **Cross-Merchant Coordination** | Not yet (Issue #207) | Not found in spec | No | No | Not found in spec |
| **Cross-Protocol Budget Tracking** | No | No | No | No | No |
| **MCP Integration** | Sample servers shipped (credentials provider, merchant, payment processor) [1] | Yes (transport binding, merged) [2] | Yes (transport spec) [7] | Yes (transport spec) [8] | Yes (checkout, cart, catalog, order, ask, location, lodging bindings) [9] |
| **Post-execution Evidence (third-party verifiable)** | Partial. Dispute Evidence: the signed Checkout and Payment Mandates plus their receipts "can be brought together to provide a non-repudiable picture of the transaction"; retention, retrieval and the dispute process are out of scope [1] | Not found in spec | Partial. Optional Offer and Receipt extension: the merchant signs a receipt (payer, resource, network, time; tx hash optional); no record of the agent's authorization, and trust rests on the merchant's key [11] | Not found in spec (the core `Payment-Receipt` carries no signature) | Not found in spec |
| **License** | Apache 2.0 | Apache 2.0 | Apache 2.0 | CC0 (specs); SDKs vary | Apache 2.0 |

## How the Protocols Relate

```
┌─────────────────────────────────────────────────────────┐
│                    COMMERCE LAYER                       │
│                                                         │
│   ACP (OpenAI+Stripe)          UCP (Google+Shopify)     │
│   REST checkout sessions       Multi-transport commerce │
│   Delegate payment tokens      AP2 mandates extension   │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                  AUTHORIZATION LAYER                    │
│                                                         │
│                    AP2 (Google)                         │
│        Checkout Mandate + Payment Mandate (open/closed) │
│          Cryptographic signing, VDCs, accountability    │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                   SETTLEMENT LAYER                      │
│                                                         │
│   x402 (x402 Foundation)       MPP (Tempo+Stripe)       │
│   HTTP 402 + on-chain          HTTP 402 + multi-rail    │
│   EIP-3009 / Permit2           Challenge/credential/    │
│   Per-request micropayments    receipt flow             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

ACP and UCP are alternatives at the commerce layer. AP2 is the authorization layer that plugs into UCP (and could theoretically plug into ACP). x402 and MPP compete at settlement. AP2 sits above both and works regardless of which settlement protocol wins.

**The gaps nobody fills.** An agent that shops via UCP, pays for APIs via MPP, and settles via x402 leaves three separate questions unanswered, and they are worth keeping apart.

*Cumulative spend across rails* is a rollup over history: what has this agent already spent, everywhere, against one limit. AP2 v0.2 added a `payment.budget` constraint whose running total covers repeated uses of one open Payment Mandate, but not spending across mandates, merchants or protocols. Each protocol still tracks its own transactions in isolation.

*Current-state verification of a counterparty* is a query over present state rather than history: does this wallet satisfy some condition right now — holding a credential, clearing a token threshold — at the moment a payment is about to route. AP2 does not cover it — a mandate authorizes a future payment rather than attesting to present state. Douglas Borthwick argues in [#2](https://github.com/goodmeta/agent-payments-landscape/issues/2) that none of the five sign such a claim, which matches the reading here but has not been checked protocol by protocol.


*Post-execution evidence* is a third gap, distinct from both of the above: no spec defines how someone outside the transaction obtains a record of *what the agent was authorized to do* and *what actually happened*, and checks one against the other. AP2 comes closest: its Dispute Evidence section says the signed Checkout and Payment Mandates and their receipts "can be brought together to provide a non-repudiable picture of the transaction", but retention, retrieval and the dispute process are out of scope, so the evidence stays with the parties to the transaction. x402's optional signed receipt is signed by the merchant and records the payment, not the authorization. We found no signed post-execution record in ACP, MPP or UCP. Raised by giskard09 in [#6](https://github.com/goodmeta/agent-payments-landscape/pull/6).

None of the three is solved here, and none has a section: a pattern earns one in this doc when several communities converge on it independently, the way the budget-reservation shape did.

## Budget Reservation Protocol (Draft)

The [Budget Reservation Protocol](specs/budget-reservation-protocol.md) formalizes a spending enforcement layer that sits between authorization and settlement. Four verbs (`authorize`, `commit`, `refund`, `query`), one budget, any payment rail.

This pattern emerged independently across AP2, x402, MPP, OpenClaw, and ACP communities in April–May 2026. The spec documents the convergence.

## Protocol Details

See individual protocol pages for deeper analysis:

- [AP2](protocols/ap2.md) (Google)
- [ACP](protocols/acp.md) (OpenAI + Stripe)
- [x402](protocols/x402.md) (x402 Foundation)
- [MPP](protocols/mpp.md) (Tempo + Stripe)
- [UCP](protocols/ucp.md) (Google + Shopify)

## Sources

[1] AP2 spec `docs/ap2/specification.md` + `payment_mandate.md` (`payment.budget` constraint), MCP sample servers in `code/samples/python/src/roles` — [github.com/google-agentic-commerce/AP2](https://github.com/google-agentic-commerce/AP2)

[2] ACP `rfcs/rfc.delegate_payment.md` Section 3.5 (Allowance schema) — [github.com/agentic-commerce-protocol/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)

[3] x402 `specs/x402-specification-v2.md` "Out of Scope" — still lists "Client-side budget management" as out of scope on main — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402/blob/main/specs/x402-specification-v2.md)

[4] MPP core spec `specs/core/draft-httpauth-payment-01.md` — no delegation or budget concepts; `specs/intents/draft-payment-intent-subscription-00.md` — recurring fixed-amount authorization — [github.com/tempoxyz/mpp-specs](https://github.com/tempoxyz/mpp-specs)

[5] UCP `docs/specification/payment/extensions/ap2-mandates.md` — AP2 mandate extension; its "Specification Boundary" limits UCP to where mandates are placed — [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp)

[6] AP2 core spec donated to the FIDO Alliance, 2026-04-28 — [fidoalliance.org](https://fidoalliance.org/fido-alliance-to-develop-standards-for-trusted-ai-agent-interactions/)

[7] x402 `specs/transports-v2/mcp.md` — MCP transport specification — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402)

[8] MPP `specs/extensions/transports/draft-payment-transport-mcp-00.md` — MCP transport binding

[9] UCP `docs/specification/shopping/checkout/mcp.md` — MCP checkout binding (other capabilities have their own `mcp.md`)

[10] x402 `docs/sdk-features.md` — `spendControls` client safety feature in TypeScript (PR [#3124](https://github.com/x402-foundation/x402/pull/3124), [#3147](https://github.com/x402-foundation/x402/pull/3147), merged 2026-08-13), Python (PR [#3154](https://github.com/x402-foundation/x402/pull/3154)) and Go (PR [#3156](https://github.com/x402-foundation/x402/pull/3156)), both merged 2026-08-18

[11] x402 `specs/extensions/extension-offer-and-receipt.md` — signed offers and receipts — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402/blob/main/specs/extensions/extension-offer-and-receipt.md)

## Contributing

Found an error? Protocol shipped an update? Open an issue or PR. Every claim must cite a primary source (spec document, GitHub repo, official blog post).

**Editorial rule on implementations.** The matrix and the gap prose describe the five protocols and name no implementations — anyone's, the maintainer's included. A pattern earns prose here when several communities converge on it independently; a specific project earns a mention when one of the five protocols adopts it, or an unaffiliated organization ships an implementation. The Budget Reservation Protocol section below the gaps is maintainer-authored and is kept because it documents such a convergence; it is held to the same test as everything else.

## About

Maintained by [Eric Tsang](https://linkedin.com/in/erictsang). I build agent payment infrastructure across AP2, x402, MPP, and MCP — and agent verification is the business I'm building, which gives me a commercial interest in the gaps this doc names. That interest is why the rule above exists: no implementations are named in the matrix or gap prose, mine included.

*Last verified: 2026-10-12 (all five protocols). Not re-checked on that date: UCP's live-deployment, partner and announcement details, last checked 2026-08-18.*
