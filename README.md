# Blng

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

BLNG is an AI-driven creative suite for the jewelry industry, giving jewelers, designers, brands, and retailers tools to explore, refine, and present designs fast and without compromise. Its Design product turns sketches, doodles, photos, illustrations, or text prompts into photorealistic renderings on web and iOS; Studio transforms CAD models into market-ready marketing photos and videos at scale; and Retail enables live, made-to-order design in-store. BLNG is a Speedinvest portfolio company. BLNG publishes no developer program, but the web application is backed by three first-party REST APIs whose OpenAPI definitions are served publicly and unauthenticated behind Swagger UI on its own hosts — a Journey API for design journeys, prompts, image assets and 3D model generation, a User API for users, workspaces, invitations, organizations and subscriptions, and a Billing API for Stripe-backed products, checkout and subscription webhooks. All three authenticate with AWS Cognito bearer tokens issued at auth.app.blng.ai.

**Areas:** Company · Jewelry · Generative AI · Design · Creative Tools · Rendering · Marketing · Retail · 3D Models · Image Generation · Workspace · Billing

## Blng on APIs.io

Everything profiled for Blng is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/blng/](https://apis.io/providers/blng/)** — or go straight to one of the APIs or properties below.

### APIs (14)

- [BLNG User API](https://apis.io/apis/blng/blng-user-api/) — REST API that manages BLNG users, roles and permissions, workspaces and workspace members, workspace and subscription invitations, organizations, SSO…
- [BLNG Billing API](https://apis.io/apis/blng/blng-billing-api/) — REST API that manages BLNG payment and billing — listing Stripe products and prices, creating Stripe checkout sessions and customer-portal sessions,…
- [Blng Chat Prompts API](https://apis.io/apis/blng/blng-chat-prompts-api/) — The Chat Prompts API from Blng — 2 operation(s) for chat prompts.
- [Blng Composite API](https://apis.io/apis/blng/blng-composite-api/) — The Composite API from Blng — 2 operation(s) for composite.
- [Blng Design Journey API](https://apis.io/apis/blng/blng-design-journey-api/) — The Design Journey API from Blng — 16 operation(s) for design journey.
- [Blng Invitation API](https://apis.io/apis/blng/blng-invitation-api/) — The Invitation API from Blng — 5 operation(s) for invitation.
- [Blng Journeys API](https://apis.io/apis/blng/blng-journeys-api/) — The Journeys API from Blng — 6 operation(s) for journeys.
- [Blng Organization API](https://apis.io/apis/blng/blng-organization-api/) — The Organization API from Blng — 3 operation(s) for organization.
- [Blng Prompts API](https://apis.io/apis/blng/blng-prompts-api/) — The Prompts API from Blng — 4 operation(s) for prompts.
- [Blng Subscription API](https://apis.io/apis/blng/blng-subscription-api/) — The Subscription API from Blng — 4 operation(s) for subscription.
- [Blng User Subscription API](https://apis.io/apis/blng/blng-user-subscription-api/) — The User Subscription API from Blng — 3 operation(s) for user subscription.
- [Blng User Tooltip API](https://apis.io/apis/blng/blng-user-tooltip-api/) — The User Tooltip API from Blng — 2 operation(s) for user tooltip.
- [Blng Webhook API](https://apis.io/apis/blng/blng-webhook-api/) — The Webhook API from Blng — 3 operation(s) for webhook.
- [Blng Workspace API](https://apis.io/apis/blng/blng-workspace-api/) — The Workspace API from Blng — 15 operation(s) for workspace.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Blng Webhooks](https://apis.io/asyncapis/blng/blng-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [blng-mcp.yml](https://apis.io/servers/blng/blng-mcp-yml/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Blng Agentic Access](https://apis.io/agentic-access/blng/blng-agentic-access/)

#### Overlays (3)

OpenAPI overlays, including conversational phrasing for each operation.

- [Blng Billing Api Overlay](https://apis.io/overlays/blng/blng-billing-api-overlay/)
- [Blng Journey Api Overlay](https://apis.io/overlays/blng/blng-journey-api-overlay/)
- [Blng User Api Overlay](https://apis.io/overlays/blng/blng-user-api-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Blng Authentication](https://apis.io/security/blng/blng-authentication/)
- [Blng Domain Security](https://apis.io/security/blng/blng-domain-security/)
- [Blng Trust Center](https://apis.io/security/blng/blng-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Blng Scopes](https://apis.io/scopes/blng/blng-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Blng Plans Pricing](https://apis.io/plans/blng/blng-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Blng Rate Limits](https://apis.io/rate-limits/blng/blng-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://blng.ai)
- [Documentation](https://docs.blng.ai/)
- [Getting started](https://docs.blng.ai/getting-started/)
- [Pricing](https://blng.ai/pricing)
- [Sign up](https://app.blng.ai/)
- [Blog](https://blng.ai/news)
- [Trust centre](https://trust.blng.ai/)
- [Terms of service](https://blng.ai/terms)
- [Privacy policy](https://blng.ai/privacy-policy)
- [GitHub](https://github.com/blng-ai)
- [Status](https://status.blng.ai/)

## API Evangelist rating

**Kin Score 50.5** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 71.4 |
| Contract quality | 49.7 |
| Contract governance | 4.5 |
| Operational transparency | 23.7 |
| Developer ergonomics | 49.4 |
| Access clarity | 71.1 |

**Agent readiness 30.3** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | documented (partial credit) |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | partial |
| Rate Limit Signal | derived (partial credit) |
| Event Surface Described | derived (partial credit) |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | — |
| Operation Distinctiveness | distinct |

**Regulatory alignment 36.4** — AI & Biometrics

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/blng/](https://apis.io/providers/blng/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Blng: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/blng/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
