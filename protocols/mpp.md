# MPP — Machine Payments Protocol

**Creator:** Tempo Labs + Stripe
**Repos:** [github.com/tempoxyz/mpp-specs](https://github.com/tempoxyz/mpp-specs) (specs) / [github.com/wevm/mppx](https://github.com/wevm/mppx) (TypeScript SDK)
**Version:** IETF Internet-Draft (draft-httpauth-payment-00)
**License:** CC0 (specs), MIT (mppx SDK)
**Authors:** Brendan Ryan, Jake Moxey, Tom Meagher (Tempo), Jeff Weinstein, Steve Kaliski (Stripe)

## What It Is

Settlement layer using HTTP 402 with a challenge/credential/receipt flow. Like x402 but multi-rail by design: supports stablecoins (Tempo), cards (Stripe), Lightning (Bitcoin), and chain-specific methods (Solana, Stellar). IETF-track standardization.

## Core Concepts

- **Challenge** — server returns 402 with `WWW-Authenticate: Payment` specifying intent, amount, method
- **Credential** — client signs and returns payment proof in `Authorization: Payment` header
- **Receipt** — server confirms settlement in `Payment-Receipt` response header
- **Accept-Payment** — client-side header (modeled on `Accept-Language`) declaring supported payment methods and preferences. Reduces wasted server computation. Merged Apr 13.
- **Intents** — the type of payment. Only `charge` (one-time) is formalized on main. `authorize`, `subscription`, and `session/stream` in active draft PRs.
- **Methods** — payment rail implementations: Tempo, Stripe, Lightning, Card, Solana, Stellar. Unified EVM charge, TON NanoPay, and Initia methods in draft PRs.
- **Extensions** — discovery (OpenAPI), MCP transport, community extensions

## What It Covers

- Per-request payments (charge intent)
- Client payment method negotiation (`Accept-Payment` header)
- Multi-rail settlement (6+ payment methods, more in draft)
- MCP transport binding (pay-per-tool-call)
- Split payments (multi-recipient in single charge)
- Service discovery via OpenAPI

## What It Doesn't Cover

- Agent delegation or mandates (no concept of "who authorized this agent")
- Budget or spending limits (each 402 is stateless and independent)
- Cross-service spending coordination
- Authorize/subscription/session intents (in development on branches, not merged)

## In Development (draft PRs, not merged)

- **Authorization intent** ([PR #226](https://github.com/tempoxyz/mpp-specs/pull/226)) — pre-authorization with spending caps and delayed capture
- **Subscription intent** ([PR #230](https://github.com/tempoxyz/mpp-specs/pull/230)) — recurring periodic charges
- **EVM session** ([PR #225](https://github.com/tempoxyz/mpp-specs/pull/225)) — streaming payment channels, voucher-based, EIP-712 signatures, escrow contract
- **WebSocket transport** ([PR #212](https://github.com/tempoxyz/mpp-specs/pull/212)) — session intent over JSON-RPC 2.0 / WebSocket
- **Security guidance** ([PR #233](https://github.com/tempoxyz/mpp-specs/pull/233)) — challenge-binding secrets, rotation overlap
- **New chain methods** — Unified EVM charge (#213), TON NanoPay (#214), Initia (#222)

## Relationship to Other Protocols

- **Competes with x402** at the settlement layer (multi-rail vs stablecoin-only)
- **Independent of AP2/ACP/UCP** — settlement only, no commerce or authorization layer
- If MPP becomes the IETF standard, x402 likely gets absorbed (MPP claims backward compatibility)

## Sources

- [Core spec](https://github.com/tempoxyz/mpp-specs/blob/main/specs/core/draft-httpauth-payment-00.md)
- [Charge intent](https://github.com/tempoxyz/mpp-specs/blob/main/specs/intents/draft-payment-intent-charge-00.md)
- [Payment methods](https://github.com/tempoxyz/mpp-specs/tree/main/specs/methods)
- [MCP transport](https://github.com/tempoxyz/mpp-specs/blob/main/specs/extensions/transports/draft-payment-transport-mcp-00.md)

*Last verified: 2026-04-14*
