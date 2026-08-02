---
name: Manage Picarro measurement sets
description: >-
  Inspect, create, edit, activate and reconcile the measurement sets that decide which
  compounds a Picarro SAM system measures during a FOUP job.
api: grpc/picarro-sam-foup-measurementset.proto
service: picarro.sam.ms.MeasurementSet
transport: gRPC (plaintext) on TCP 3343
operations:
  - get_pool_of_cids
  - get_all_measurement_sets
  - get_active_measurement_sets
  - add_measurement_set
  - edit_measurement_set
  - activate_measurement_sets
  - deactivate_measurement_sets
  - get_suggested_measurement_sets
  - sync_measurement_sets
  - watch
generated: '2026-08-02'
method: generated
source: https://github.com/picarro/sam-foup-public/blob/master/README.md
---

# Manage Picarro measurement sets

A **measurement set** is a named list of compound ids (`cid`) the analyzer measures during
a job. Every operation below exists verbatim in
`grpc/picarro-sam-foup-measurementset.proto`.

## Hard rules the system enforces

- **`cid` 3776 (Isopropyl Alcohol) is mandatory** in every custom measurement set's cid
  list. A set without it will not be accepted.
- **9 empty slots** are available on a first install, in addition to the standard set named
  `FOUP`. Slots are addressed 1–9.
- **A set must be inactive before it can be edited.** Deactivate first.
- **If ALL measurement sets are deactivated the system halts** and performs no operation.
  Never leave the system with zero active sets.
- `activate_measurement_sets` is **exclusive**: the sets you name become active and *every
  set you did not name is deactivated*. Always send the complete intended active list.

## Inspect

- `get_pool_of_cids` (`Empty`) — the compound ids available to put in a set. Start here;
  do not guess cids.
- `get_all_measurement_sets` (`Empty`) → `MeasurementSetList`. Each
  `MeasurementSetDetail` has `measurement_set_name` and a oneof of `target_cids`
  (`CIDList`) or `suggested_cids` (`CIDConfidenceList`, each entry `cid` + `confidence`).
- `get_active_measurement_sets` (`Empty`) → the currently active subset.
- `get_suggested_measurement_sets` (`Empty`) → sets the system proposes from historical
  data.

CLI: `measurementset-api-tool get_pool_of_cids | get_all_measurement_sets |
get_active_measurement_sets | get_suggested_measurement_sets`

## Create

`add_measurement_set` takes a `MeasurementSetDetail`: a friendly name plus the cid list.

CLI: `measurementset-api-tool add_measurement_set "My First MS" 3776 180 241 284 10911 66110`

Creation is **asynchronous**. You get an immediate `MS_OP_ACCEPTED` on the
`ms_op_response` signal, then — possibly several minutes later — `MS_OP_SUCCESS`. Open
`watch` (or `measurementset-api-tool monitor`) *before* you create, and treat the operation
as incomplete until you see the terminal state. **A newly created set is inactive by
default.**

## Edit

`edit_measurement_set` takes an `EditMeasurementSetRequest` whose identifier is a oneof —
`measurement_set_name` (string) or `measurement_set_number` (slot 1–9). Three modes,
exposed by the CLI as separate verbs:

| Mode | Effect |
|---|---|
| `rename` | change the name only |
| `update` | replace the cid list only (3776 still mandatory) |
| `redefine` | change both name and cid list |

CLI: `measurementset-api-tool edit_measurement_set_by_name redefine "OLD" "NEW" 3776 241 …`
or `edit_measurement_set_by_number redefine 3 "NEW" 3776 241 …`

## Activate / deactivate

- `activate_measurement_sets` — `MeasurementSetNamesList`. Exclusive; see the hard rules.
- `deactivate_measurement_sets` — `MeasurementSetNamesList`.

## Reconcile — treat as destructive

`sync_measurement_sets` (`Empty`) aligns the measurement sets on the SAM computer to those
on the VOC analyzer. Picarro documents it as a repair procedure, not routine maintenance.

Run it **only** when a mismatch has been confirmed — via a health alert on
`controller-api-tool monitor`, or a UI notification of a Measurement Sets discrepancy —
and only when authorized.

It can **remove sets present on the SAM computer but absent on the analyzer**, add sets in
the other direction, and modify attributes of existing sets. Before calling it, capture
`get_all_measurement_sets` so the previous configuration can be reapplied afterwards with
`add_measurement_set` / `edit_measurement_set`.

This is the single highest-consequence call on the measurement-set surface. Require an
explicit human confirmation.

## Events

`watch` with a `picarro.signal.Filter` streams:

| Field | Signal | Meaning |
|---|---|---|
| 7 | `ms_op_response` | measurement-set operation status change (`MS_OP_ACCEPTED`, `MS_OP_SUCCESS`) |
| 13 | `suggested_ms` | a `MeasurementSetList` of suggested sets |

An empty filter streams both.
