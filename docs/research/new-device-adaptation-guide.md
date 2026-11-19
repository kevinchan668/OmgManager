# New Device Adaptation Guide — Sony OMGAUDIO

**Scope:** a practical, evidence-driven procedure for adapting an additional Sony OMGAUDIO Walkman model to OmgManager. This is a **recommended engineering workflow**, not a claim that the listed tests have already passed on an untested device.

The project's recommended implementation order is:

> **1. Playlist operations → 2. Music deletion → 3. Music import**

The stages generally increase in implementation difficulty and the number of database structures that must be coordinated. **Do not begin with import merely because it is the most visible feature.** Do not interpret a successful earlier stage as proof that a later stage will succeed.

## 1. Foundation: collect sufficiently rich, authentic SonicStage reference samples

A new model must be studied using **real SonicStage-generated databases** from that target model, not only a template copied from an existing OmgManager device profile. Gather a reproducible set of entire **OMGAUDIO directory snapshots** before and after operations. Record device model and firmware, SonicStage version, operating system, connection environment, content format and the complete operation sequence.

### Minimum music-library state matrix

| ID | Required reference state / transition | Purpose |
| --- | --- | --- |
| S00 | **Empty library** after initialization | Discover baseline files, counters, default records, timestamps and firmware-created files |
| S01 | Empty library → **import one track** | Discover minimum viable content registration, identity, OMA structure and transfer records |
| S02 | Empty library or documented baseline → **import multiple tracks** | Determine multi-entry record ordering, grouping and counts |
| S03 | Populated library → **delete one track** | Identify active-reference removal and preserved history |
| S04 | Populated library → **delete multiple tracks** | Test shifting membership ranges, counters and batch effects |
| S05 | Import → delete → **import again** | Reveal slot reuse, historical identities, counters and re-import behavior |
| S06 | Populated library → **delete until one track remains** | Inspect boundary behavior and retention of the last active entry |
| S07 | Populated library → **delete all tracks** | Observe truly empty active state, retained registration and maintenance effects |
| S08 | Delete all → **import again** | Distinguish a freshly initialized database from an emptied-then-reused database |

**Repeat the same operation two or three times** under controlled, comparable conditions when investigating unexplained changes. This is essential for separating stable fields from SonicStage-generated *variable bytes*, operation-history changes and timing differences. “Variable” does not prove randomness. When feasible, create **independent runs restored from the same baseline**, not just repeated operations on an ever-changing database. Maintain both experimental types if historical evolution is itself the subject of study.

The nine rows are **required sample classes**, not a universal maximum number of capture files. Each transition needs at least a before and after image; complex operations should have additional captures after reconnecting to the player and after SonicStage updates. Vary input count, album/artist metadata and playlist membership in later tests only after baseline comparisons are understood.

### Additional reference samples for playlist work

As playlists are the first implementation stage, music-library samples alone are not sufficient. Obtain native SonicStage snapshots for **no playlists, create one playlist, create several playlists, add one member, add several members, remove one member, remove several members, reorder members, rename a playlist, delete a playlist**, and where supported delete the final member. Include a playlist that shares tracks with another list and a list affected by a song deletion.

Record when a command is **not supported by SonicStage directly on the player**. Where necessary, perform the SonicStage-supported PC-library workflow and its synchronization operation, documenting that it is a reference for the final device database rather than an identical user interaction to OmgManager's direct playlist editing.

## 2. Capture and diff the complete device state

For each sample and repeated run:

1. Back up the **complete** original OMGAUDIO directory before experimentation; never write to the only copy.
2. Capture the **file inventory**: relative path, size, hash, creation/modified timestamps where available and filesystem timestamp precision.
3. Compare **byte-level changes**: full-file hashes, changed ranges, DAT record counts, headers, unused/reserved fields and OMA content identity.
4. Identify **timestamp-batch structure** using a documented window `Δt` and repeat the analysis at different plausible windows. Record time-only changes, byte-only changes and ordering of timestamp groups.
5. Track **new, removed, and firmware-maintained** files separately. NW-HD5 is a known reason why initial file counts cannot be treated as permanent.
6. Compare **repeated identical operations** to classify stable changes, history-dependent changes and unexplained variable bytes. Do not write back unknown values simply to force a 100% match.
7. Preserve exact operation history: a library after “delete all” may differ materially from a never-used initialized library.

Relevant structures in existing OmgManager research include `01TREE` / GPLB / TPLB, `03GINF`, `04CNTINF`, `05CIDLST`, `TREE22`, `TREE01`, OMA/EA3 content identities and device-specific extensions. Their presence, layout and roles must be verified on the *new* model; do not blindly apply previously observed offsets or rules.

## 3. Adaptation stage 1 — Playlist operations

**First implementation target:** reading and writing playlists, with no audio import or deletion required.

Recommended order of tests: read existing playlists → create and rename → add/remove and reorder members → create multiple lists → delete list → handle empty-list boundaries → export/import PL data where applicable.

Technical checks:

- Playlist header/metadata correspondence and TREE22–GINF relationships.
- Shared TPLB member storage: changing one playlist can shift later playlists' `Start` offsets.
- Correct member order, playlist order and references to existing track IDs.
- Effects on other playlists, tree registry entries and timestamps.
- Reboot, real-device navigation and SonicStage handover without repair prompts.

**Stage exit:** the named playlist operations can be repeated on backup test libraries, the player displays them correctly, and SonicStage accepts subsequent updates in the documented test configuration. Any failures or untested operations remain explicitly listed.

## 4. Adaptation stage 2 — Music deletion

Deletion is next because it extends the write surface beyond playlist membership into track references, metadata, classifications, historical registration and file disposal, but does not yet require generation of a valid new encoded audio payload or its device-specific protection.

Recommended order: delete one → delete several → delete a track present in several playlists → delete until one remains → delete all → import-after-delete only as a **SonicStage reference observation** until OmgManager import is implemented.

Technical checks:

- Removal from all active classification views, TREE22 lists and other required indexes.
- Preservation of model-specific historical slots where native evidence demands it.
- Coordinated updates to counts, GINF, TREE registry and transfer bookkeeping.
- No inadvertent overwrite of firmware-owned or regenerated files.
- Separate treatment of database records and physical OMA deletion/recovery.
- Timestamp changes even for DAT files whose bytes remain identical.
- Device playback/navigation and host repair/forced-reinitialization outcome.

**Stage exit:** deletion cases match the target device's accepted structural and temporal constraints closely enough for successful device and SonicStage verification; results for the empty-library boundaries are explicitly recorded.

## 5. Adaptation stage 3 — Music import

Import comes last because it typically involves the most coordinated changes: new content identity, audio container creation, codec/protection requirements, content tables, all relevant classification trees, transfer groups, timestamps and file-set state.

Recommended progression: import one → import several → mixed metadata / artists / albums → import following deletion → import into a previously emptied library → repeated import and SonicStage handover.

Technical checks:

- OMA/EA3 structure, track numbering and ContentKey consistency across files.
- Model-specific supported formats, protection, encryption and required device identity.
- New entries in active indexes and correct treatment of historical slots.
- Valid category tree layouts and playlist interactions.
- Transfer bookkeeping (including TREE01 where applicable): a track that plays on-device may still be lost or reclassified after SonicStage synchronization.
- Timestamp batch ordering, firmware-maintained records and restart behavior.
- Repeated handover tests on the **same surviving library**, not only on freshly initialized data.

**Special caution:** NW-HD5 MP3 uses a device-ID-related protection path; the project's HD5 ATRAC encryption reverse-engineering attempt did not produce implemented ATRAC import support. A new model's codec/protection path must be established independently rather than inferred from database readability.

## 6. Acceptance is model-specific, not a single byte-match percentage

The OmgManager team observed that **very high structural and timestamp reference fidelity** was correlated with reliable acceptance in tested workflows, but the apparent threshold differs by model. Certain player-maintained records and SonicStage-generated variable bytes can remain different without disrupting tested handover.

Accordingly:

- Aim to reproduce Sony's observed reference behavior **as faithfully as practicable**, especially for unknown structural relationships.
- Distinguish critical invariants from candidate non-critical differences using controlled tests.
- Do **not** invent a universal percentage threshold, assume a variable field is random or claim to know Sony's internal acceptance algorithm.
- Check the player for error states, Simple Mode, normal playback and playlist/category behavior.
- Then reconnect to SonicStage and verify no forced repair, erasure or initialization; inspect that the library and playlists remain intact.
- Where feasible, test **OmgManager → SonicStage → OmgManager → SonicStage**, including actual modifications, not just successful connections.

See the [SonicStage Compatibility Threshold Hypothesis](sonicstage-compatibility-threshold.md) for the four-level outcome classification and the role of repeated bidirectional handover.

## 7. Sample naming, reproducibility and reporting

Suggested directory convention (examples only):

```text
NewModel/
  00-metadata/
  S00-empty/run-01/before-and-after/
  S01-import-one/run-01/
  S01-import-one/run-02/
  S01-import-one/run-03/
  S02-import-multiple/
  S03-delete-one/
  S04-delete-multiple/
  S05-delete-then-import/
  S06-delete-to-one/
  S07-delete-all/
  S08-reimport-after-empty/
  P01-create-playlist/
  P02-edit-members/
  P03-remove-and-reorder/
  results/
```

For each operation, save: `model / firmware / SonicStage version / operation / inputs / baseline snapshot / output snapshot / hashes / byte deltas / timestamp grouping rule / device result / SonicStage result / unresolved differences`.

Keep the **raw captured datasets unchanged** and put interpreted reports in separate files. Avoid posting private track collections, device IDs or proprietary application binaries in public research archives. If public examples are published, sanitize identifying data while preserving the structure needed to reproduce the observation.

## 8. Deliverables and stage gates

A new device adaptation should deliver:

| Output | Requirement |
| --- | --- |
| Reference archive | All nine minimum music-library state classes, plus playlist-specific examples |
| Variability study | Two or three controlled repetitions of selected identical operations and a record of unexplained bytes |
| Structural map | Device-specific file inventory, physical record structures, cross-file references and exceptions |
| Temporal map | Documented timestamp-batch analysis and byte-identical/time-changed cases |
| Playlist stage report | Tests and explicit accepted/failed/untested operations |
| Deletion stage report | Boundary cases, structural consistency and host handover |
| Import stage report | Codec/protection, identity, transfer records and handover |
| Final compatibility statement | Exact hardware/firmware/software scope and demonstrated interoperability level |

**Do not mark a later stage complete solely because an earlier one passed.** If the required SonicStage reference samples are not available, explicitly mark the adaptation as evidence-incomplete rather than filling missing behaviors by guesswork.

---

**In short:** build a rich native SonicStage sample corpus first; **adapt playlists, then deletion, then import**; repeat nominally identical operations to reveal variable data; compare bytes, timestamps and file lifecycles; and let actual player/SonicStage handover determine acceptance.

[Research overview](README.md) · [Research methodology](research-methodology.md) · [Structural invariants](structural-invariants.md)
