---
name: Run a Picarro FOUP measurement and collect results
description: >-
  Start a FOUP (front-opening unified pod) airborne-molecular-contamination measurement on
  a Picarro Edge system, follow it on the event stream, and retrieve the per-compound
  concentrations when it finishes.
api: grpc/picarro-sam-foup-foup.proto
service: picarro.sam.foup.FOUP
transport: gRPC (plaintext) on TCP 3343
operations:
  - get_service_info
  - start_job
  - abort_job
  - get_result
  - watch
generated: '2026-08-02'
method: generated
source: https://github.com/picarro/sam-foup-public/blob/master/README.md
---

# Run a Picarro FOUP measurement

Every operation named here exists verbatim in `grpc/picarro-sam-foup-foup.proto`. Nothing
below is invented.

## Before you start

- **There is no hosted Picarro endpoint.** `picarro-edge` runs on the customer's own
  instrument network and listens for **plaintext** gRPC on **TCP 3343**. You must be told
  which host to talk to; pass it as `--host=ADDRESS` (resolvable name, IPv4 dotted quad, or
  bracketed IPv6). The default is `localhost`.
- **No credentials exist for this surface.** Do not ask the user for an API key or token —
  the edge services have no per-call authentication. Access control is network placement.
- **This drives physical hardware.** `start_job` runs a real measurement on real
  semiconductor cleanroom equipment. Confirm with the operator before starting or aborting
  a job.

## 1. Confirm you are talking to the right server

Call `get_service_info` (input `google.protobuf.Empty`). It returns a `ServiceInfo` with
`server_name` and `api_version` (a `picarro.version.Version` with `major`/`minor`/`patch`).
Compare the API level against the interface version your client was built from — the
`.proto` embeds `APILEVEL_MAJOR`/`APILEVEL_MINOR`/`APILEVEL_PATCH` for exactly this
purpose, and a `MAJOR` mismatch means breaking changes.

CLI equivalent: `foup-api-tool get_info`

## 2. Pick a measurement set

A job will not run without a valid measurement set name. Call
`picarro.sam.ms.MeasurementSet/get_active_measurement_sets` and choose from the result, or
use the standard set named `FOUP` which is present on every system. See the companion
skill *Manage Picarro measurement sets* if you need a custom one.

## 3. Open the event stream before you start the job

Call `watch` with a `picarro.signal.Filter`. An **empty** filter streams every event. To
narrow it, set `polarity: true` and list the `oneof` field numbers you want:

| Field | Signal | Emitted |
|---|---|---|
| 8 | `op_status` | on any change of overall operational state |
| 10 | `job_status` | on change, and **once per second** during a run |
| 11 | `job_result` | once, when a job completes successfully |
| 12 | `reprocessed_result` | when reprocessed data becomes available |
| 15 | `fdc_event` | when the analyzer raises Fault Detection and Classification flags |

CLI equivalent: `foup-api-tool monitor` (leave it running in its own terminal).

## 4. Start the job

Call `start_job` with a `JobInputs`:

- `action` — `ACTION_MEASURE_1` (inlet #1) or `ACTION_MEASURE_2`. `CLEAN_1`/`CLEAN_2` are
  the cleaning actions.
- `foup_id` — a string you choose; make it unique per pod.
- `duration` — a `google.protobuf.Duration`. The published quickstart uses 90 seconds for a
  standard measurement and 300 seconds for an extended VOC measurement.
- `measurement_set_name` — from step 2.

It returns a `JobIdentity` carrying the server-assigned **Run ID** (`job_id`, `uint64`).
Hold onto it — it is the only handle for abort and result retrieval.

CLI equivalent:
`foup-api-tool start_job MEASURE_1 "My First Foup" 90 "FOUP"`
(add a trailing `extended` for the extended VOC measurement).

**This is not idempotent.** Picarro documents no idempotency key. Replaying `start_job`
starts a second job. If a call times out, do **not** blindly retry — call
`get_result` with no identity to see whether the latest job is already yours.

## 5. Watch it run

`job_status` arrives once per second with `state` (`JobState`), `starttime`,
`scheduled_duration` and `elapsed_duration`. `op_status` carries `OperationalFlags`:
`initialized`, `ready`, `active`, `measuring`, `cleaning`, `calibrating`,
`calibrating_background`, `calibrating_ambient`, `validating_inline`, `disabled`.

To cancel: call `abort_job` with the `JobIdentity`. If you supply an identity the server
cancels **only** if it matches the current job, so a duplicate abort is safe. It returns
`AbortResult { aborted: bool }`.

## 6. Read the results

`job_result` arrives on the stream when the job completes, or call `get_result` with the
`JobIdentity` (omit it to get the latest job).

`JobResult` carries `job_id`, `foup_id`, `action`, `state`, `measurement_set_name`,
`endtime`, and a **oneof**:

- `failure` → a `picarro.status.Error` (see `errors/picarro-problem-types.yml` for the
  `domain`/`level`/`code`/`symbol` contract). Always check this branch first.
- `concentrations` → a `Concentrations` holding `repeated CompoundDetail`, each with
  `cid`, `name`, `concentration` (double) and `unit` (`UNIT_PPB`).

CLI equivalent: `foup-api-tool get_results RUN_ID`

## 7. Look back over history

- `get_reprocessed_results_by_ids` — pass a `JobIdentityList`.
- `get_reprocessed_results_time_bound` — pass a `TimeBoundRequest`; **defaults to the past
  24 hours** when no range is given.
- `get_species_time_bound` — same bounding, returns a `SpeciesList`.

There is **no pagination** anywhere in this API. Time range is the only way to bound a
result set — narrow the window rather than asking for a page.

## Practising without hardware

`picarro-edge` installs and starts in **simulation mode** by default, and the FOUP and
MeasurementSet simulators run together, emitting real `op_status`, `job_status`,
`job_result` and `reprocessed_result` signals. CRDS data functions are **not** simulated.
See `sandbox/picarro-sandbox.yml`.
