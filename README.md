# Agent Payments Landscape

A living comparison of protocols shaping how AI agents transact: AP2, ACP, x402, MPP, and UCP.

This is a neutral, practitioner-maintained reference. No protocol advocacy. Every claim cites a primary source.

## Protocol Comparison

| Dimension | AP2 | ACP | x402 | MPP | UCP |
|-----------|-----|-----|------|-----|-----|
| **Creator** | Google (spec → FIDO Alliance) | OpenAI + Stripe + Meta | Coinbase (now x402 Foundation) | Tempo Labs + Stripe | Co-governed (Google, Shopify + many) |
| **Maturity** | v0.2.0 (spec + Python SDK) | Beta (5 releases) | V2 (SDKs v2.14.0; +Java) | IETF draft-01 | v2026-04-08 (latest tagged) |
| **Layer** | Authorization | Commerce (checkout) | Settlement | Settlement | Commerce (full-stack) |
| **Payment Rails** | Cards; all rails planned | Cards (delegate payment tokens); Razorpay UPI/S2S proposed (open SEPs) | Stablecoins on 14+ chains (Base, Ethereum, Polygon, Solana, Algorand, Aptos, Hedera, Stellar, Sui, Keeta, TON, NEAR, Cardano, Concordium) | Multi-rail: 8 method specs on main (Tempo, Stripe, Lightning, Card, Solana, Stellar, EVM, Hedera) | Rail-agnostic (pluggable payment handlers) |
| **Agent Delegation** | Yes. Two mandate types: Checkout Mandate + Payment Mandate [1] | Yes. Delegate Payment API with Allowance constraints [2] | No (explicitly out of scope) [3] | No [4] | Yes (via AP2 mandates extension) [5] |
| **Budget / Spending Limits** | Yes. Payment Mandate `budget` field (max + currency), single-mandate [1] | Yes. Allowance object: max_amount, currency, expires_at, scoped to merchant_id [2] | No (explicitly out of scope) [3] | No [4] | Yes (inherited from AP2 mandates) [5] |
| **Cross-Merchant Coordination** | Not yet (Issue #207) | Not found in spec | No | No | Not found in spec |
| **Cross-Protocol Budget Tracking** | No | No | No | No | No |
| **MCP Integration** | Sample servers shipped (all roles) [1] | Yes (transport binding, merged) [2] | Yes (transport spec) [7] | Yes (transport spec) [8] | Yes (checkout + cart bindings) [9] |
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
│          IntentMandate → CartMandate → PaymentMandate   │
│          Cryptographic signing, VDCs, accountability    │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                   SETTLEMENT LAYER                      │
│                                                         │
│   x402 (Coinbase)              MPP (Tempo+Stripe)       │
│   HTTP 402 + stablecoin        HTTP 402 + multi-rail    │
│   EIP-3009 permits             Challenge/credential/    │
│   Per-request micropayments    receipt flow             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

ACP and UCP are alternatives at the commerce layer. AP2 is the authorization layer that plugs into UCP (and could theoretically plug into ACP). x402 and MPP compete at settlement. AP2 sits above both and works regardless of which settlement protocol wins.

**The gap nobody fills:** cross-protocol budget tracking. An agent that shops via UCP, pays for APIs via MPP, and settles via x402 has no unified spending verification across all three. AP2 v0.2 added a single-mandate `budget` field (max + currency), but that governs one mandate — not spending across merchants or protocols. Each protocol still tracks its own transactions in isolation.

## Budget Reservation Protocol (Draft)

The [Budget Reservation Protocol](specs/budget-reservation-protocol.md) formalizes a spending enforcement layer that sits between authorization and settlement. Four verbs (`authorize`, `commit`, `refund`, `query`), one budget, any payment rail.

This pattern emerged independently across AP2, x402, MPP, OpenClaw, and ACP communities in April–May 2026. The spec documents the convergence.

## Protocol Details

See individual protocol pages for deeper analysis:

- [AP2](protocols/ap2.md) (Google)
- [ACP](protocols/acp.md) (OpenAI + Stripe)
- [x402](protocols/x402.md) (Coinbase)
- [MPP](protocols/mpp.md) (Tempo + Stripe)
- [UCP](protocols/ucp.md) (Google + Shopify)

## Sources

[1] AP2 spec `docs/ap2/specification.md` + `payment_mandate.md` (budget field), MCP sample servers in `code/samples/python/src/roles` — [github.com/google-agentic-commerce/AP2](https://github.com/google-agentic-commerce/AP2)

[2] ACP `rfcs/rfc.delegate_payment.md` Section 3.5 (Allowance schema) — [github.com/agentic-commerce-protocol/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)

[3] x402 spec "Document Scope" — explicitly lists "Client-side budget management" as out of scope — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402)

[4] MPP core spec `draft-httpauth-payment-00.md` — no delegation or budget concepts — [github.com/tempoxyz/mpp-specs](https://github.com/tempoxyz/mpp-specs)

[5] UCP `docs/specification/ap2-mandates.md` — AP2 mandate extension — [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp)

[6] AP2 core spec donated to the FIDO Alliance, 2026-04-28 — [fidoalliance.org](https://fidoalliance.org/fido-alliance-to-develop-standards-for-trusted-ai-agent-interactions/)

[7] x402 `specs/transports-v2/mcp.md` — MCP transport specification — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402)

[8] MPP `specs/extensions/transports/draft-payment-transport-mcp-00.md` — MCP transport binding

[9] UCP `docs/specification/checkout-mcp.md` — MCP checkout binding

## Contributing

Found an error? Protocol shipped an update? Open an issue or PR. Every claim must cite a primary source (spec document, GitHub repo, official blog post).

## About

Maintained by [Eric Tsang](https://linkedin.com/in/erictsang). I build agent payment infrastructure across AP2, x402, MPP, and MCP.

*Last verified: 2026-06-01*
