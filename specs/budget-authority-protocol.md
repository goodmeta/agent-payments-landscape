# Budget Authority Protocol — v0.1 (Draft)

A minimal protocol for cross-service agent spending enforcement. Four verbs. One budget. Any payment rail.

## The Three Layers

```
┌─────────────────────────────────────────────────────┐
│  Identity + Delegation                              │
│  AP2 mandates, Agent Passport System, ERC-8004      │
│  "Who authorized this agent?"                       │
├─────────────────────────────────────────────────────┤
│  Budget Authority (this spec)                       │
│  "Is this spend within the authorized limit?"       │
├─────────────────────────────────────────────────────┤
│  Settlement                                         │
│  x402, MPP, ACP Allowance, UCP, cards, bank         │
│  "Move the money."                                  │
└─────────────────────────────────────────────────────┘
```

Each layer references the others but does not embed them:
- A delegation without a budget has no spending limit.
- A budget without a delegation has no proof of authorization.
- Settlement without either is what got the Bankr agent drained ($115K, May 4 2026).

## Problem

Five agent payment protocols exist (AP2, ACP, x402, MPP, UCP). Each handles settlement independently. An agent with a $500 budget calling three services gets three independent approvals — nobody tracks the total. The agent overspends, and nobody detects it until the wallet is empty.

On May 4, 2026, this became tangible: an AI agent on Base lost $115K through a prompt injection attack (Bankr incident). The model was tricked into authorizing a transfer. No spending limit existed between the model's decision and the payment execution.

The budget authority sits between the agent and the payment rail. Even if the agent is compromised, spending is bounded.

## Architecture

```
Principal (human/org)
  │
  └── creates budget (amount, expiry, constraints)
         │                    [requires principal credential]
         │
Agent ───┤
  │      ├── authorize(budget_id, amount, vendor) → hold_id
  │      │     [requires agent credential]
  │      │     [atomic: check + decrement + hold]
  │      │
  │      ├── [agent makes payment via any rail: x402, card, MPP, bank]
  │      │
  │      ├── commit(hold_id) → confirmed
  │      │     [on payment success]
  │      │
  │      └── refund(hold_id) → released
  │            [on payment failure]
  │
Budget Authority (stateful, single source of truth)
```

The budget authority is rail-agnostic. It does not move money. It tracks how much has been authorized, held, committed, and refunded against a budget. The payment rail handles settlement independently.

### Credential Separation

Budget creation requires a **principal credential** (the human or org setting the limit). Authorize/commit/refund require an **agent credential** (the agent spending within the limit). These MUST be different credentials. A compromised agent with the principal credential can create its own budgets — defeating the purpose.

| Operation | Credential | Holder |
|-----------|-----------|--------|
| create budget | principal key | Human, org, orchestrator |
| authorize, commit, refund, query | agent key | Spending agent |

## Verbs

### `authorize`

Atomically check the budget and place a hold. There is no separate "check remaining" call — the read and decrement are one operation. This prevents race conditions when multiple agents or services draw from the same budget concurrently.

```
Request:
  budget_id         string    required
  amount            integer   required   (minor currency units)
  vendor            string    required
  idempotency_key   string    required   (caller-generated, UUID)

Response (approved):
  approved          true
  hold_id           string
  remaining         integer              (after this hold)
  protocol_version  string               ("0.1")

Response (denied):
  approved          false
  error             string               (error code, see below)
  reason            string               (human-readable detail)
  remaining         integer
```

Idempotency: retrying with the same `idempotency_key` returns the original hold without double-decrementing.

### `commit`

Confirm a hold after successful payment. The held amount becomes permanently spent.

A `commit` on an expired hold MUST still succeed if the hold record exists. This prevents budget divergence when the payment rail is slow (3DS challenge, network congestion) and the hold TTL expires before the payment confirms. Money already moved — the budget must reflect it.

```
Request:
  hold_id           string    required

Response:
  settled           true
```

### `refund`

Release a hold when payment fails. The held amount is restored to the budget.

```
Request:
  hold_id           string    required

Response:
  released          true
```

### `query`

Check budget state without modifying it.

```
Request:
  budget_id         string    required

Response:
  budget_id         string
  total             integer
  spent             integer   (committed)
  held              integer   (authorized but not yet committed)
  remaining         integer   (total - spent - held)
```

## Error Codes

Structured error codes for the `error` field in denied responses:

| Code | Meaning |
|------|---------|
| `BUDGET_EXCEEDED` | Amount exceeds remaining budget |
| `HOLD_EXPIRED` | Hold TTL elapsed (for commit/refund on expired holds) |
| `HOLD_NOT_FOUND` | Unknown hold_id |
| `BUDGET_NOT_FOUND` | Unknown budget_id |
| `VENDOR_BLOCKED` | Vendor is on the blocklist |
| `AMOUNT_EXCEEDED` | Amount exceeds per-transaction max |
| `BUDGET_EXPIRED` | Budget validity window has passed |
| `UNAUTHORIZED` | Invalid or missing credential |

## Hold Lifecycle

```
authorize() ──► HELD ──► commit() ──► COMMITTED
                  │
                  └──► refund() ──► RELEASED
                  │
                  └──► [TTL expires] ──► EXPIRED
                         │
                         └──► commit() ──► COMMITTED
                               [late commit MUST succeed
                                if hold record exists]
```

Holds have a TTL (implementation-defined, recommended 5 minutes). If neither `commit` nor `refund` is called, the hold expires and the budget is restored. This handles process crashes between authorize and settlement.

Implementations SHOULD retain expired hold records for at least 24 hours to allow late commits. A late commit on an expired hold re-debits the budget. This is preferable to budget divergence (money moved, budget not debited).

## Budget Creation

Budgets are created by the principal (human or orchestrating agent), not by the spending agent. The creation endpoint requires a principal credential distinct from the agent credential.

```
Request:
  agent_id          string    required
  amount            integer   required   (minor currency units)
  currency          string    required
  valid_until       string    required   (ISO 8601)
  constraints       object    optional   (max per-tx, vendor allow/blocklist)

Response:
  budget_id         string
  protocol_version  string               ("0.1")
```

## Transport

The protocol is transport-agnostic. Known implementations:

| Transport | How |
|-----------|-----|
| HTTP REST | `POST /authorize`, `POST /commit`, `POST /refund`, `GET /query` |
| MCP | Tools: `check_budget`, `settle`, `get_budget` |
| On-chain | Smart contract with `transferWithMandate` |

## Audit Trail

Each verb (authorize, commit, refund) SHOULD produce a timestamped log entry. The query verb is read-only and does not require logging.

Minimum fields per log entry:

```
  timestamp         string    (ISO 8601)
  verb              string    (authorize | commit | refund)
  budget_id         string
  hold_id           string
  amount            integer
  vendor            string
  result            string    (approved | denied | settled | released)
```

This log is the basis for compliance audit. Enterprises adopting agent spending will require audit trails for regulatory review (e.g., SOC 2 automated transaction controls). The log format is intentionally minimal — implementations MAY extend it with additional fields (agent_id, payment_rail, transaction_hash) without breaking conformance.

## Conformance

A conformant implementation MUST support:
- `authorize` with atomic check + hold
- `commit` and `refund` on held records
- `commit` on expired holds (late commit)
- Idempotency on `authorize` via `idempotency_key`
- Credential separation between budget creation and spending

A conformant implementation SHOULD support:
- `query` for budget state inspection
- Hold TTL with automatic expiry
- Audit trail logging per verb
- Structured error codes from the error code table

## Known Implementations

| Implementation | Transport | Status |
|----------------|-----------|--------|
| [agent-verifier](https://github.com/goodmeta/agent-verifier) (npm) | HTTP REST | Reference — budget creation, hold/settle, audit |
| [agent-verifier-mcp](https://github.com/goodmeta/agent-verifier-mcp) | MCP (stdio + HTTP) | Reference — 4 tools for Claude Code / MCP clients |
| [demo-agent](https://github.com/goodmeta/demo-agent) | HTTP client | Demo — real x402 payments to Exa with budget enforcement |
| AP2 #252 sample | Python (in-process) | Approved sample in google-agentic-commerce/AP2 repo |
| x402 mandate-bound (proposed) | On-chain (EVM) | Draft — Rivier token contract with constraint enforcement |

## Roadmap (v0.2)

The following are out of scope for v0.1 but identified for future versions:
- Hierarchical budgets (org → department → team → agent)
- Delegation chains and sub-budgets for multi-agent orchestration
- Budget period resets (daily, weekly, monthly)
- Portable signed attestations (JWT/VC) alongside the live API
- Full RFC 2119 normative language

Contributions welcome — file an issue on this repo.

## Convergence

This spec formalizes a pattern that emerged independently across multiple protocol communities in April–May 2026:

| Thread | Pattern |
|--------|---------|
| google-agentic-commerce/AP2#207 | Cross-merchant budget enforcement, verb set proposal |
| google-agentic-commerce/AP2#252 | Reference implementation with hold lifecycle |
| x402-foundation/x402#2176 | On-chain `mandate-bound` scheme with constraint enforcement |
| second-state/payment-skill#31 | SpendEnvelope with post-quantum signatures |
| agentic-commerce-protocol#231 | Governance-layer binding with signed delegation + receipts |
| paygraph-ai/paygraph#25 | ExternalBudgetGuard wrapper for cross-wallet enforcement |

## Status

Draft v0.1. Not affiliated with any standards body. Published for reference by implementers converging on this pattern.

Feedback: file an issue on this repo or comment on any of the threads above.

## License

CC BY 4.0
