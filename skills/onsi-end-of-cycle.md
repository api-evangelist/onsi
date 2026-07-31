---
name: Process end-of-cycle pay deductions
description: Retrieve pay cycles and their deductions at end of cycle so they can be reconciled with a payroll system, and upload a pay run, using the Onsi Partner API.
api: openapi/onsi-openapi-original.json
operations: [getPayCycles, getPayCycle, getPayCycleDeductions, uploadPayRun]
---

# Process end-of-cycle pay deductions

At the end of each pay cycle, retrieve the withdrawals members made so the
amounts can be deducted in your payroll. Base URL `https://api.onsi.com`;
`x-api-key` header required. Pay cycle ids are prefixed and returned latest-first.

## Rules
- List endpoints use `skip`/`take` offset pagination.
- Put an `x-idempotency-key` on `uploadPayRun` so a retry does not submit the
  same pay run twice.
- `getPayCycles` returns the latest pay cycle first; filter by `statuses`.

## Steps
1. `getPayCycles` (`GET /v1/pay/cycles`) — find the cycle(s) to process.
2. `getPayCycle` (`GET /v1/pay/cycles/{id}`) — read a specific cycle's dates and
   status.
3. `getPayCycleDeductions` (`GET /v1/pay/cycles/{id}/deductions`) — retrieve the
   per-member deductions to apply in payroll.
4. `uploadPayRun` (`POST /v1/pay/run`) — upload the partner pay run that feeds
   members' available balances for the next cycle.
