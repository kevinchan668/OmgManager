# Evidence-Based OMGAUDIO Reverse-Engineering Method

## Goal

This document formalizes a reproducible way to move from an unexplained Sony behavior to a tested OmgManager device adaptation.

It is based on the project's existing compare-and-rebuild work: snapshots from SonicStage and device operations, the September 2026 database-structure notes, and the November 2026 NW-A608 deletion / Simple Mode investigation.

## 1. Distinguish kinds of evidence

| Evidence class | Meaning | What it does NOT establish |
| --- | --- | --- |
| **Native comparison** | Difference observed between two authentic SonicStage/device states | A complete causal explanation |
| **Controlled intervention** | A deliberately changed field or timestamp followed by a different observed result | Necessarily the internal firmware rule |
| **Application implementation** | OmgManager contains a model-specific reconstruction or preservation strategy | That all firmware versions passed regression |
| **Device acceptance** | Playback, normal UI or no Simple Mode in a named test | That SonicStage will accept the same database |
| **SonicStage handover** | SonicStage continues managing a changed library without repair/reset in a named test | That all byte/time differences are irrelevant |
| **Repeatable bidirectional interoperability** | Successful repeated host/device operations in both directions | Universal support outside tested conditions |

## 2. Build a comparable baseline

For each experiment record:

- precise player model and firmware;
- Windows version, connection method and SonicStage version where applicable;
- application build, audio codec and import/delete/playlist operation;
- the starting OMGAUDIO snapshot;
- full file inventory, file sizes and cryptographic hashes;
- modification timestamps **including filesystem precision and time-zone handling**;
- device outcome, SonicStage outcome and post-handover snapshot.

Comparing unlike starting states can mistake historical differences for effects of the operation.

## 3. Analyze differences in layers

**First: file-set delta.** Determine files created, removed or regenerated. Do not confuse a filename that is absent from an initialization snapshot with a permanently unsupported file; HD5 provides a counterexample.

**Second: byte-level delta.** Calculate full-file hashes and localized differences, grouping them by record fields, table counts, headers, classification ranges, historical slots and unclassified bytes. Do not assume apparently variable bytes are intrinsically random.

**Third: timestamp-batch delta.** Predefine a grouping window `Δt`, timestamp resolution and grouping rule. Show which files changed content only, time only, both or neither. Run sensitivity checks with other window sizes; a batch is an analytical grouping, not necessarily a firmware transaction.

**Fourth: cross-file constraints.** Verify ContentKey and OMG references, TREE/GPLB/TPLB/GINF mappings, TREE22 playlist intervals, TREE01 transfer groups, tracked statistics and maintenance metadata.

**Fifth: actual acceptance.** Test the player (errors, playback, classification, Simple Mode) and then SonicStage (repair warning, forced reset, library retention and subsequent updates).

## 4. Treat unknown bytes carefully

The team reports two useful classes of residual differences:

- player-written operation records left untouched by OmgManager;
- SonicStage-generated variable bytes not reproduced by OmgManager.

Their coexistence with acceptance in some workflows is informative, but **does not prove the internal fields are ignored**, and does not establish that variable values are produced by a random process.

Maintain an 'unexplained differences' register with the file, offset, observed values, baseline/operation, model and handover result. Investigate a field only when the evidence or the adaptation goal warrants it.

## 5. Estimate model-specific acceptance constraints

The [compatibility threshold model](sonicstage-compatibility-threshold.md) is best tested by changing one documented factor at a time, preserving a reliable rollback path and examining **which invariant broke**, not by quoting a global 'percentage of matching bytes'.

For two model families, equivalent experiments may produce different acceptance outcomes. This motivates the [generational tolerance hypothesis](generational-tolerance.md), while leaving open other influences such as firmware, codecs, state histories and PC software versions.

## 6. Report unresolved and unsuccessful work

Engineering reports should include failure boundaries:

- An S638 timestamp result must not automatically be applied to A608 deletion.
- A player accepting a track does not prove transfer bookkeeping is valid after SonicStage reconnection.
- HD5 MP3 and ATRAC import have distinct device-protection requirements; the team reports an unsuccessful ATRAC protection reverse-engineering attempt, not complete ATRAC support.
- Unsupported earlier devices were deprioritized for engineering reasons, not shown to be fundamentally impossible to support.

Such limitations make a research repository **more** credible, not less.

## 7. Suggested experiment report template

```text
Experiment ID:
Device / firmware:
Software / version:
Operation:
Baseline dataset:
Reference (Sony) dataset:
Modified (OmgManager) dataset:
Files added / removed:
Byte deltas and field groups:
Timestamp-batch rule (Δt, precision, timezone):
Time-only changes:
Firmware-managed files:
Device result / Simple Mode:
SonicStage result / repair request:
Repeated handover result:
Inference:
Alternative explanations:
Evidence level:
Open questions:
```

## Conclusion

A convincing reverse-engineering result links **a concrete data difference**, **a controlled or carefully documented intervention**, and **an observed device/SonicStage outcome**, while preserving the limits of what remains unknown.

[Research index](README.md) · [Multidimensional compatibility](multidimensional-compatibility.md)
