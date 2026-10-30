# Metered Call Guard

Three tools let an agent call an allowed x402 resource with a policy check first and an on-chain record after. The hosted server at `https://mcp.veyanet.tech/mcp` lists them; a self-hosted server lists them only when the Metered Call Guard is turned on (`VEYA_PAID_CALLS=1`).

| Tool | What it does |
|---|---|
| [`veya_paid_discover`](tools.md#veya_paid_discover) | Lists allowed resources, their input fields, a live quote and the limits. Never pays. |
| [`veya_paid_call`](tools.md#veya_paid_call) | Checks the call against the policy, then pays once, gets 2-of-3 validator signatures and records a commitment on Veya.sol. |
| [`veya_paid_run`](tools.md#veya_paid_run) | Lists or reads your runs, and resumes a pending one. Never pays again. |

## Flow

1. `veya_paid_discover` → pick a `resourceId`, read its `input` fields and `policyPreview`.
2. Confirm with the user: the resource, the quoted amount and that their wallets will be used.
3. `veya_paid_call` with `apiKey`, `taskId`, a new `idempotencyKey`, `resourceId` and `input`.
4. On `decision: "blocked"`, report the `code` and `reason`. Nothing was signed.
5. On a pending state, `veya_paid_run { runId, resume: true }`. Reuse the same `idempotencyKey` only to retry the same payment.

## Limits

Read them live from `veya_paid_discover` (`limits`). On the hosted server: 0.01 USDC per-call cap, 0.02 USDC largest total per call, 0.04 USDC task budget maximum, 2 calls per task. The kill switch is operator-only; while it is on, every call is refused and nothing is signed.

## Keys

Wallet keys are passed per call, used in memory for that call only, and never stored, logged or returned. Use dedicated wallets with small balances.
