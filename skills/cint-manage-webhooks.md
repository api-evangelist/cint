---
name: Subscribe to Cint webhook event notifications
description: Register and manage Cint Exchange webhooks to receive target-group-updated, quota-fill-registered, and session-updated events, with signed delivery and delivery history.
api: openapi/cint-demand-openapi.yml
operations:
- create_webhook
- get_webhooks
- get_webhook
- update_webhook
- regenerate_webhook_secret
- list_webhook_events_history
- get_webhook_events
- delete_webhook
---

# Subscribe to Cint webhook event notifications

## Auth & headers
- `Authorization: Bearer <JWT>`, `Cint-API-Version: 2025-12-18`.
- `POST`/`PUT` should carry an `Idempotency-Key`.

## Steps
1. **Create a webhook** — `create_webhook` (`POST /accounts/{account_id}/webhooks`) with your endpoint URL and the event types you want: `target-group-updated`, `quota-fill-registered`, `session-updated`. Store the returned signing secret.
2. **Verify deliveries** — Cint signs each POST with the webhook secret; validate the signature on receipt.
3. **List / inspect** — `get_webhooks`, `get_webhook`; audit deliveries with `list_webhook_events_history` and query all events via `get_webhook_events` (`GET /accounts/{account_id}/webhooks-events`).
4. **Rotate the secret** — `regenerate_webhook_secret` (`POST .../webhooks/{webhook_id}/regenerate-secret`) when a secret leaks; update your verifier.
5. **Change or remove** — `update_webhook`, `delete_webhook`.

## Rules
- Respond `2xx` fast; process asynchronously.
- Use `list_webhook_events_history` to backfill after downtime rather than assuming at-least-once ordering.
- See event catalog in `asyncapi/cint-webhooks.yml`.
