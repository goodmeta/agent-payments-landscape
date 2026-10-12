# MPP — Machine Payments Protocol

**Creator:** Tempo Labs + Stripe
**Repos:** [mpp-specs](https://github.com/tempoxyz/mpp-specs) (specs) · SDKs: [mppx](https://github.com/wevm/mppx) (TS), [pympp](https://github.com/tempoxyz/pympp) (Python), [mpp-rs](https://github.com/tempoxyz/mpp-rs) (Rust), [mpp-go](https://github.com/tempoxyz/mpp-go) (Go), [mpp-rb](https://github.com/stripe/mpp-rb) (Ruby), [solana-foundation/pay-kit](https://github.com/solana-foundation/pay-kit) (Solana method, multi-language)
**Version:** Individual Internet-Draft `draft-ryan-httpauth-payment-01` (posted 2026-03-18). Datatracker now shows it **expired and archived**; it was never adopted into an IETF working group or stream. The spec continues in the `mpp-specs` repo.
**License:** Specs CC0; SDKs vary (mppx, mpp-go, mpp-rb MIT; pympp + mpp-rs Apache-2.0)
**Authors:** Brendan Ryan, Jake Moxey, Tom Meagher (Tempo), Jeff Weinstein, Steve Kaliski (Stripe)

## What It Is

Settlement layer using HTTP 402 with a challenge/credential/receipt flow. Like x402 but multi-rail by design: supports stablecoins (Tempo), cards (Stripe), Lightning (Bitcoin), and chain-specific methods (Solana, Stellar, XRPL and others). Published as an individual Internet-Draft, now expired; not on an IETF standards track.

## Core Concepts

- **Challenge** — server returns 402 with `WWW-Authenticate: Payment` specifying intent, amount, method
- **Credential** — client signs and returns payment proof in `Authorization: Payment` header
- **Receipt** — server confirms settlement in `Payment-Receipt` response header
- **Accept-Payment** — client-side header (modeled on `Accept-Language`) declaring supported payment methods and preferences. Reduces wasted server computation. Defined in the core spec.
- **Intents** — the type of payment. `charge` (one-time) and `subscription` (recurring, merged 2026-07-29) are core intents on main. Session specs exist per payment method: Tempo, Lightning, EVM, Hedera, Solana and XRPL. The `authorize` intent PR was closed without merging.
- **Methods** — payment-method specs on main: card, evm (unified charge, merged 2026-04-17; session, merged 2026-07-03), hedera (merged 2026-05-14), lightning, nearintents, solana, stellar, stripe, tempo, usdc, xrpl. Initia still open; TON NanoPay was closed without merge.
- **Extensions** — discovery (OpenAPI), MCP transport, community extensions

## What It Covers

- Per-request payments (charge intent)
- Client payment method negotiation (`Accept-Payment` header)
- Multi-rail settlement (payment-method specs for cards, Stripe, Lightning, and several chains)
- MCP transport binding (pay-per-tool-call)
- Split payments (multi-recipient in single charge)
- Service discovery via OpenAPI

## What It Doesn't Cover

- Agent delegation or mandates in the core spec (no concept of "who authorized this agent")
- General spending limits. The core 402 exchange is stateless. Two narrower limits exist at the intent/method level: the `subscription` intent is "a recurring payment authorization for a fixed amount charged once per billing period" (Tempo implements it with a delegated key carrying a per-period spending limit), and sessions cap exposure with a prepaid deposit.
- Cross-service or cross-merchant spending coordination
- Pre-authorization with delayed capture — the `authorize` intent PR (#226) was closed without merging

## In Development (open PRs)

- **Solana subscription** ([PR #310](https://github.com/tempoxyz/mpp-specs/pull/310)) and **Solana session passkey** ([PR #271](https://github.com/tempoxyz/mpp-specs/pull/271))
- **Initia** chain method ([PR #222](https://github.com/tempoxyz/mpp-specs/pull/222))

**Recently merged:** Subscription intent + Stripe and Tempo implementations ([#230](https://github.com/tempoxyz/mpp-specs/pull/230), 2026-07-29), EVM session ([#225](https://github.com/tempoxyz/mpp-specs/pull/225), 2026-07-03 — payment channels with EIP-712 vouchers and an escrow contract), XRPL method, Near Intents and USDC payment methods, unified EVM charge (#213, 2026-04-17), Hedera charge (2026-05-14), challenge-binding security guidance (#233, 2026-04-16), Stripe Connect settlement docs (2026-05-20). **Closed without merge:** the `authorize` intent ([#226](https://github.com/tempoxyz/mpp-specs/pull/226), 2026-07-29), WebSocket transport ([#212](https://github.com/tempoxyz/mpp-specs/pull/212), 2026-07-29), the first Solana subscription proposal ([#270](https://github.com/tempoxyz/mpp-specs/pull/270), 2026-07-13; replaced by #310), and TON NanoPay (#214).

**Note on "machine-token":** support for machine-token charge payments landed in the Rust SDK ([mpp-rs #385](https://github.com/tempoxyz/mpp-rs/pull/385), merged 2026-08-14) under the existing `tempo` method. It is not a separate payment-method spec — the string `machine-token` does not appear anywhere in `mpp-specs` on main.

## Relationship to Other Protocols

- **Competes with x402** at the settlement layer (multi-rail vs mostly on-chain)
- **x402 compatibility, per MPP's own docs:** the mpp.dev FAQ says "The x402 exact flow maps onto MPP's charge intent. mppx can run x402-compatible EVM charges inline so one endpoint serves both MPP and x402 clients." That is a docs claim, not spec text.
- **Independent of AP2/ACP/UCP** — the specs do not mention them; settlement only, no commerce or authorization layer

## Sources

- [Core spec](https://github.com/tempoxyz/mpp-specs/blob/main/specs/core/draft-httpauth-payment-01.md)
- [Charge intent](https://github.com/tempoxyz/mpp-specs/blob/main/specs/intents/draft-payment-intent-charge-00.md)
- [Subscription intent](https://github.com/tempoxyz/mpp-specs/blob/main/specs/intents/draft-payment-intent-subscription-00.md)
- [Payment methods](https://github.com/tempoxyz/mpp-specs/tree/main/specs/methods)
- [MCP transport](https://github.com/tempoxyz/mpp-specs/blob/main/specs/extensions/transports/draft-payment-transport-mcp-00.md)
- [IETF draft (datatracker, expired)](https://datatracker.ietf.org/doc/draft-ryan-httpauth-payment/)
- [mpp.dev FAQ](https://mpp.dev/faq) — x402 compatibility statement (text in [`tempoxyz/mpp` `src/components/FaqStructuredData.tsx`](https://github.com/tempoxyz/mpp/blob/main/src/components/FaqStructuredData.tsx))

*Last verified: 2026-10-12*
