---
name: Triage Riot credential breach exposure
description: >-
  Enumerate the credential breaches affecting a Riot workspace, rank them by criticality and impact, and pull
  the compromised employees and breached accounts for the ones that matter.
api: openapi/riot-public-api-openapi.yml
generated: '2026-08-05'
method: generated
operations:
  - breaches_get_paginated_FAUE35Y
  - breaches_get_statistics_FAUE35Y
  - breaches_get_breach_compromised_employees_FAUE35Y
  - employees_get_LRY7OLI
  - organizations_get_XEBQFJQ
---

# Triage Riot credential breach exposure

Riot monitors third-party breaches for credentials belonging to the customer's employees. This skill reads
that exposure and turns it into a triage list. **The API is read-only here** — there is no operation to
acknowledge a breach, warn an employee, or force a password reset. Those actions happen in the Riot portal.
Say so rather than implying you can remediate.

## Before you call anything

- **Auth.** `x-api-key: <key>` on every request. Needs the `breach:read` scope, plus `workspace:read` to
  enrich employees.
- **Tenancy.** Resolve `workspace_id` once via `organizations_get_XEBQFJQ`.
- **Base URL.** `https://public-api.tryriot.com/v1`

## Steps

1. **Resolve the workspace.** `organizations_get_XEBQFJQ` (`GET /v1/organization`) → `workspaces[].id`.

2. **Get the exposure baseline.** `breaches_get_statistics_FAUE35Y`
   (`GET /v1/breaches/statistics`) with `workspace_id`. It accepts `breached_after` and `breached_before`,
   so use it to answer "what changed this quarter" without paging the full list.

3. **List the breaches.** `breaches_get_paginated_FAUE35Y` (`GET /v1/breaches`) with `workspace_id`. It
   accepts a `status` filter. Each breach carries:
   - `name`, `domain` — the breached third party
   - `criticality` — Riot's severity assessment
   - `status` — where it sits in the workflow
   - `impacted`, `warned`, `acknowledged` — **integer counts**, not booleans. `impacted - warned` is your
     backlog.
   - `last_breached_at`, `created_at`, `updated_at`

4. **Rank before you drill down.** Sort by `criticality`, then by `impacted`, then by how much of
   `impacted` is still unwarned. Do not page every breach's employee list — most breaches will not warrant
   it, and each one is a separate paginated call.

5. **Pull compromised employees for the top breaches.**
   `breaches_get_breach_compromised_employees_FAUE35Y`
   (`GET /v1/breaches/{breach_id}/compromised-employees`) with `breach_id` and `workspace_id`. It accepts
   `warned` and `acknowledged` filters — **use them** to fetch only the untreated population instead of
   filtering client-side. Each row carries:
   - `employee` — id, name, username, primary_email_address
   - `breached_accounts[]` — each with `email` and `compromised_at`
   - `compromised_at`, `warned_at`, `acknowledged_at`

   An employee can appear with several `breached_accounts` when more than one of their addresses was in the
   dump. Count *employees*, not accounts, when you report exposure.
   A `404 Breach not found` here returns the flat legacy shape `{"error":"Breach not found"}`, **not** the
   `errors[]` envelope — handle both.

6. **Enrich the priority cases.** `employees_get_LRY7OLI` (`GET /v1/employees/{employee_id}`) gives
   department, manager and `cyber_posture`, so you can route the warning to the right person. It also
   returns `google_user_id`, `microsoft_user_id`, `okta_user_id` and `slack_user_id` — use those to drive a
   session revocation or forced reset in the IdP, which is where remediation actually happens.

## Pagination

Cursor-based. `limit` max **100** (default 50); pass `metadata.next_cursor` back as `cursor` unchanged;
stop on `null`.

## Errors

`{"errors":[{"code","title","detail","source":{"pointer"}}]}` — not RFC 9457.
`401` invalid key · `403` wrong workspace or missing `breach:read` · `404` breach not found (flat shape) ·
`422` bad parameter (check `source.pointer`) · `429` rate limited, no `Retry-After` published so back off
exponentially. See `errors/riot-error-codes.yml`.

## Retries and idempotency

All `GET`s, all safe to retry. No request-level `Idempotency-Key` exists — do not send one.

## Privacy

Breach data names individual employees and their compromised email addresses. Treat it as confidential
security material: report counts and criticality broadly, and share the per-employee list only with the
people doing the remediation.
