---
name: Run an on-demand pay withdrawal
description: Check a member's earned-wage balance and complete an on-demand ("Embedded") Pay withdrawal using the Onsi Partner API two-step intent/complete flow.
api: openapi/onsi-openapi-original.json
operations: [getMemberBalance, createWithdrawalIntent, completeWithdrawal, getMemberWithdrawals]
---

# Run an on-demand pay withdrawal

Onsi Embedded Pay lets a member withdraw earned wages early. Withdrawals are a
two-step flow: create an intent, then complete it. Base URL
`https://api.onsi.com`; `x-api-key` header required. Money is an `{amount,
currency}` object where `amount` is in the currency's minor unit (e.g. pence).

## Rules
- Put an `x-idempotency-key` on `createWithdrawalIntent` and
  `completeWithdrawal` so a network retry does not create or complete a
  withdrawal twice.
- Respect the balance limits: an intent below the minimum returns
  `AmountBelowLowerLimit` (with `lowerLimit`); above the maximum returns
  `AmountAboveUpperLimit` (with `upperLimit`); insufficient funds return
  `MemberInsufficientFunds`.

## Steps
1. `getMemberBalance` (`GET /v1/pay/members/{memberId}/balance`) — read the
   available balance plus lower/upper withdrawal limits and fee.
2. `createWithdrawalIntent` (`POST /v1/pay/withdrawals/intent`) — create an
   intent for a valid amount; returns the net amount and fee.
3. `completeWithdrawal` (`POST /v1/pay/withdrawals/complete`) — confirm the
   intent to execute the payout.
4. `getMemberWithdrawals` (`GET /v1/pay/members/{memberId}/withdrawals`) —
   list the member's withdrawals to confirm and reconcile.
