# Riot

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Riot is a Paris-based employee security posture management (human risk management) platform that helps companies reduce the human attack surface. The product suite spans phishing and smishing simulation, security awareness training courses, credential breach monitoring, employee-reported email triage (Inbox), inbound email protection (Slash), and third-party SaaS/drive exposure monitoring (Sonar), fronted by a chat assistant that runs in Slack, Microsoft Teams and the web portal. Riot publishes a public REST API (OpenAPI 3.1.1, x-api-key authentication, cursor pagination, scoped keys) that exposes organization, employee, group, course, campaign, attack, breach and inbox data, a SCIM 2.0 provisioning surface for user and group lifecycle, and Standard-Webhooks server-to-server events whose payloads follow the OCSF Detection Finding schema so they can be ingested by a SIEM or SOAR without custom mapping.

**Areas:** Cybersecurity · Security Awareness · Human Risk Management · Phishing Simulation · employee-security · Security Posture Management · breach-detection · Email Security · SaaS Security · SCIM · Webhook · OCSF

## Riot on APIs.io

Everything profiled for Riot is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/riot/](https://apis.io/providers/riot/)** — or go straight to one of the APIs or properties below.

### APIs (9)

- [Riot Awareness API](https://apis.io/apis/riot/riot-awareness-api/) — Provides data about courses, their settings within the workspace and employees' learning progress.
- [Riot Breaches API](https://apis.io/apis/riot/riot-breaches-api/) — The Breaches API from Riot — 3 operation(s) for breaches.
- [Riot General API](https://apis.io/apis/riot/riot-general-api/) — Provides an overview of the organization and employees' data.
- [Riot Groups API](https://apis.io/apis/riot/riot-groups-api/) — The Groups API from Riot — 2 operation(s) for groups.
- [Riot Inbox API](https://apis.io/apis/riot/riot-inbox-api/) — The Inbox API from Riot — 3 operation(s) for inbox.
- [Riot SCIM API](https://apis.io/apis/riot/riot-scim-api/) — The SCIM API from Riot — 9 operation(s) for scim.
- [Riot Simulation API](https://apis.io/apis/riot/riot-simulation-api/) — Provides data about phishing campaigns, corresponding attacks and related events.
- [Riot Slash API](https://apis.io/apis/riot/riot-slash-api/) — The Slash API from Riot — 2 operation(s) for slash.
- [Riot Sonar API](https://apis.io/apis/riot/riot-sonar-api/) — The Sonar API from Riot — 0 operation(s) for sonar.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Riot Webhooks](https://apis.io/asyncapis/riot/riot-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [riot](https://apis.io/servers/riot/riot/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Riot Agentic Access](https://apis.io/agentic-access/riot/riot-agentic-access/)

#### Collections (14)

Postman and Open Collection request collections.

- [Riot Awareness API](https://apis.io/collections/riot/open-riot-awareness-api/)
- [Riot Breaches API](https://apis.io/collections/riot/open-riot-breaches-api/)
- [Riot General API](https://apis.io/collections/riot/open-riot-general-api/)
- [Riot Groups API](https://apis.io/collections/riot/open-riot-groups-api/)
- [Riot Inbox API](https://apis.io/collections/riot/open-riot-inbox-api/)
- [Riot SCIM API](https://apis.io/collections/riot/open-riot-scim-api/)
- [Riot Simulation API](https://apis.io/collections/riot/open-riot-simulation-api/)
- [Riot Slash API](https://apis.io/collections/riot/open-riot-slash-api/)
- [Riot Sonar API](https://apis.io/collections/riot/open-riot-sonar-api/)
- [Riot Team awareness API](https://apis.io/collections/riot/open-riot-team-awareness-api/)
- [Riot Team inbox API](https://apis.io/collections/riot/open-riot-team-inbox-api/)
- [Riot Team platform API](https://apis.io/collections/riot/open-riot-team-platform-api/)
- [Riot Team simulation API](https://apis.io/collections/riot/open-riot-team-simulation-api/)
- [Riot Webhook Events API](https://apis.io/collections/riot/open-riot-webhook-events-api/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Riot Public Api Overlay](https://apis.io/overlays/riot/riot-public-api-overlay/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Riot Authentication](https://apis.io/security/riot/riot-authentication/)
- [Riot Domain Security](https://apis.io/security/riot/riot-domain-security/)
- [Riot Trust Center](https://apis.io/security/riot/riot-trust-center/)
- [Riot Vulnerability Disclosure](https://apis.io/security/riot/riot-vulnerability-disclosure/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Riot Scopes](https://apis.io/scopes/riot/riot-scopes/)

#### Rate limits (1)

Published request limits.

- [Riot Rate Limits](https://apis.io/rate-limits/riot/riot-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://tryriot.com/)
- [Documentation](https://docs.tryriot.com/)
- [API reference](https://docs.tryriot.com/)
- [Pricing](https://tryriot.com/pricing/)
- [Support](https://tryriot.com/support/)
- [Changelog](https://tryriot.com/changelog/)
- [Blog](https://tryriot.com/blog/)
- [Security](https://tryriot.com/security/)
- [Compliance](https://trust.tryriot.com/)
- [Terms of service](https://tryriot.com/terms/)
- [Privacy policy](https://tryriot.com/privacy/)
- [GitHub](https://github.com/tryriot)
- [Status](https://status.tryriot.com/)

## API Evangelist rating

**Kin Score 52.9** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 66.1 |
| Contract quality | 60.8 |
| Contract governance | 18.2 |
| Operational transparency | 44.7 |
| Developer ergonomics | 47.0 |
| Access clarity | 40.8 |

**Agent readiness 40.0** — agent-native

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | paginated |
| Operation Distinctiveness | distinct |

**Regulatory alignment 38.2** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/riot/](https://apis.io/providers/riot/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Riot: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/riot/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
