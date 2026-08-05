---
name: Consume Riot webhook events
description: >-
  Verify and process Riot's Standard Webhooks server-to-server events — the OCSF Detection Finding emitted
  when a reported email is classified, and the Sonar request to revoke a third-party drive permission.
api: openapi/riot-public-api-openapi.yml
generated: '2026-08-05'
method: generated
operations:
  - InboxEmailAnalysisClassifiedWebhook
  - RevokeDriveItemPermissionRequestCreatedWebhook
  - reports_report_attack_from_message_id_DO4XYPA
  - inbox_tickets_get_inbox_statistics_QHKH7RI
---

# Consume Riot webhook events

Riot pushes server-to-server events to an HTTPS endpoint you host. There are exactly **two** event types
today. Endpoint registration and secret rotation are **not self-service** — the customer's account manager
adds them.

## Verify the signature before you do anything else

Riot follows the [Standard Webhooks](https://github.com/standard-webhooks/standard-webhooks)
specification, so a compatible library will handle this. If you implement it yourself:

1. Read the three headers: `webhook-id` (uuid), `webhook-timestamp` (unix seconds), `webhook-signature`.
2. Build the signed string exactly: `<webhook-id>.<webhook-timestamp>.<body>`.
3. HMAC-SHA256 it with the endpoint secret, base64-encode, and compare against the signature list.
4. `webhook-signature` is a **space-delimited list** of `v1,<base64>` entries — one per *active* secret,
   which is how zero-downtime rotation works. Accept the event if **any** entry matches. Comparing only the
   first will break during a rotation.

**Verify against the raw request body bytes.** Parsing and re-serializing the JSON changes the bytes and
invalidates the signature. Capture the raw body before your framework touches it.

Also check `webhook-timestamp` against your clock and reject stale deliveries, or a captured payload can be
replayed.

## Deduplicate on `webhook-id`

The **same `webhook-id` is sent on every retry** — Riot's docs name it as the idempotency key. Riot retries
up to **10 times over roughly 75 hours** (immediate, 5s, 5m, 30m, 2h, 5h, 10h, 14h, 20h, 24h), so your
dedupe store must retain ids for **at least 75 hours** or you will double-process a late retry.

## Respond fast

Success is **any 2xx within 15 seconds**. Anything else — non-2xx, connection error, timeout — is a
failure and starts the retry schedule. Acknowledge first, process asynchronously. Never do the downstream
work inline.

## The envelope

```json
{
  "type": "inbox_email_analysis.classified",
  "timestamp": "2026-06-03T08:42:11.812Z",
  "data": {}
}
```

`timestamp` is when the event was **created in Riot** — not the delivery attempt. The delivery time is the
`webhook-timestamp` header. Order your processing by `timestamp`, not by arrival.

## Event: `inbox_email_analysis.classified`

Fires every time an email reported to the Inbox is classified — **including reclassifications**, so treat
it as an upsert keyed on the finding, not an insert.

`data` is an **OCSF Detection Finding (class 2004, v1.4.0)**, which means you can forward it to a SIEM or
SOAR with no custom mapping. Constant discriminators: `class_uid: 2004`, `category_uid: 2` (Findings),
`activity_id: 1` (Create).

Enumerated values you can branch on:
- verdict — `fraudulent`, `safe`, `spam`
- detection type — `Phishing`, `Safe`, `Spam`
- OCSF severity — `High`, `Informational`, `Low`

The payload also carries `evidences[]` extracted from the reported email, plus `finding_info`, `actor` and
observables.

## Event: `revoke_drive_item_permission_request.created`

Fires when Riot decides a third-party drive permission should be revoked.

**This is a request for action, not a notification. Riot does not perform the revocation — you do.** If
nobody handles this event, the exposure stays open.

`data.provider` is `google` or `microsoft`. `data.provider_data` carries the identifiers to act on:
- `drive_item_id` (required)
- `permission_id` (required)
- `owner_id` (required)
- `shared_drive_id` (optional, Microsoft only)

Call the corresponding Google Drive or Microsoft Graph permission-delete API with those ids. Because the
event may be retried, make the revocation idempotent — a permission already removed should be treated as
success, not an error.

## Forward compatibility — this is a hard requirement

**Ignore unknown fields in `data`.** New fields are added at any time, without notice and without a version
bump. A strict parser that rejects unknown members will start failing on a Tuesday for no visible reason.

Riot's stated contract:
- **Not breaking:** adding a field; adding a new event type.
- **Breaking:** removing or renaming a field; changing a field's type; changing the meaning of an existing
  value.
- Breaking changes ship as a **new event type** (e.g. `inbox_email_analysis.classified.v2`); the original
  type is left unchanged.

So: switch on `type`, ignore types you do not know, and never assume `data` is closed.

## Related REST operations

- `reports_report_attack_from_message_id_DO4XYPA`
  (`POST /v1/email_reports/report_attack_from_message_id`) — report a suspicious email to the Inbox by its
  provider message id. This is the **only non-GET operation on the public API**, and there is **no
  request-level idempotency key**, so a blind retry can create a duplicate report. Retry only on a network
  failure with no response, and prefer to reconcile rather than re-POST.
- `inbox_tickets_get_inbox_statistics_QHKH7RI` (`GET /v1/inbox_tickets/statistics`) — aggregate Inbox
  numbers, useful to reconcile against what you have ingested.

## What has no events

Awareness, Simulation, Breaches and Slash emit **no** webhooks. Those surfaces must be polled through the
REST API. Do not tell a user they can be notified of a phishing click or a new breach.
