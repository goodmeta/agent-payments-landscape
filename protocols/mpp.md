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
- **Intents** — the type of payment. Only `charge` (one-time) is formalized on main. `authorize`, `subscription`, and `session/stream` exist on branches.
- **Methods** — payment rail implementations: Tempo, Stripe, Lightning, Card, Solana, Stellar
- **Extensions** — discovery (OpenAPI), MCP transport, community extensions

## What It Covers

- Per-request payments (charge intent)
- Multi-rail settlement (6 payment methods)
- MCP transport binding (pay-per-tool-call)
- Split payments (multi-recipient in single charge)
- Service discovery via OpenAPI

## What It Doesn't Cover

- Agent delegation or mandates (no concept of "who authorized this agent")
- Budget or spending limits (each 402 is stateless and independent)
- Cross-service spending coordination
- Authorize/subscription/session intents (in development on branches, not merged)

## In Development (on branches, not merged)

- `add-authorize-intent` — pre-authorization with spending caps and delayed capture
- `add-subscription-intent` — recurring periodic charges
- `add-tempo-stream` — pay-per-token streaming

## Relationship to Other Protocols

- **Competes with x402** at the settlement layer (multi-rail vs stablecoin-only)
- **Independent of AP2/ACP/UCP** — settlement only, no commerce or authorization layer
- If MPP becomes the IETF standard, x402 likely gets absorbed (MPP claims backward compatibility)

## Sources

- [Core spec](https://github.com/tempoxyz/mpp-specs/blob/main/specs/core/draft-httpauth-payment-00.md)
- [Charge intent](https://github.com/tempoxyz/mpp-specs/blob/main/specs/intents/draft-payment-intent-charge-00.md)
- [Payment methods](https://github.com/tempoxyz/mpp-specs/tree/main/specs/methods)
- [MCP transport](https://github.com/tempoxyz/mpp-specs/blob/main/specs/extensions/transports/draft-payment-transport-mcp-00.md)

*Last verified: 2026-03-31*
