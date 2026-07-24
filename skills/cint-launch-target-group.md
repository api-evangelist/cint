---
name: Launch a survey Target Group on the Cint Exchange
description: Create a project and target group, check feasibility, then launch a fielding run to buy market-research sample on the Cint Exchange Demand API.
api: openapi/cint-demand-openapi.yml
operations:
- create_project
- create_target_group
- calculate_target_group_feasibility_by_id
- get_target_group_rate_card
- create_launch_fielding_run_from_draft_job
- launch_now_fielding_run
- get_fielding_run
---

# Launch a survey Target Group on the Cint Exchange

Use the Cint Exchange Demand API (`https://api.cint.com/v1`) to buy sample.

## Auth & headers (every request)
- `Authorization: Bearer <JWT>` — credentials come from the onboarding Starter Kit.
- `Cint-API-Version: 2025-12-18` — required date version header.
- On every `POST`, send `Idempotency-Key: <ULID or UUID>` so retries are safe.

## Steps
1. **Create a project** — `create_project` (`POST /demand/accounts/{account_id}/projects`). Keep the returned `project_id`.
2. **Create a target group** — `create_target_group` (`POST .../projects/{project_id}/target-groups`) with your quotas, locale, and profiling. Keep `target_group_id`.
3. **Check feasibility** — `calculate_target_group_feasibility_by_id` (`POST .../target-groups/{target_group_id}/calculate-feasibility`) to confirm the sample is achievable before spending.
4. **Review cost** — `get_target_group_rate_card` (`GET .../target-groups/{target_group_id}/rate-card`).
5. **Launch fielding** — `create_launch_fielding_run_from_draft_job` (`POST .../fielding-run-jobs/launch-from-draft`); this is an async job, poll `get_launch_fielding_run_from_draft_job`. To go live immediately on an existing run use `launch_now_fielding_run`.
6. **Monitor** — `get_fielding_run` for status; pause/resume/relaunch with `pause_fielding_run` / `resume_fielding_run` / `relaunch_fielding_run`.

## Rules
- Handle `429` by honoring the `Retry-After` header.
- Use `If-Match` (ETag) on updates to avoid clobbering concurrent edits.
- Errors are JSON with `object`, `detail`, and `invalid_params[]` — see `errors/cint-problem-types.yml`.
