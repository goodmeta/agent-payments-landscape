# MPP — Machine Payments Protocol

**Creator:** Tempo Labs + Stripe
**Repos:** [mpp-specs](https://github.com/tempoxyz/mpp-specs) (specs) · SDKs: [mppx](https://github.com/wevm/mppx) (TS), [pympp](https://github.com/tempoxyz/pympp) (Python), [mpp-rs](https://github.com/tempoxyz/mpp-rs) (Rust), [solana-foundation/pay-kit](https://github.com/solana-foundation/pay-kit) (Solana method, multi-language)
**Version:** IETF Internet-Draft (draft-ryan-httpauth-payment-01, submitted 2026-03-18)
**License:** Specs CC0; SDKs vary (mppx MIT; pympp + mpp-rs Apache-2.0)
**Authors:** Brendan Ryan, Jake Moxey, Tom Meagher (Tempo), Jeff Weinstein, Steve Kaliski (Stripe)

## What It Is

Settlement layer using HTTP 402 with a challenge/credential/receipt flow. Like x402 but multi-rail by design: supports stablecoins (Tempo), cards (Stripe), Lightning (Bitcoin), and chain-specific methods (Solana, Stellar). IETF-track standardization.

## Core Concepts

- **Challenge** — server returns 402 with `WWW-Authenticate: Payment` specifying intent, amount, method
- **Credential** — client signs and returns payment proof in `Authorization: Payment` header
- **Receipt** — server confirms settlement in `Payment-Receipt` response header
- **Accept-Payment** — client-side header (modeled on `Accept-Language`) declaring supported payment methods and preferences. Reduces wasted server computation. Merged Apr 13.
- **Intents** — the type of payment. `charge` (one-time) is formalized; Tempo and Lightning also have merged `session` intents on main. `authorize` (PR #226) and `subscription` (PR #230) remain open.
- **Methods** — 8 payment-method specs on main: card, evm (unified charge, merged 2026-04-17), hedera (merged 2026-05-14), lightning (charge + session), solana, stellar, stripe, tempo (charge + session). Initia still open; TON NanoPay was closed without merge.
- **Extensions** — discovery (OpenAPI), MCP transport, community extensions

## What It Covers

- Per-request payments (charge intent)
- Client payment method negotiation (`Accept-Payment` header)
- Multi-rail settlement (8 payment-method specs on main)
- MCP transport binding (pay-per-tool-call)
- Split payments (multi-recipient in single charge)
- Service discovery via OpenAPI

## What It Doesn't Cover

- Agent delegation or mandates (no concept of "who authorized this agent")
- Budget or spending limits (each 402 is stateless and independent)
- Cross-service spending coordination
- Authorize and subscription intents (open PRs #226 / #230, not merged)

## In Development (open PRs)

- **Authorization intent** ([PR #226](https://github.com/tempoxyz/mpp-specs/pull/226)) — pre-authorization with spending caps and delayed capture
- **Subscription intent** ([PR #230](https://github.com/tempoxyz/mpp-specs/pull/230)) — recurring periodic charges
- **EVM session** ([PR #225](https://github.com/tempoxyz/mpp-specs/pull/225)) — streaming payment channels, voucher-based, EIP-712 signatures, escrow contract
- **WebSocket transport** ([PR #212](https://github.com/tempoxyz/mpp-specs/pull/212)) — session intent over JSON-RPC 2.0 / WebSocket
- **Solana subscription** ([PR #270](https://github.com/tempoxyz/mpp-specs/pull/270)) and **Solana session passkey** ([PR #271](https://github.com/tempoxyz/mpp-specs/pull/271))
- **Initia** chain method ([PR #222](https://github.com/tempoxyz/mpp-specs/pull/222))

**Recently merged:** Unified EVM charge (#213, 2026-04-17), Hedera charge (2026-05-14), challenge-binding security guidance (#233, 2026-04-16), Stripe Connect settlement docs (2026-05-20). TON NanoPay (#214) was closed without merge.

## Relationship to Other Protocols

- **Competes with x402** at the settlement layer (multi-rail vs stablecoin-only)
- **Independent of AP2/ACP/UCP** — settlement only, no commerce or authorization layer
- If MPP becomes the IETF standard, x402 likely gets absorbed (MPP claims backward compatibility)

## Sources

- [Core spec](https://github.com/tempoxyz/mpp-specs/blob/main/specs/core/draft-httpauth-payment-00.md)
- [Charge intent](https://github.com/tempoxyz/mpp-specs/blob/main/specs/intents/draft-payment-intent-charge-00.md)
- [Payment methods](https://github.com/tempoxyz/mpp-specs/tree/main/specs/methods)
- [MCP transport](https://github.com/tempoxyz/mpp-specs/blob/main/specs/extensions/transports/draft-payment-transport-mcp-00.md)
- [IETF draft (datatracker)](https://datatracker.ietf.org/doc/draft-ryan-httpauth-payment/)

*Last verified: 2026-06-01*
