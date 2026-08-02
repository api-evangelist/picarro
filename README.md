# Picarro

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
