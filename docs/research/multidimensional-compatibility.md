# Multidimensional Compatibility Model for Sony OMGAUDIO

## Why database compatibility is not a single yes/no property

OmgManager's investigation suggests that Sony OMGAUDIO device support should be assessed as a **multi-layer compatibility problem**, rather than treating successful audio playback or readable DAT files as proof of complete support.

This document synthesizes the project's structural and write-back notes, the A608 Simple Mode investigation, the NW-HD5 implementation findings, and the development team's observations about device-specific encryption and legacy PC compatibility.

**Evidence language:** *recorded* means documented in the project's source notes or code; *observed by the project team* means reported engineering experience; *hypothesis* means an explanation whose internal Sony mechanism has not been confirmed.

## The five compatibility dimensions

| Dimension | Core question | Failure examples / limits |
| --- | --- | --- |
| **D — Database structural integrity** | Are physical records, indexes, content identities and inter-file references acceptable to the device and SonicStage? | Invalid TREE ordering, incorrect TPLB Start, inconsistent ContentKey, missing transfer batch |
| **T — Temporal and lifecycle integrity** | Does the updated library exhibit acceptable maintenance timestamps, batch structure and operation sequencing? | Simple Mode or host update behavior after a structurally readable write |
| **C — Codec and protection pathway** | Can this device accept the audio encoding and device-specific protection required by the chosen format? | HD5 MP3 uses device-ID-dependent protection; HD5 ATRAC import remains unresolved in this project |
| **F — Firmware-owned state** | Which files or records does the player generate, regenerate or maintain itself? | HD5 firmware-created TREE/GINF structures must not be indiscriminately rebuilt or removed |
| **H — Host connectivity and interoperability** | Can the operating system access the player, and can OmgManager and SonicStage take turns managing the library? | Legacy driver/connection difficulty, SonicStage repair requests, failed handover |

These layers interact. For example, an audio conversion can produce a valid codec payload while failing device-specific protection; a library can play normally while SonicStage rejects its transfer bookkeeping; and a logically coherent host write can break a firmware-managed file registry.

## A layered acceptance path

```text
Host detects and can access the device
        ↓
OMGAUDIO records can be parsed
        ↓
Cross-file and physical invariants survive a write
        ↓
Audio codec / device-specific protection are supported
        ↓
Firmware boots without error or degraded mode
        ↓
SonicStage accepts the changed library
        ↓
Both tools can subsequently hand over management
```

This is a **testing sequence**, not a proven order of Sony's internal checks. Failing any stage can limit usable support. Different features need different tests: read-only browsing does not require audio encryption; ATRAC import has requirements different from MP3 export.

## How structural fidelity is evaluated

The original project notes describe a series of important invariants:

**Track identity.** The OMG number links OMA naming, track records and classification references. The 20-byte ContentKey is recorded consistently across OMA/EA3, `05CIDLST`, `EXCNTINF` and `EXCNTMTA`. A correct ContentKey alone did not guarantee retention after SonicStage synchronization: TREE01 transfer membership also mattered in the recorded S638 experiments.

**Physical classification layout.** TREE GPLB and TPLB are not merely semantic lists. GPLB physical order, hierarchical Level, InfoIndex mappings and Start boundaries are part of the observed traversal contract. Multi-level groups must remain physically contiguous in preorder.

**Historical registration.** In the recorded deletion model, active references are removed, but `04CNTINF` and `05CIDLST` historical slots are preserved. Physical slot count, activity count and logical content count must not be confused.

**Playlist coupling.** TREE22 playlists share one global TPLB member area. Editing one list may shift the Start positions of following lists, requiring coordinated reconstruction.

These are **specific structural obligations** derived from research, not a claim that every unknown byte has been decoded.

## Temporal fidelity is distinct from byte fidelity

Project experiments found that a file can have **unchanged bytes but a modified timestamp**, or changed bytes without a corresponding intuitive timestamp relationship. A608 deletion captures include multiple modification-time groups for a single user operation.

A *timestamp batch* is an analytical grouping using a declared time window, not evidence of an actual firmware transaction. The earlier S638 import strategy of synchronizing selected DAT modification times is **not a universal prescription** for A608 deletion or every other device operation.

This distinction matters for the project's [SonicStage compatibility threshold hypothesis](sonicstage-compatibility-threshold.md): very high reference fidelity has coincided with successful acceptance in tested cases, but some unexplained Sony-generated variable bytes or player-maintained records can remain different.

## NW-HD5 is a boundary case

The team identifies **NW-HD5 as the practical edge** of its current supported scope:

- Its MP3 handling requires a device-ID-dependent special protection/encryption path.
- The team attempted, but did not complete, reverse engineering of the corresponding ATRAC encryption behavior through what it refers to as Sony Shared code. HD5 ATRAC import is **not** advertised as implemented.
- HD5 firmware can create or regenerate additional database TREE/GINF files, making the file inventory dynamic.

This combination demonstrates that database handling, file-set lifecycle and audio-protection compatibility are separable.

The earlier-than-HD5 devices investigated by the team were deprioritized due to a combination of stricter apparent database tolerance, proprietary protection complexity, small storage capacities and practical connectivity issues with modern PC environments. This is an engineering scope decision, not proof of a universal impossibility.

## Practical support matrix: avoid a single 'supported' flag

Each model should ideally be assigned a separate status for:

1. Detection and read-only parsing
2. Library and category navigation
3. Playlist editing
4. MP3 import
5. ATRAC import
6. Export and restoration of available formats
7. Database maintenance
8. Device playback and Simple Mode
9. SonicStage handover and repeated interoperability

Each status should identify the firmware, software version, operation and evidence level. The repository does not presently publish a complete V9.3.13 regression matrix for all four profiles.

## Conclusion

**OmgManager's most distinctive engineering result is not just the ability to read Sony's database, but the progressive understanding of which structural, temporal, protection, firmware and host-environment constraints must be respected to achieve useful interoperability.**

These are partially decoded constraints backed by model-specific tests, not a claim to have recovered Sony's internal validation algorithm.

[Research index](README.md) · [NW-HD5 boundary](legacy-device-scope.md) · [HD5 file evolution](hd5-dynamic-file-set.md) · [Device compatibility](../device-compatibility.md)
