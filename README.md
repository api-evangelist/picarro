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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Picarro, Inc. (Santa Clara, CA) builds high-precision gas concentration and stable-isotope
analyzers based on Cavity Ring-Down Spectroscopy (CRDS), plus the cloud and mobile software
that turns those measurements into operational decisions. It serves natural gas utilities,
EtO sterilization facilities, refinery and chemical-plant fenceline monitoring,
semiconductor cleanrooms, pharmaceutical manufacturing, and atmospheric research.

- https://www.picarro.com/
- https://github.com/picarro/sam-foup-public

## API surface

Picarro's machine-readable contract is **gRPC/ProtoBuf**, published openly:

| API | Transport | Contract |
|---|---|---|
| Picarro Edge — SAM FOUP | gRPC, TCP 3343 | `grpc/` — FOUP, MeasurementSet, Controller |
| Picarro Platform Server | gRPC, TCP 7528 | `grpc/` — NetConfig, SysConfig, VirtualFileSystem, Upgrade |
| Picarro Identity (P-Cubed SSO) | HTTPS | `well-known/picarro-openid-configuration.json` (Keycloak realm) |
| Picarro P-Cubed Platform | HTTPS | no public specification |

There is **no OpenAPI, no GraphQL, no MCP server and no A2A agent card**. The Edge and
Platform servers run on the customer's own instrument network with plaintext gRPC and no
per-call authentication.

## Artifacts in this repo

- `grpc/` — 11 `.proto` files mirrored verbatim from `picarro/sam-foup-public`
- `asyncapi/` — AsyncAPI 3.0.0 **derived** from the ProtoBuf signal streams (Picarro
  publishes none)
- `well-known/` — the Keycloak OIDC discovery document plus the full probe record
- `authentication/`, `scopes/` — OIDC/SAML/gRPC auth profile
- `packages/`, `cli/`, `sandbox/` — Python wheels, Debian packages, eight CLIs, simulation mode
- `conventions/`, `errors/`, `data-model/` — streaming semantics, the `picarro.status.Error`
  contract, the entity graph
- `conformance/`, `security/`, `lifecycle/`, `changelog/` — standards, certifications,
  status page, version policy
- `mcp/` — a **candidate** MCP tool design and the tool ↔ gRPC crosswalk (not hosted by Picarro)
- `skills/`, `llms/` — three Agent Skills grounded in real gRPC methods, plus a generated llms.txt

## Notable gaps

Picarro completed SOC 2 Type 2 and ISO/IEC 27001:2022 / 27017 / 27018 in November 2025 but
publishes **no security.txt, no responsible-disclosure policy and no bug bounty**.
