# Picarro

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

Picarro, Inc. is a Santa Clara, California company that builds high-precision gas concentration and stable-isotope analyzers based on Cavity Ring-Down Spectroscopy (CRDS), together with the cloud software and mobile platforms that turn those measurements into operational decisions. Its instruments measure more than 700 compounds — methane, ethane, CO2, N2O, ammonia, ethylene oxide, hydrogen peroxide, water isotopes and cleanroom-level airborne molecular contamination — at parts-per- billion and parts-per-trillion sensitivity. Picarro serves natural gas utilities (mobile methane leak detection and the Picarro Surveyor programme), sterilization and EtO facilities, refinery and chemical-plant fenceline monitoring, semiconductor cleanrooms and FOUP contamination control, pharmaceutical manufacturing, and atmospheric and earth-science research. The company reports 25+ years of operation, 5,000+ instruments installed in about 100 countries, and 77 patents. Its software side is P-Cubed, a cloud data-processing and storage platform with a customer-facing API for custom data transformation routines, a companion mobile app for field leak investigations, and — for semiconductor deployments — a publicly published gRPC/ProtoBuf interface (picarro/sam-foup-public) for external control and monitoring of Picarro Edge systems.

**Areas:** Company · Gas Detection · Environmental Monitoring · Emissions · Methane · Greenhouse Gas · Scientific Instruments · Semiconductors · Industrial IoT · Sensors · Analytics · gRPC

## Picarro on APIs.io

Everything profiled for Picarro is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/picarro/](https://apis.io/providers/picarro/)** — or go straight to one of the APIs or properties below.

### APIs (4)

- [Picarro Edge — SAM FOUP gRPC API](https://apis.io/apis/picarro/picarro-edge-sam-foup-grpc-api/) — The first-party gRPC/ProtoBuf interface Picarro publishes for external access to Picarro SAM (semiconductor airborne molecular contamination) systems. The…
- [Picarro Platform Server API](https://apis.io/apis/picarro/picarro-platform-server-api/) — The gRPC interface to the host computer underneath a Picarro SAM deployment. `platformserver` exposes NetConfig for network connectivity and Wi-Fi…
- [Picarro Identity (P-Cubed SSO)](https://apis.io/apis/picarro/picarro-identity-p-cubed-sso/) — The Keycloak-backed identity service that fronts the Picarro P-Cubed cloud platform. It publishes an anonymous OpenID Connect discovery document for the…
- [Picarro P-Cubed Platform](https://apis.io/apis/picarro/picarro-p-cubed-platform/) — P-Cubed is Picarro's cloud data processing and storage platform. Picarro analyzers stream or batch data to it, customers interact with the results in a…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Picarro Edge — SAM FOUP Signal Streams](https://apis.io/asyncapis/picarro/picarro-sam-foup-asyncapi/)

#### Event channels (6)

Individual events and webhooks the provider emits.

- [picarro.platform.netconfig.NetConfig/watch](https://apis.io/channels/picarro/picarro-platform-netconfig-netconfig-watch/)
- [picarro.platform.sysconfig.SysConfig/watch](https://apis.io/channels/picarro/picarro-platform-sysconfig-sysconfig-watch/)
- [picarro.platform.upgrade.Upgrade/watch](https://apis.io/channels/picarro/picarro-platform-upgrade-upgrade-watch/)
- [picarro.sam.controller.Controller/watch](https://apis.io/channels/picarro/picarro-sam-controller-controller-watch/)
- [picarro.sam.foup.FOUP/watch](https://apis.io/channels/picarro/picarro-sam-foup-foup-watch/)
- [picarro.sam.ms.MeasurementSet/watch](https://apis.io/channels/picarro/picarro-sam-ms-measurementset-watch/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Picarro Authentication](https://apis.io/security/picarro/picarro-authentication/)
- [Picarro Domain Security](https://apis.io/security/picarro/picarro-domain-security/)
- [Picarro Trust Center](https://apis.io/security/picarro/picarro-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Picarro Scopes](https://apis.io/scopes/picarro/picarro-scopes/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.picarro.com/)
- [Documentation](https://github.com/picarro/sam-foup-public/blob/master/README.md)
- [Getting started](https://github.com/picarro/sam-foup-public/blob/master/README.md#getting-started)
- [API reference](https://github.com/picarro/sam-foup-public/tree/master/proto)
- [Pricing](https://store.picarro.com/)
- [Sign up](https://www.picarro.com/user/register)
- [Support](https://www.picarro.com/support)
- [Blog](https://www.picarro.com/company/press_releases)
- [Compliance](https://www.picarro.com/ensuring_data_security_and_compliance)
- [Trust centre](https://trust.picarro.com/)
- [Terms of service](https://www.picarro.com/terms_of_service)
- [Privacy policy](https://www.picarro.com/privacy_policy)
- [GitHub](https://github.com/picarro)
- [Status](https://status.picarro.com/status/gas)

## API Evangelist rating

**Kin Score 45.5** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 62.5 |
| Contract quality | 39.6 |
| Contract governance | 4.5 |
| Operational transparency | 34.2 |
| Developer ergonomics | 42.3 |
| Access clarity | 60.5 |

**Agent readiness 30.8** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | served (partial credit) |
| Idempotency | — |
| Error Semantics | derived (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | — |
| Event Surface Described | derived (partial credit) |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | served (partial credit) |
| Protected Resource Metadata | — |
| Dynamic Client Registration | yes |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 38.5** — Energy & Utilities

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/picarro/](https://apis.io/providers/picarro/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Picarro: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/picarro/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
