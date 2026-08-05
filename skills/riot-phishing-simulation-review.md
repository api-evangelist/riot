---
name: Review a Riot phishing simulation campaign
description: >-
  Pull a Riot phishing simulation campaign, its per-employee attacks and the event trail for each attack, then
  identify which employees were tricked, which reported the attack, and how the workspace is trending.
api: openapi/riot-public-api-openapi.yml
generated: '2026-08-05'
method: generated
operations:
  - campaigns_get_paginated_CWCTX3I
  - campaigns_get_statistics_CWCTX3I
  - attacks_get_paginated_KCLEOEQ
  - employees_get_LRY7OLI
---

# Review a Riot phishing simulation campaign

Riot's Simulation product sends phishing (and smishing) exercises to employees and records what each
recipient did. This skill reads that data. **The public API is read-only for simulation — there is no
operation to create, launch or stop a campaign.** Do not tell a user you can run a simulation for them.

## Before you call anything

- **Auth.** Every request needs `x-api-key: <key>`. Keys are not self-service; the customer obtains one
  from Riot's technical team. See `authentication/riot-authentication.yml`.
- **Scope.** This flow needs the `simulation:read` scope, plus `workspace:read` if you resolve employees.
  A key scoped to one workspace gets `403` if you pass a different `workspace_id`.
- **Tenancy.** Almost every call takes `workspace_id`. Resolve it once with `organizations_get_XEBQFJQ`,
  which returns the organization and its `workspaces[]`.
- **Base URL.** `https://public-api.tryriot.com/v1`

## Steps

1. **Resolve the workspace.** Call `organizations_get_XEBQFJQ` (`GET /v1/organization`). Pick the
   `workspaces[].id` you want. Cache it — it does not change.

2. **List campaigns.** Call `campaigns_get_paginated_CWCTX3I` (`GET /v1/campaigns`) with `workspace_id`.
   Each campaign carries `status`, `delivery`, `frequency`, `launched_at`, `completed_at`, and a
   `cycles[]` array. A campaign is a recurring program; a **cycle** is one run of it. Attacks belong to a
   cycle, so keep `cycles[].id` alongside the campaign id.

3. **Get the headline numbers.** Call `campaigns_get_statistics_CWCTX3I`
   (`GET /v1/campaigns/statistics`) with `workspace_id`. Use this for reporting rather than counting
   attacks yourself — it is cheaper and is the number Riot's own dashboard shows.

4. **Pull the attacks.** Call `attacks_get_paginated_KCLEOEQ`
   (`GET /v1/campaigns/{campaign_id}/attacks`) with the campaign id and `workspace_id`. Each attack is one
   email to one employee and carries:
   - `employee` — id, name, username, primary_email_address
   - `template`, `sender`, `service`, `difficulty` — what was sent and how hard it was
   - `sent_at`, `reported_at`, `tricked_at` — the three timestamps that decide the outcome
   - `events[]` — the full behavioural trail

5. **Classify each attack from the timestamps, not from the events.** The three nullable timestamps are the
   authoritative outcome:
   - `tricked_at` non-null → the employee fell for it.
   - `reported_at` non-null → the employee reported it.
   - both null and `sent_at` non-null → delivered, no action.
   Use `events[]` for the *story* (what they clicked, in what order), not for the verdict.

6. **Read the event trail when you need detail.** `events[].type` is one of exactly these values:
   `attack_sent`, `email_opened`, `email_answered`, `download_link_clicked`, `attachment_opened`,
   `file_opened`, `credentials_submitted`, `employee_tricked`, `email_reported`, `manually_reported`,
   `manually_retried`, `attack_voided`. Treat any value outside this list as new and ignore it rather than
   failing.
   **`events[].data` is populated only for `credentials_submitted`.** It is null everywhere else — do not
   dereference it unconditionally.

7. **Enrich an at-risk employee.** For anyone with `tricked_at` set, call `employees_get_LRY7OLI`
   (`GET /v1/employees/{employee_id}`) to get their department, manager, `cyber_posture` and `aura_score`
   before you recommend an intervention.

## Pagination

Every list here is cursor-paginated. Send `limit` (max **100**, default 50). Read
`metadata.next_cursor` from the response and pass it back as `cursor` **unchanged**; stop when it is
`null`. A `link` header with `rel="next"` carries the same next-page URL if you would rather follow it.
Never construct a cursor yourself.

## Errors

Errors come back as `{"errors":[{"code","title","detail","source":{"pointer"}}]}` — **not** RFC 9457
problem+json.

- `401 unauthorized` — missing or invalid `x-api-key`.
- `403 forbidden` — valid key, wrong workspace or missing scope. Do not retry; re-scope the request.
- `422` — parameter validation failed; read `source.pointer` to find which one.
- `429 too_many_requests` — rate limited per key. **No `Retry-After` or `RateLimit-*` header is
  published**, so back off exponentially starting around 1s rather than reading a reset time.

Full catalog: `errors/riot-error-codes.yml`.

## Retries and idempotency

Every operation in this skill is a `GET` and is safe to retry. Riot publishes **no** request-level
`Idempotency-Key` — do not send one. See `conventions/riot-conventions.yml`.

## Privacy

This flow returns named employees, their email addresses and their security failures. It is sensitive HR-
adjacent data. Do not paste employee-level results into a shared channel; report aggregates and escalate
individuals privately.
