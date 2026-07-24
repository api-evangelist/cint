---
name: Reconcile completes on the Cint Exchange
description: Submit and track reconciliations for completed interviews on the Cint Exchange Demand API, including reason codes and downloadable reconciliation reports.
api: openapi/cint-demand-openapi.yml
operations:
- get_eligible_target_groups
- request_reconciliation_reason_codes
- post_reconciliations
- post_reconciliations_completes
- get_reconciliation_by_request_id
- get_target_groups_by_request_id
- get_recon_downloads
---

# Reconcile completes on the Cint Exchange

Reconciliation lets you dispute/adjust completed interviews after fielding.

## Auth & headers
- `Authorization: Bearer <JWT>`, `Cint-API-Version: 2025-12-18`, `Idempotency-Key` on POSTs.

## Steps
1. **Find eligible target groups** — `get_eligible_target_groups` (`GET .../reconciliations/eligible-target-groups`).
2. **Get reason codes** — `request_reconciliation_reason_codes` (`GET .../reconciliations/reason-codes`) for valid rejection reasons.
3. **Submit a reconciliation** — `post_reconciliations` (`POST .../reconciliations`) or per-project completes with `post_reconciliations_completes` (`POST .../projects/{project_id}/reconciliations/completes`). Keep the `request_id`.
4. **Track status** — `get_reconciliation_by_request_id`, then `get_target_groups_by_request_id` for per-target-group results.
5. **Download results** — `get_recon_downloads` (`GET .../reconciliations/{request_id}/downloads/{download_type}`).

## Rules
- Reconciliation is asynchronous — poll the `request_id` rather than blocking.
- Use documented reason codes only; invalid values return a `422` with `invalid_params[]`.
