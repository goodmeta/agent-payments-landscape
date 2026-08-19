# Agent Payments Landscape

A living comparison of protocols shaping how AI agents transact: AP2, ACP, x402, MPP, and UCP.

This is a neutral, practitioner-maintained reference. No protocol advocacy. Every claim cites a primary source.

## Protocol Comparison

| Dimension | AP2 | ACP | x402 | MPP | UCP |
|-----------|-----|-----|------|-----|-----|
| **Creator** | Google (spec → FIDO Alliance) | OpenAI + Stripe + Meta | Coinbase (now x402 Foundation) | Tempo Labs + Stripe | Co-governed (Google, Shopify + many) |
| **Maturity** | v0.2.0 (spec + Python SDK; repo unmerged since 2026-04-29) | Beta (5 releases, latest 2026-04-17) | V2 (SDKs: TS v2.22.0, Go v2.22.0, Python v2.19.0) | IETF draft-01 | v2026-04-08 (latest tagged) |
| **Layer** | Authorization | Commerce (checkout) | Settlement | Settlement | Commerce (full-stack) |
| **Payment Rails** | Cards; all rails planned | Cards (delegate payment tokens); Razorpay UPI/S2S proposed (open SEPs) | Stablecoins on 14+ chains (Base, Ethereum, Polygon, Solana, Algorand, Aptos, Hedera, Stellar, Sui, Keeta, TON, NEAR, Cardano, Concordium) | Multi-rail: 10 method specs on main (Tempo, Stripe, Lightning, Card, Solana, Stellar, EVM, Hedera, Near Intents, USDC) | Rail-agnostic (pluggable payment handlers) |
| **Agent Delegation** | Yes. Two mandate types: Checkout Mandate + Payment Mandate [1] | Yes. Delegate Payment API with Allowance constraints [2] | No (explicitly out of scope) [3] | No [4] | Yes (via AP2 mandates extension) [5] |
| **Budget / Spending Limits** | Yes. Payment Mandate `budget` field (max + currency), single-mandate [1] | Yes. Allowance object: max_amount, currency, expires_at, scoped to merchant_id [2] | Not in the protocol (still out of scope [3]). TypeScript SDK only: `spendControls` per-payment USD cap + asset allowlist, no running total [10] | No [4] | Yes (inherited from AP2 mandates) [5] |
| **Cross-Merchant Coordination** | Not yet (Issue #207) | Not found in spec | No | No | Not found in spec |
| **Cross-Protocol Budget Tracking** | No | No | No | No | No |
| **MCP Integration** | Sample servers shipped (all roles) [1] | Yes (transport binding, merged) [2] | Yes (transport spec) [7] | Yes (transport spec) [8] | Yes (checkout + cart bindings) [9] |
| **Post-execution Evidence (third-party verifiable)** | Not found in spec | Not found in spec | Not found in spec | Not found in spec | Not found in spec |
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

**A second, adjacent gap:** none of the five specs define a post-execution record that a third party can verify without trusting the merchant, the agent operator, or the protocol's own logs. Settlement proofs (the on-chain transaction, the signed mandate) prove *that a payment cleared* — none commit to a content-addressed, independently-recomputable record of *what the agent was authorized to do and whether that matches what happened*. This is a different layer than settlement or budget tracking: it's evidence, checkable after the fact by someone who wasn't part of the transaction. action_ref/negotiation_ref (IETF draft-etcheverry-action-ref) is one open, permissionless-anchored implementation of this layer, with worked examples against real code from eight tools in the broader agent-payments space [11].

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

[3] x402 `specs/x402-specification-v2.md` "Out of Scope" — still lists "Client-side budget management" as out of scope on main — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402/blob/main/specs/x402-specification-v2.md)

[4] MPP core spec `draft-httpauth-payment-00.md` — no delegation or budget concepts — [github.com/tempoxyz/mpp-specs](https://github.com/tempoxyz/mpp-specs)

[5] UCP `docs/specification/ap2-mandates.md` — AP2 mandate extension — [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp)

[6] AP2 core spec donated to the FIDO Alliance, 2026-04-28 — [fidoalliance.org](https://fidoalliance.org/fido-alliance-to-develop-standards-for-trusted-ai-agent-interactions/)

[7] x402 `specs/transports-v2/mcp.md` — MCP transport specification — [github.com/x402-foundation/x402](https://github.com/x402-foundation/x402)

[8] MPP `specs/extensions/transports/draft-payment-transport-mcp-00.md` — MCP transport binding

[9] UCP `docs/specification/checkout-mcp.md` — MCP checkout binding

[10] x402 `docs/sdk-features.md` + buyer quickstart — `spendControls` client safety feature, TypeScript only (PR [#3124](https://github.com/x402-foundation/x402/pull/3124), [#3147](https://github.com/x402-foundation/x402/pull/3147), merged 2026-08-13)

[11] `action_ref`/`negotiation_ref` spec — [argentum-core/docs/spec/action-ref.md](https://github.com/giskard09/argentum-core/blob/main/docs/spec/action-ref.md), IETF `draft-etcheverry-action-ref`. Independent conformance suite (57/57 blind-tested by a separate implementer). Worked examples anchored on Base mainnet against real code from eight agent-payment tools: [giskard09/agent-accountability-landscape](https://github.com/giskard09/agent-accountability-landscape).

## Contributing

Found an error? Protocol shipped an update? Open an issue or PR. Every claim must cite a primary source (spec document, GitHub repo, official blog post).

## About

Maintained by [Eric Tsang](https://linkedin.com/in/erictsang). I build agent payment infrastructure across AP2, x402, MPP, and MCP.

*Last verified: 2026-08-18 (all five protocols).*
