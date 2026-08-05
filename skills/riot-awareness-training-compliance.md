---
name: Report Riot awareness training compliance
description: >-
  Build a security awareness training compliance report from Riot — which courses are in the program, who has
  completed what, per-year status, and which employees or groups are behind.
api: openapi/riot-public-api-openapi.yml
generated: '2026-08-05'
method: generated
operations:
  - courses_get_paginated_DJESCNQ
  - courses_get_statistics_DJESCNQ
  - courses_get_employees_progress_DJESCNQ
  - courses_get_course_statuses_of_employees_DJESCNQ
  - groups_get_paginated_BOILCUA
  - groups_get_group_employees_BOILCUA
  - organizations_get_XEBQFJQ
---

# Report Riot awareness training compliance

This is the flow behind an auditor's question: *can you show me that your staff completed security
awareness training?* Riot's Awareness product holds the answer. **Read-only** — you cannot assign a course,
send a reminder, or mark a completion through the public API.

## Before you call anything

- **Auth.** `x-api-key: <key>`. Needs `awareness:read`, plus `workspace:read` for employees and groups.
- **Tenancy.** Resolve `workspace_id` once via `organizations_get_XEBQFJQ`.
- **Base URL.** `https://public-api.tryriot.com/v1`

## Steps

1. **Resolve the workspace.** `organizations_get_XEBQFJQ` (`GET /v1/organization`).

2. **List the program.** `courses_get_paginated_DJESCNQ` (`GET /v1/courses`) with `workspace_id`. Returns
   `id`, `name`, `slug`, `description`. **`name` and `description` are localized to the workspace's default
   locale** — if you are producing a report for a different audience, say which locale the strings came
   from rather than translating them silently. Note that this endpoint is paginated even though the list is
   short; do not assume one page.

3. **Get the headline compliance rate.** `courses_get_statistics_DJESCNQ`
   (`GET /v1/courses/statistics`) with `workspace_id`. Use this as the top-line number.

4. **Choose your axis.** There are two progress views and they answer different questions:
   - **By employee, across all courses** — `courses_get_employees_progress_DJESCNQ`
     (`GET /v1/courses/employees_progress`). Use this for "who is behind".
   - **By course, across all employees** — `courses_get_course_statuses_of_employees_DJESCNQ`
     (`GET /v1/courses/{course_id}`). Use this for "how did the OWASP course land".

   Pick one and page it fully. Do not fan out a per-course call for every employee.

5. **Read the status rows correctly.** Each progress row carries:
   - `employee` — id, name, username, primary_email_address
   - `status` — the employee's course status
   - `quiz_score` — **nullable string**, not a number. Guard before comparing.
   - `years[]` — per-year status history. Annual training is a recurring obligation, so **compliance is
     per-year**. Report against the year the auditor asked about, not the latest status.

   A `404` here means `{"error":"Course is not included in the program"}` — the flat legacy shape, not the
   `errors[]` envelope. It means the course exists in Riot's catalog but is not part of this workspace's
   program.

6. **Break down by group when asked.** `groups_get_paginated_BOILCUA` (`GET /v1/groups`) lists groups;
   `groups_get_group_employees_BOILCUA` (`GET /v1/groups/{group_id}/employees`) lists their members. Join
   membership to progress locally — there is no group-scoped progress endpoint.

## Pagination

Cursor-based on every list. `limit` max **100** (default 50). Pass `metadata.next_cursor` back as `cursor`
unchanged; stop on `null`. For a full-workspace compliance export, always use `limit=100` and page to
exhaustion — a partial page silently understates non-compliance, which is the one error that matters here.

## Errors

`{"errors":[{"code","title","detail","source":{"pointer"}}]}` — not RFC 9457.
`401` · `403` (wrong workspace / missing `awareness:read`) · `404` course not in program (flat shape) ·
`422` (read `source.pointer`) · `429` rate limited, no `Retry-After` published. See
`errors/riot-error-codes.yml`.

## Retries and idempotency

All `GET`s, safe to retry. No request-level `Idempotency-Key` exists.

## Reporting honestly

If any page failed or you stopped early, say so and state the coverage. A compliance report built on a
truncated export is worse than no report.
