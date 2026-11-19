# OMGAUDIO Database Research — Consolidated English Overview

## Scope and evidence levels

This is a **deduplicated English synthesis** of the project's Chinese notes on Sony OMGAUDIO data structures and selected device experiments. It is a technical overview, **not** a full binary-format specification or a claim of universal firmware compatibility.

We distinguish:
- **Observed:** directly recorded in a specified sample or device experiment.
- **Interpretation:** a working explanation consistent with observations.
- **Unresolved:** hypotheses requiring more samples, firmware variants or controlled tests.

The source notes comprise a broad architecture study, a database write-back specification, logical record lists and a later NW-A608 deletion / Simple Mode investigation. Where their scope differs, the later evidence qualifies earlier implementation assumptions.

## 1. OMGAUDIO as a relational device library

An OMGAUDIO library is not simply a directory of OMA audio files. A track participates in multiple interrelated structures: a content identity, database tables, classification trees, playlists, transfer-related records and maintenance metadata.

**Engineering interpretation:** changing a track requires consistency checks across dependent structures. A playable audio payload alone is not proof that a library will survive later library updates.

## 2. ContentKey and track identity

The research records a **20-byte ContentKey** associated with OMA/EA3 headers and related records in the database. Matching values across locations can validate identity relationships. The collected results do **not** justify treating this field as a conventional hash of the audio content, nor do they establish a complete key-generation algorithm.

**Practical consequence:** preserve cross-file references and check both database entries and the associated OMA files.

## 3. Classification trees: TREE / GPLB / TPLB

Classification structures use linked GPLB and TPLB entries and fields including **Level**, **Start** and **InfoIndex**. For some hierarchical views, physical sequence and reference ranges affect interpretation.

**Practical consequence:** sorting a logical list in memory is not equivalent to constructing a valid physical classification tree. Rebuild affected sequences and references, and verify read-back.

## 4. TREE22 playlists and shared member storage

The research describes playlist membership in terms of shared TPLB storage. Playlists do not necessarily own independent, isolated arrays. Inserting or deleting members may shift subsequent membership ranges and their Start positions.

**Practical consequence:** coordinated range reconstruction is required for safe insertions, removals and ordering changes.

## 5. TREE01 transfer-group bookkeeping

Historical experiments observed content remaining playable on a device while its visibility changed following an update in SonicStage. The notes implicate the presence and consistency of transfer-group records in TREE01.

**Evidence limit:** this is a finding within documented test workflows, not a proven universal mechanism for all SonicStage versions or all models.

## 6. Import, delete and coordinated writes

A robust write can affect content indexes, identity references, metadata, classification trees, playlist membership, transfer bookkeeping, audio files and filesystem metadata.

The research recommends preserving an original backup, staging changes, validating inter-table references, committing coordinated changes and reading results back afterward. This describes a **safety approach**, not a guarantee of interruption-proof or crash-proof operation.

## 7. NW-A608 deletion, modification times and Simple Mode

The later A608 investigation compared successive snapshots after ordinary deletion operations and found a critical distinction: **changes in file bytes and changes in file-modification time are not equivalent**.

| Snapshot transition | DAT files with timestamp changes | Distinct timestamp groups | Byte-identical DAT files with changed timestamps |
| --- | ---: | ---: | ---: |
| 01 → A02 | 49 | 8 | 24 |
| A02 → A03 | 38 | 6 | 12 |
| A03 → A04a | 36 | 6 | 14 |
| A04a → A05a | 36 | 5 | 13 |

These observations belong to a related sample sequence, not four independent randomized experiments.

**Important qualification:** a timestamp-normalization approach recorded in an earlier S638-family workflow must **not** become a universal write-back rule. Device model, operation, dataset, file role and validation outcome need to be named separately.

**Still unresolved:** the firmware's precise Simple Mode decision procedure. Timestamp experiments and improvements in a particular scenario do not prove a general algorithm.

## 8. Logical records and validation model

The research distinguishes logical views such as tracks, playlist headers, playlist members, classification nodes, import candidates and deletion candidates from their physical representation across DAT files.

Useful validation categories include:
- content ID / ContentKey and database reference consistency;
- presence and identity of OMA payloads;
- TREE and playlist range integrity;
- transfer-group coherence where relevant;
- post-write read-back on supported models;
- timestamp records and mode observations for maintenance experiments.

## SonicStage compatibility: observed model-dependent thresholds

During OmgManager engineering, the team observed that device and SonicStage problems disappeared in tested workflows once reconstructed database bytes and timestamp structures approached **very high reference fidelity**. The apparent amount of fidelity needed **varied across device models**. This is an important practical result, but the specific numeric acceptance boundary has not been measured, nor has Sony's internal validation algorithm been established.

A further observation is that some **player-maintained operation records** remain untouched by OmgManager, and some **SonicStage-generated variable bytes** are not reproduced, yet tested handover still succeeds. These differences are *provisionally outside the compatibility-critical constraints* in those workflows; their purpose and Sony's internal checks remain unknown. Variable byte values must not be assumed to be truly random.

The project calls this the **SonicStage Compatibility Threshold Hypothesis**: maximize fidelity to the original output during adaptation, then verify actual device behavior and SonicStage handover. Acceptance does **not** require every byte and timestamp to be identical. Read the [four-level handover model and threshold research](sonicstage-compatibility-threshold.md).

## Generational tolerance and S638-based profile reuse

A further engineering observation is that **some later OMGAUDIO-era models appear more tolerant of database differences than earlier devices**. This helps explain why S638-derived handling has been a useful starting point for several later models. The trend is an observed adaptation pattern, not an established Sony firmware rule or proof that every newer device is compatible. Read the [Generational Tolerance Hypothesis and S638 reuse study](generational-tolerance.md).

## Why pre-HD5 device support was not pursued

The OmgManager team also investigated generations preceding the NW-HD5. The reported combination of **stricter database tolerance, greater encryption/protection complexity, limited storage capacity, and difficulties connecting some devices to modern PCs** led to a deliberate scope decision: focus on more practical HD5 and later research targets. This is an engineering trade-off, not a claim that earlier players are fundamentally impossible to support. See the [legacy-device scope study](legacy-device-scope.md).

## HD5: Firmware-managed growth of the OMGAUDIO file set

The NW-HD5 can create or regenerate additional classification TREE/GINF files after an initial library is established. OmgManager must distinguish firmware-managed records from files it may safely rebuild; the existence of only 16 DAT files in the HD5 initialization snapshot is not a permanent file-count invariant. See [HD5 dynamic file-set research](hd5-dynamic-file-set.md).

## 9. Limits and planned documentation

This English overview intentionally consolidates repeated explanations and excludes unpublished application source code, internal translation records and unverified universal claims. More detailed research publications can add sanitized hex samples, record layouts, offsets, test provenance, device firmware, reproducible steps and evidence grades.

This research accompanies the independent OmgManager Windows utility. Sony, Walkman and SonicStage are trademarks of their respective owners.
