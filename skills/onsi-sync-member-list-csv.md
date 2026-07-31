---
name: Sync a full member list via CSV job
description: Reconcile a partner's complete member list with Onsi using a full-list CSV upload or a batch action job, then poll the job to completion.
api: openapi/onsi-openapi-original.json
operations: [createJobFromCsvV2, createJobFromCsv, createJob, getJob]
---

# Sync a full member list via CSV job

Bulk member changes run as asynchronous jobs that return a `jobId` you poll.
Base URL `https://api.onsi.com`; `x-api-key` header required.

## Rules
- Choose one approach:
  - **Full-list CSV** (`createJobFromCsvV2`, `PUT /v2/members/jobs/csv`) — send
    your complete current member list; Onsi diffs it and invites, updates,
    re-invites, or offboards members to match. V2 removes existing bank details
    when bank columns are present but empty (V1 preserves them). This is
    destructive if the CSV does not reflect your true desired state.
  - **Batch actions** (`createJob`, `POST /v1/members/jobs`) — send an explicit
    array of per-member actions when you already know the deltas. Recommended
    for precise control.
- Add an `x-idempotency-key` to the submission so a retry does not double-apply.
- File-processing failures surface as `FileContent` errors carrying the `row`.

## Steps
1. Submit the job: `createJobFromCsvV2` (full list) or `createJob` (batch
   actions). Both return `202 Accepted` with a `jobId`.
2. `getJob` (`GET /v1/members/jobs/{jobId}`) — poll until the job status shows
   completion; inspect any per-row errors.
