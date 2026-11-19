# SonicStage Compatibility Threshold Hypothesis (SCTH)

## Central hypothesis

**A host-generated OMGAUDIO library need not be identical to a SonicStage-generated library, byte for byte or timestamp for timestamp, to be accepted by SonicStage. Nevertheless, acceptance has limits.**

OmgManager's reverse-engineering approach is to reproduce Sony's observable database behavior as faithfully as possible while using **successful device playback and SonicStage handover** as the final compatibility criteria.

This is the **SonicStage Compatibility Threshold Hypothesis (SCTH)**. It is an interpretation of observed acceptance of non-identical data, **not** a measured universal numerical threshold or a proven internal Sony algorithm.

## Four levels of interoperability

The following four levels are a proposed *outcome classification*, not a statement that each failure necessarily has the same root cause.

| Level | On-device behavior | SonicStage reconnect | Interpretation |
| --- | --- | --- | --- |
| 1 — Rejected by player | Error or unusable library | Detects database damage, requests repair or reinitialization | Not device-compatible |
| 2 — Degraded mode | Music plays, but Simple Mode appears or advanced functions are unavailable | Detects an issue, repairs or requests reinitialization | Partial device-level acceptance |
| 3 — Device-accepted, host-rejected | Normal playback, no Simple Mode | Detects an issue, repairs or requests reinitialization | Device-compatible but not interoperable |
| 4 — SonicStage handover | Normal playback, no Simple Mode | Accepts the library without repair or forced reset | Successful tested host handover |

**Bidirectional handover** is stronger than a one-time successful reconnect: test OmgManager → SonicStage → OmgManager, including read/write operations, followed by another SonicStage reconnect. A single successful handshake should not be represented as universal bidirectional interoperability.

## Reference fidelity vs. functional acceptance

Let the test state of a library be described by four dimensions:

- **B — Byte-structure fidelity:** data layouts, entries, ordering, reserved fields and cross-file references, compared against comparable SonicStage operations.
- **T — Timestamp-batch fidelity:** groups of files with temporally linked modification times, unchanged-byte files whose timestamps nevertheless change, and the ordering of such groups.
- **F — File-set fidelity:** files present, absent or dynamically regenerated; special attention to firmware-owned records such as HD5 TREE/GINF files.
- **R — Referential/behavioral integrity:** track identity, ContentKeys, classification references, playlist membership, transfer records and behavior after a device boot and host reconnection.

These dimensions cannot be safely combined into an unweighted percentage. **A single critical field may have much greater importance than many irrelevant byte differences.**

The acceptance function is therefore represented conceptually as:

```text
Accepted = A(device model, firmware, SonicStage version, operation, B, T, F, R)
```

The *compatibility boundary* is the set of observed accepted/rejected states in this multidimensional space. It may depend on the operation, model and software version; acceptance need not be monotonic in a naive byte-match score.

## Why maximize reference fidelity for a new model?

For a new device model, OmgManager should initially aim to reproduce the reference output as closely as practical:

1. Capture before/after snapshots from a comparable SonicStage operation.
2. Align the **logical operation**, source music, device state and baseline database.
3. Compare the complete data-file inventory and byte-level differences.
4. Identify timestamps and **timestamp batches** using an explicit grouping rule and a sensitivity analysis of the grouping window.
5. Investigate non-byte-changing files whose timestamps move, and firmware-created or regenerated files.
6. Bring the implementation closer to the reference, prioritizing unexplained critical differences.
7. Validate on the actual player and reconnect to SonicStage.
8. Once acceptance is observed, perform repeat, reverse-direction and regression tests rather than claiming a specific percentage threshold.

Pursuing high fidelity is an engineering risk-reduction strategy. The end goal is **reproducible compatibility**, not blind replication of every incidental field or filesystem time.

## Timestamp-batch structure

A **timestamp batch** is an analytical group of file modification events occurring sufficiently close in time according to a predeclared window `Δt`. For example, files could be grouped when adjacent recorded modification times differ by no more than a chosen `Δt`, but this is a measurement convention, **not** proof of a physical firmware transaction.

A comparable batch description should include:

- the grouping window and timestamp granularity;
- batch sequence and relative ordering;
- which files changed bytes and timestamps;
- which files changed timestamps without changing bytes;
- which files changed bytes without the expected timestamp movement;
- files added or deleted during the operation.

No fixed `Δt` should be declared universal without evidence. Compare results across multiple candidate windows and device models; do not infer write sequence where filesystem timestamp granularity or copying operations make it ambiguous.

## Existing evidence and important uncertainty

The NW-A608 deletion investigation observed multiple timestamp groups in one operation and files whose bytes did not change although their timestamps did. It also records behavior differences in Simple Mode scenarios. Earlier S638-family work used a different timestamp-handling strategy.

Taken together, these observations show **structure beyond semantic equivalence** and support investigation of a compatibility boundary. They **do not** establish an exact numerical threshold, an internal scoring rule, or even that Sony checks all timestamp differences directly.

A further important distinction: **SonicStage accepting a library despite some byte/timestamp differences shows that 100% raw identity is not mandatory in that case. It does not prove which particular differences are tolerated or why.**

## Experimental route to estimate the boundary

For each device/firmware/SonicStage combination:

1. Use identical controlled baselines, operation inputs and backup recovery procedures.
2. Record the Sony reference outcome and OmgManager output (files, byte diffs, hashes, timestamp precision and batches).
3. Change one documented factor at a time where possible, while preserving a rollback path.
4. Run device checks (error, playback, Simple Mode, playlists), then SonicStage reconnect and read/write checks.
5. Track accepted and rejected examples by the **specific structures changed**, not just overall similarity.
6. Repeat the cycle and classify the strongest demonstrated handover level.
7. Report unresolved cases and counterexamples rather than converting successful tests into universal claims.

The research question is not simply *“What percentage matches?”* but *“Which data and temporal invariants must be preserved for this model, operation and host version?”*

## Practical conclusion

**High-fidelity reconstruction is the development strategy; SonicStage handover is the operational acceptance test; the compatibility threshold is the hypothesis connecting them.**

A library may cross the operational compatibility boundary while still differing from a SonicStage-produced library at byte and timestamp levels. The boundary is a testable research question and should be refined as device-specific evidence accumulates.

[Research overview](README.md) · [HD5 file-set evolution](hd5-dynamic-file-set.md) · [Device compatibility](../device-compatibility.md)
