# Agent Payments Landscape

A living comparison of protocols shaping how AI agents transact: AP2, ACP, x402, MPP, and UCP.

This is a neutral, practitioner-maintained reference. No protocol advocacy. Every claim cites a primary source.

## Protocol Comparison

| Dimension | AP2 | ACP | x402 | MPP | UCP |
|-----------|-----|-----|------|-----|-----|
| **Creator** | Google | OpenAI + Stripe | Coinbase | Tempo Labs + Stripe | Google (+ Shopify) |
| **Maturity** | V0.1 (spec + samples) | Beta (4 releases) | V2 (production SDKs) | IETF draft-00 | Pre-release draft |
| **Layer** | Authorization | Commerce (checkout) | Settlement | Settlement | Commerce (full-stack) |
| **Payment Rails** | Cards (v0.1); all rails planned for v1.x | Cards (delegate payment tokens) | Stablecoins (Base, Ethereum, Polygon, Solana, Algorand, Aptos, Hedera, Stellar, Sui) | Multi-rail (Tempo stablecoins, Stripe cards, Lightning, Solana, Stellar) | Rail-agnostic (pluggable payment handlers) |
| **Agent Delegation** | Yes. Three mandate types: IntentMandate, CartMandate, PaymentMandate [1] | Yes. Delegate Payment API with Allowance constraints [2] | No (explicitly out of scope) [3] | No [4] | Yes (via AP2 mandates extension) [5] |
| **Budget / Spending Limits** | Yes. IntentMandate constraints (natural language + merchant/SKU allowlists + expiry) [1] | Yes. Allowance object: max_amount, currency, expires_at, scoped to merchant_id [2] | No (explicitly out of scope) [3] | No [4] | Yes (inherited from AP2 mandates) [5] |
| **Cross-Merchant Coordination** | Not yet. Roadmap: "multi-merchant transaction topologies" [6] | Not found in spec | No | No | Not found in spec |
| **Cross-Protocol Budget Tracking** | No | No | No | No | No |
| **MCP Integration** | Planned (v1.x roadmap) [6] | No | Yes (transport spec) [7] | Yes (transport spec) [8] | Yes (transport binding) [9] |
| **License** | Apache 2.0 | Apache 2.0 | Apache 2.0 | CC0 (specs), Apache/MIT (code) | Apache 2.0 |

## How the Protocols Relate

```
┌─────────────────────────────────────────────────────────┐
│                    COMMERCE LAYER                        │
│                                                         │
│   ACP (OpenAI+Stripe)          UCP (Google+Shopify)     │
│   REST checkout sessions       Multi-transport commerce │
│   Delegate payment tokens      AP2 mandates extension   │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                  AUTHORIZATION LAYER                     │
│                                                         │
│                    AP2 (Google)                          │
│          IntentMandate → CartMandate → PaymentMandate   │
│          Cryptographic signing, VDCs, accountability    │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                   SETTLEMENT LAYER                       │
│                                                         │
│   x402 (Coinbase)              MPP (Tempo+Stripe)       │
│   HTTP 402 + stablecoin        HTTP 402 + multi-rail    │
│   EIP-3009 permits             Challenge/credential/    │
│   Per-request micropayments    receipt flow              │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

ACP and UCP are alternatives at the commerce layer. AP2 is the authorization layer that plugs into UCP (and could theoretically plug into ACP). x402 and MPP compete at settlement. AP2 sits above both and works regardless of which settlement protocol wins.

**The gap nobody fills:** cross-protocol budget tracking. An agent that shops via UCP, pays for APIs via MPP, and settles via x402 has no unified spending verification across all three. Each protocol tracks its own transactions in isolation.

## Protocol Details

See individual protocol pages for deeper analysis:

- [AP2](protocols/ap2.md) (Google)
- [ACP](protocols/acp.md) (OpenAI + Stripe)
- [x402](protocols/x402.md) (Coinbase)
- [MPP](protocols/mpp.md) (Tempo + Stripe)
- [UCP](protocols/ucp.md) (Google + Shopify)

## Sources

[1] AP2 spec `src/ap2/types/mandate.py` + `docs/specification.md` Section 4.1 — [github.com/google-agentic-commerce/AP2](https://github.com/google-agentic-commerce/AP2)

[2] ACP `rfcs/rfc.delegate_payment.md` Section 3.5 (Allowance schema) — [github.com/agentic-commerce-protocol/agentic-commerce-protocol](https://github.com/agentic-commerce-protocol/agentic-commerce-protocol)

[3] x402 spec "Document Scope" — explicitly lists "Client-side budget management" as out of scope — [github.com/coinbase/x402](https://github.com/coinbase/x402)

[4] MPP core spec `draft-httpauth-payment-00.md` — no delegation or budget concepts — [github.com/tempoxyz/mpp-specs](https://github.com/tempoxyz/mpp-specs)

[5] UCP `docs/specification/ap2-mandates.md` — AP2 mandate extension — [github.com/Universal-Commerce-Protocol/ucp](https://github.com/Universal-Commerce-Protocol/ucp)

[6] AP2 `docs/roadmap.md` — V1.x and Long-Term Vision sections

[7] x402 `specs/transports-v2/mcp.md` — MCP transport specification

[8] MPP `specs/extensions/transports/draft-payment-transport-mcp-00.md` — MCP transport binding

[9] UCP `docs/specification/checkout-mcp.md` — MCP checkout binding

## Contributing

Found an error? Protocol shipped an update? Open an issue or PR. Every claim must cite a primary source (spec document, GitHub repo, official blog post).

## About

Maintained by [Eric Tsang](https://linkedin.com/in/erictsang). I build agent payment infrastructure across AP2, x402, MPP, and MCP.

*Last verified: 2026-03-31*
