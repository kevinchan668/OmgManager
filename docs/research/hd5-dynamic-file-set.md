# Dynamic OMGAUDIO File Set on NW-HD5

## Key finding: the player can grow its own database file set

The Sony NW-HD5 differs from the other profiled devices in an important way: **the set of OMGAUDIO data files present after device use is not necessarily the same as the set shipped in a clean initial library**. HD5 firmware may add or regenerate device-owned classification structures during boot and subsequent use. Therefore, file inventory is part of the device's evolving state, not a fixed template.

This is referred to here as **firmware-managed database file-set evolution**.

### Observed / implemented device-specific cases

The HD5-specific deletion code records firmware-generated classification trees, including:

- `01TREE0A.DAT`;
- `01TREE10.DAT` through `01TREE14.DAT`;
- associated `03GINF` structures (the code notes `03GINF0A–0F` and `03GINF10–14` as a family of observed device-owned structures).

The code documents these as **player-generated** and describes regeneration on device boot, with observed timestamps in the year 2000 for particular datasets. The precise generation conditions and exact sequence across firmware versions should be treated as subjects for further measurement.

### Structural coupling matters

A related structure, `00GTRLST.DAT`, records tree topology and active entries. If a host tool deletes or rewrites a device-owned TREE/GINF file while leaving its entries active in `00GTRLST.DAT`, it can create an inconsistent library even though the host's own logical model looks correct.

The application therefore has HD5-specific handling to **preserve firmware-owned classification files** rather than blindly applying cleanup rules derived from other device families. The code also differentiates these from orphaned cover folders, which are managed through separate cleanup rules.

## Why a 16-file initial library is not a fixed 16-file device

The original HD5 profile declares a core required set of 16 files. That is a **minimum signature / initialization baseline**, not a claim that a running HD5 always contains exactly 16 files. The HD5 firmware can create additional files, and device management must preserve the valid relationships between those files and the database catalog.

By comparison, the available initial-data snapshots for the four profiles contain:

| Profile | DAT files in initial snapshot |
| --- | ---: |
| NW-A608 | 65 |
| NW-S638F / S638 profile | 54 |
| NW-S706F / S706 profile | 46 |
| NW-HD5 | 16 |

These numbers describe **specific initialization snapshots**, not exhaustive permanent on-device inventories. They are not directly comparable as a measure of feature richness or reverse-engineering difficulty.

## The engineering model

A robust implementation should distinguish at least:

1. **Initialization baseline:** structures initially provided by the library.
2. **Host-managed files:** records OmgManager may legitimately rebuild as part of a supported operation.
3. **Firmware-managed files:** records the Walkman itself generates, changes or regenerates.
4. **Cross-file registry / topology:** entries that must remain consistent even when an individual file is not host-owned.

This introduces an additional dimension beyond byte-level comparison and timestamp-batch analysis: **file-set evolution**, i.e., which database files exist at each point in a reproducible interaction sequence.

### Suggested verification procedure

Capture the full OMGAUDIO inventory and a backup at each step: (a) clean initialization, (b) first device boot, (c) repeated boots, (d) first import, (e) deletion and (f) reconnecting through SonicStage. Record file names, sizes, SHA-256 hashes and modification timestamps, and compare changes in `00GTRLST.DAT` and TREE/GINF pairs. Do not infer a newly created file solely from a timestamp change.

**Scope:** this document describes a device-specific behavior supported by the present HD5 implementation notes. It does not claim that every HD5 firmware creates exactly the same files at the same time, or that file-set evolution alone explains all database compatibility issues.

[Research overview](README.md) · [Device compatibility](../device-compatibility.md)
