---
name: Monitor Picarro analyzer health and FDC alerts
description: >-
  Subscribe to the Picarro Edge signal streams to track instrument health alerts, analyzer
  driver restarts, and Fault Detection and Classification (FDC) events, and interpret the
  escalation semantics correctly.
api: grpc/picarro-sam-foup-controller.proto
service: picarro.sam.controller.Controller
transport: gRPC (plaintext) on TCP 3343
operations:
  - watch
generated: '2026-08-02'
method: generated
source: https://github.com/picarro/sam-foup-public/blob/master/README.md
---

# Monitor Picarro analyzer health

The only method on `picarro.sam.controller.Controller` is `watch`. Everything else in this
skill is about reading the stream correctly.

## The stream is a keyed cache, not a log

`picarro.sam.controller.Signal` is a **mapping signal**. Each message carries:

- `mapping_action` — `MAP_ADDITION`, `MAP_UPDATE`, `MAP_REMOVAL` (or `MAP_NONE`)
- `mapping_key` — the analyzer identity, e.g. `"Picarro_8008-AMSADS3008"`

Maintain a dictionary keyed by `mapping_key`. `MAP_ADDITION`/`MAP_UPDATE` upsert;
`MAP_REMOVAL` deletes. Do not append these to a flat event log and report the count —
you will report cleared alarms as live ones.

## The signals

| Field | Signal | Payload | Cached server-side |
|---|---|---|---|
| 8 | `raw` | `picarro.variant.Value` — raw PicarroMQ message from SAM core; `mapping_key` is the PicarroMQ topic, `mapping_action` is fixed `MAP_UPDATE` | no |
| 14 | `analyzer_driver_started` | `picarro.status.Error`; `mapping_action` fixed `MAP_ADDITION`; `mapping_key` is the analyzer identity, e.g. `"Picarro_9038-NUV1083"` | no |
| 15 | `analyzer_health` | `picarro.status.Error`; `mapping_action` says added/updated/removed | **yes** |

Two consequences worth getting right:

1. **`analyzer_health` is cached and re-emitted on every new `watch()`.** A reconnecting
   client resynchronizes its full alert state without a separate snapshot call. Do not
   treat the replay burst as new alerts.
2. **`analyzer_driver_started` implicitly clears all existing `analyzer_health` mappings**,
   on the assumption they are invalidated by a SAM core restart. When you see it, drop your
   whole health dictionary for that analyzer and rebuild from what follows.

`raw` is described by Picarro as useful mainly for debugging — the data model varies across
topics and even within a topic, and time representations are not standardized. Do not build
logic on it.

CLI: `controller-api-tool monitor analyzer_health analyzer_driver`
(omit the keywords to stream everything, which is noisy).

## Reading a health alert

Both health signals carry `picarro.status.Error`:

| Field | Meaning |
|---|---|
| `domain` | `DOMAIN_APPLICATION`, `DOMAIN_SYSTEM`, `DOMAIN_PROCESS`, `DOMAIN_DEVICE`, `DOMAIN_SERVICE` |
| `origin` | domain-specific origin, e.g. `"Linux"`, `"HTTP"` |
| `level` | `LEVEL_TRACE` … `LEVEL_FATAL` (9 levels) |
| `code` | origin-specific numeric id |
| `symbol` | symbolic name, unique within the domain |
| `timestamp` | time of occurrence |
| `attributes` | free-form key/value map |
| `text` | human description, expanded with attribute values |

Escalate on `LEVEL_ERROR` and above: `LEVEL_ERROR` means the operation failed but the
entity still works; `LEVEL_CRITICAL` means the entity is disabled; `LEVEL_FATAL` means the
reporting entity cannot recover. Full table in `errors/picarro-problem-types.yml`.

## FDC events live on the FOUP stream, not this one

Fault Detection and Classification flags from the analyzer arrive as `fdc_event`
(field 15) on `picarro.sam.foup.FOUP/watch`, added in **FOUP v6.1**.

`AnalyzerFdcEvent` carries `analyzer_name`, `analyzer_model`, `publish_reason`,
`fdc_event` (string), `severity` (`picarro.status.Level`) and `timestamp`.

The escalation state machine matters more than the raw count:

| Reason | Meaning | Trigger |
|---|---|---|
| `ESCALATED` | severity increased | consecutive count or duration crossed a threshold |
| `ESCALATION_CONTINUED` | still critical | **repeats every 60 seconds while escalated** |
| `DE_ESCALATED` | resolved automatically | no new occurrences for 60 seconds |

`ESCALATION_CONTINUED` is a heartbeat, not a new incident. Deduplicate it, or a
five-minute outage will read as five separate faults.

CLI: `foup-api-tool monitor fdc_event`

## Filtering

`picarro.signal.Filter` has `polarity` and a list of `oneof` field numbers. `polarity: true`
means include only those fields; `false` means exclude them. An **empty filter streams
everything**. Ask for the two or three signals you actually consume — `job_status` alone
emits once per second for the whole duration of every run.

## Practising without hardware

In the default simulation mode the Controller is observable but generates **no** health
alerts or other events. To exercise this skill you need a real system.
