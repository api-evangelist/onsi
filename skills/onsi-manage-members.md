---
name: Manage benefit-program members
description: Invite, read, update, replace, and offboard members in an Onsi benefit programme using the Onsi (BMO) Partner API.
api: openapi/onsi-openapi-original.json
operations: [inviteMember, getMember, getMembers, updateMember, replaceMember, removeMember, getTiers]
---

# Manage benefit-program members

Use the Onsi Partner API to keep a partner's member list in sync. Base URL
`https://api.onsi.com` (sandbox `https://api.onsi.dev`). Authenticate every
request with the `x-api-key` header and send `Content-Type: application/json`.

## Rules
- Add `x-idempotency-key` (a UUID) to every mutating call (`inviteMember`,
  `updateMember`, `replaceMember`, `removeMember`). Keys are honored for 7 days,
  so a safe retry returns the original result instead of duplicating work.
- Member ids are prefixed `mem_`. A member is `invited`, then `active`, then
  `inactive` once offboarded.
- Branch on the error `code` field, never the `message`. Handle `MemberNotFound`,
  `MemberAlreadyInvited`, `InvalidAddMemberPayload`, and `HeaderValidation`.

## Steps
1. `getTiers` — list valid tier names before assigning one to a member.
2. `inviteMember` (`POST /v1/members`) — invite a new worker with their details
   and tier. Duplicate invites return `MemberAlreadyInvited`.
3. `getMember` (`GET /v1/members/{id}`) — read current state by member id.
4. `getMembers` (`GET /v1/members`) — search/list with `skip`/`take` offset
   pagination; filter by `memberIds`, `statuses`, `emails`, or `workerIds`.
5. `updateMember` (`PATCH /v1/members/{id}`) — change specific attributes
   (e.g. tier). Set `accountDetails` to `null` to unset bank details.
6. `replaceMember` (`PUT /v1/members/{id}`) — supply the entire desired member
   state; re-invites inactive members.
7. `removeMember` (`DELETE /v1/members/{id}`) — offboard a member.
