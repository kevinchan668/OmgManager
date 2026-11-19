# OMGAUDIO Database File Structures — Consolidated Reverse-Engineering Study

> **Unofficial research.** This English study consolidates three Chinese V3 technical papers dated 2026-09-12: *OMGAUDIO Research Analysis*, *Database Structure and Write-Back Specification*, and *Data Object/List Specification*. It incorporates later A608 Simple Mode corrections where necessary. It is **not** a Sony specification. Offsets, record sizes and rules apply to the documented investigated formats and must be checked independently on each model and firmware.

## Contents

1. [System architecture](#1-system-architecture)
2. [Database-file inventory](#2-database-file-inventory)
3. [Logical identities and OMA objects](#3-logical-identities-and-oma-objects)
4. [Physical table and TREE representations](#4-physical-table-and-tree-representations)
5. [Classification-tree catalog](#5-classification-tree-catalog)
6. [TREE22 playlists and shared TPLB](#6-tree22-playlists-and-shared-tplb)
7. [TREE01 and SonicStage transfer bookkeeping](#7-tree01-and-sonicstage-transfer-bookkeeping)
8. [Track import and deletion contracts](#8-track-import-and-deletion-contracts)
9. [Application-level data objects](#9-application-level-data-objects)
10. [Transactional safety and time structures](#10-transactional-safety-and-time-structures)
11. [Evidence, limits and research agenda](#11-evidence-limits-and-research-agenda)

## 1. System architecture

OMGAUDIO is **not merely a folder of OMA audio files plus separate playlist files**. It is a coordinated, device-specific database in which a track ID is referenced by content records, identity tables, classification trees, playlist membership, transfer-history bookkeeping, cover references and maintenance state.

```text
                    OMGAUDIO
                        |
       +----------------+----------------+
       |                |                |
  OMA / EA3 data   Content tables   Navigation indexes
  10Fxx/100...     04CNTINF         01TREExx / 03GINFxx
  audio+identity   05CIDLST          TREE22 playlists
                   EXCNTINF          TREE01 transfer groups
                   EXCNTMTA
                        |
                Maintenance / registry
                00GTRLST, 02TREINF
                07GTCHLG, AFMNTLOG
```

**Principal invariant:** an operation must preserve the physical and referential relations expected by the target device and, where relevant, by SonicStage. Logical equivalence alone may be insufficient.

## 2. Database-file inventory

| File or path | Researched role | Implication for write-back |
| --- | --- | --- |
| `00GTRLST.DAT` | Global registration/catalog of classification trees | Maintain registrations when playlist or tree topology changes |
| `01TREExx.DAT` | Classification trees: GPLB groups + TPLB track members | Preserve record order, Level, InfoIndex, Start, capacities and membership |
| `02TREINF.DAT` | Per-tree statistics/accumulated information | Update relevant counts and totals |
| `03GINFxx.DAT` | Group names, aggregate duration, sort strings, cover references | Keep InfoIndex mappings valid |
| `04CNTINF.DAT` | Historical basic content registration | Researched record length `0x290`; preserve historical slots on ordinary deletion |
| `05CIDLST.DAT` | Historical ContentKey registration | `0x30`-byte record; 20-byte key at record offset `+0x04` |
| `07GTCHLG.DAT` | Commit/update time information for selected core trees | `0x10`-byte entries in researched variant; do not assume one universal tree set |
| `A_WM/EXCNTINF.DAT` | Active content/synchronization table | `0x30`-byte record; ContentKey at `+0x04`; zero slots may be reusable |
| `A_WM/EXCNTMTA.DAT` | Extended metadata for track identities | `0x2A0`-byte record in researched variant |
| `A_WM/AFMNTLOG.DAT` | Maintenance and operation state/times | Parse the specific blocks and preserve operation-specific timing |
| `10Fxx/100xxxxx.OMA` | EA3-wrapped audio, codec information, ContentKey | Path tied to track ID; payload and header must agree with database |
| `20Pxx/*.JPG` | Album-cover derivatives/cache | Use supported JPEG variants and consistent tree/GINF references |

`ACTIVITY.DAT`, TREE2D/2E and some runtime trees can be managed by the player or SonicStage. Do **not** apply a generic rebuild to unknown or device-owned files. The NW-HD5 is an important example of [firmware-generated TREE/GINF files](hd5-dynamic-file-set.md).

## 3. Logical identities and OMA objects

### OMG track ID

The researched format uses the OMG/ContentId identifier as a common reference across OMA paths, content tables and TREE membership. ContentId is **not** the same thing as a UI row number, current sort position or physical active-slot count.

In the documented allocation model, imports take the smallest reusable hole from active tables and then extend the table if necessary. Historical registrations may persist after deletion even though the active slot is available for reuse. An import queue of several tracks obtains IDs in queue order.

OMA filenames are mapped from the ID using a verified model-specific mapping rule; allocating any available filename by scanning the directory would violate the researched identity scheme.

### Twenty-byte ContentKey

The 20-byte ContentKey is recorded at corresponding locations in:

- OMA/EA3 metadata;
- `05CIDLST.DAT`;
- `A_WM/EXCNTINF.DAT`;
- `A_WM/EXCNTMTA.DAT`.

The original CK07 synthetic-key experiment and related testing indicate that **ContentKey should not be treated as a normal digest of the audio payload**. The tested implementation generated new transfer identity for certain ATRAC imports; those results are specific to the reported S638 workflow. Consistent ContentKeys are **not sufficient by themselves** for SonicStage to retain a track: TREE01 may also matter.

## 4. Physical table and TREE representations

### Single-block tables

Several content files consist of a file header and a data block, with researched data sections frequently starting at offset `0x30`. A safe parser must verify the actual magic, block identifier, declared count and record length before locating entries. The allocation bound is at least:

```text
required_file_end = data_offset + record_count * record_size
```

An expansion must append **whole records** and preserve record alignment. It must not conflate declared physical record count with current active-track count.

### TREE layout

The researched TREE data has two principal blocks:

| Block | Record geometry | Role |
| --- | --- | --- |
| GPLB | 8 bytes per group | Group membership descriptors and hierarchy |
| TPLB | 2-byte big-endian unsigned track ID per member | Shared ordered content references |

Important GPLB concepts include:

- `InfoIndex`: 1-based link to a companion GINF record.
- `Level`: hierarchy level (the investigated layout places it at byte B2); zero can indicate an inactive/reserved slot in certain cases.
- `Start`: 1-based starting position in global TPLB.
- `Count / interval`: constrained by group fields, following Start boundaries and logical membership counts.

For affected hierarchical TREE structures, valid groups must appear in the required **physical preorder**, with parent followed by its contiguous descendants. Changing only Start values, or sorting nodes in memory without writing the correct physical sequence, can produce a logically equivalent but unacceptable file.

A documented implementation rule expands TPLB capacity in multiples of 16 members, with the block-length relationship:

```text
TPLB_block_length = 16 + member_capacity * 2
```

The investigated implementation also enforces a 16-bit member-ID ceiling (65,535) and a group-count safety cap of 2,048. These are **implementation constraints**, not proven limits for all Sony devices.

## 5. Classification-tree catalog

The original V3 structural analysis identifies these meanings in its studied dataset:

| TREE ID | Classification or role | Hierarchy |
| --- | --- | --- |
| 01 | Base transfer batches | One-level |
| 02 | Artist | One-level |
| 03 | Album | One-level |
| 04 | Genre | One-level |
| 0A | SensMe / integrated channel | Special handling |
| 22 | Playlists | One-level |
| 2F | Rating / fixed integrated groups | Special handling |
| 30 | Genre → Artist → Album | Three levels |
| 31 | Year → Artist | Two levels |
| 32 | Title initial | One-level |
| 33 | Artist initial → Artist → Album | Three levels |
| 34 | Album initial → Album | Two levels |
| 35 | Recent transfers / album view | Special rolling rules |
| 36 | Artist → Album, including cover references | Two levels |

TREE02/03/04/30/31/32/33/34/36 were investigated under a reconstruction strategy that keeps existing valid-node relative order, inserts missing nodes, flattens in preorder, and recalculates only affected leaf memberships. An S638 test with four tracks from *Dangerous* was reported as successful for this incremental approach.

TREE0A, TREE2F and TREE35 require **separate algorithms** rather than generic tree reconstruction.

TREE36 album identity must preserve the **artist path + album** relationship; two albums with the same display name but different artists are not necessarily one database entity.

The catalog describes observed tree types, not the mandatory file set of every model.

## 6. TREE22 playlists and shared TPLB

A playlist is **not an independent member file**. All playlists share the ordered global TPLB member area of TREE22. Each playlist's active GPLB entry selects a contiguous member interval beginning at its Start.

For three ordered playlists:

```text
Start(P1) = 1
Start(P2) = Start(P1) + Count(P1)
Start(P3) = Start(P2) + Count(P2)
TPLB = Members(P1) || Members(P2) || Members(P3)
```

Editing P1 can therefore shift **every later Start**, even if the content of P2 and P3 is unchanged. Correct serialization must reconcile active/inactive GPLB slots, GINF22 names and InfoIndex, and member order. Duplicate references to the same active track may be legitimate members of a playlist.

The source data-object specification treats an active playlist with **zero members** as a deletion rather than a persistent empty active playlist in the supported implementation.

### Application playlist representation

| Field | Interpretation |
| --- | --- |
| `PhysicalOrder` | 1-based device-visible order, not InfoIndex |
| `InfoIndex` | 1-based companion GINF22 record |
| `Name` | Validated playlist title |
| `Start` | 1-based interval start in shared TPLB |
| `Members` | Ordered ContentId array, potentially with duplicates |
| `CoverRef` | Optional cover behavior, profile-dependent |

The application's direct playlist editor can modify on-device lists **without requiring the same playlist to exist in SonicStage's PC-side library**; this is a product behavior, separate from the binary structural claims above.

## 7. TREE01 and SonicStage transfer bookkeeping

A significant S638-family experiment reported that music could play on the device yet disappear after SonicStage updated the library. Investigation linked the behavior to **missing or inactive valid base-transfer TREE01 membership**, despite extended content tables containing the songs.

Subsequent test construction of valid transfer batches in TREE01 was associated with tracks remaining visible after SonicStage processing.

**Implication:** playback and a coherent extended content index are not enough to demonstrate complete host/device interoperability. Correct transfer-group history may be crucial for certain SonicStage workflows.

This is a **workflow-specific empirical finding**, not proof of Sony's complete synchronization algorithm.

## 8. Track import and deletion contracts

### Import

The studied import procedure coordinates:

1. Validated audio input and codec/device-protection pathway.
2. Track-ID allocation from eligible active slots.
3. OMA/EA3 container creation and compatible metadata.
4. ContentKey registration across all applicable tables.
5. TREE01 transfer group membership.
6. Artist, album, genre, year, initial-letter and combined classifications.
7. Optional TREE36 cover linkage and JPEG cache.
8. Per-tree statistics, maintenance records and timestamps.
9. Verification of candidate outputs before committing changes.

Model-specific protection remains an independent concern: **HD5 MP3 requires device-ID-related handling; this project does not claim HD5 ATRAC import support**.

### Ordinary deletion

A deletion can affect multiple classification trees and playlists. The researched normal-delete model removes the track from active references and its OMA payload, clears applicable active metadata, and **preserves historical slots** in `04CNTINF` and `05CIDLST` rather than truncating their historical registration.

The implementation must handle the deletion of a playlist whose last member is removed, update affected GINF and counts, preserve protected firmware-owned files, and validate all remaining references.

A music-library *reorganization/repair* workflow is a distinct operation: it aims to reconstruct the collection in place to a state comparable to initialization followed by one complete import. Do not infer that its file effects match ordinary deletion.

## 9. Application-level data objects

The third source V3 document distinguishes a logical data model from the physical file layout. These are **application-side abstraction types**, not Sony's official struct names:

| Object | Important fields / intent |
| --- | --- |
| `Track` | ContentId, OmaFile, ContentKey, Title, Artist, Album, Genre, TrackNumber, Year, DurationMs, Format, BitrateKbps, SampleRateHz, SizeBytes, OmaWriteTime, CoverRef |
| `Playlist` | PhysicalOrder, InfoIndex, Name, Start, ordered Members, CoverRef |
| `PlaylistMember` | ContentId, CurrentOrder, OriginalOrder for undo/change summary |
| `ClassificationNode` | Tree identity, level, parent/group identity, InfoIndex, member range |
| `ImportItem` | Input content and proposed library identity/metadata with validation status |
| `DeleteItem` | Existing track identity and dependent references targeted for removal |

Important rules:

- A multi-artist display string is not automatically split at `feat.`, `&` or commas into multiple database artists.
- A missing year should not be fabricated.
- An empty album name may have a UI grouping label without changing the underlying stored empty value.
- `OmaWriteTime` may support an approximate “recently imported” view, **not an authoritative chronological import record**.
- The same music may occur more than once in a playlist, and duplicate occurrence order should be preserved.
- UI row order, physical record slot and OMG ContentId are different domains.

The source specification's default library ordering is `OmaWriteTime` descending, then ContentId descending and title as a stable tiebreak. Album-track sorting considers track number, artist, title, year and ContentId.

## 10. Transactional safety and time structures

The research notes advocate a staged write-back strategy:

- Work from an untouched backup and parse/validate the starting state.
- Produce candidate in-memory or temporary representations of all affected data.
- Verify table geometry, references, intended file sizes and read-back parseability.
- Detect changes to source files made after loading, for example through fingerprints.
- Coordinate replacement of affected database files and handling of OMA payloads.
- Reopen and validate results; do not claim full crash-proof atomicity unless explicitly demonstrated.

**Timestamp structures are operation- and model-dependent.** A608 ordinary-deletion tests observed distinct modification-time groups, as well as files with changed timestamps but identical bytes. A prior S638 import workflow synchronized timestamps differently. Neither “all DAT files get one time” nor “only byte-modified DAT files update their time” is a universal rule.

The A608 follow-up dated 2026-11-19 records a successful **device-reported** ordinary-deletion/Simple Mode improvement in the named workflow, but does not prove the firmware's internal Simple Mode test or absolute Sony byte/timestamp identity. Do not retroactively promote earlier generalizations into universal laws.

See the [compatibility threshold model](sonicstage-compatibility-threshold.md), [research methodology](research-methodology.md) and [new-device adaptation guide](new-device-adaptation-guide.md).

## 11. Evidence, limits and research agenda

**Source provenance:** three Chinese V3 papers dated 2026-09-12, referring to a SonyOmgaudioDemo V3.16.13 research baseline; A608 correction notes dated 2026-11-19 based on 2026-11-18 research and device feedback. Earlier experiments include S638-specific cases. These sources cover different code versions and device operations; references to the older research baseline **do not identify the public OmgManager V9.3.13 executable as that same build**.

**Confidence:** the original Chinese papers use evidence markers such as A/B and named experiments. This English consolidation is a curated synthesis; it retains named experiment boundaries rather than treating all statements as universally proven. Numeric record layouts, byte offsets, field semantics, classification-tree presence, timestamp behavior and acceptance constraints should be rechecked against actual sample files for any new model.

**Unresolved:** Sony's internal validation algorithm, exact model-dependent compatibility constraints, interpretation of all variable fields, and a complete cross-firmware file-format specification are not known. In some tested workflows, SonicStage has accepted OmgManager outputs despite residual byte and timestamp differences. Therefore **full binary identity is a reference goal, not a demonstrated requirement for interoperability**.

### Further reading

- [OMGAUDIO research index](README.md)
- [Structural invariants and write-back](structural-invariants.md)
- [Five-dimensional compatibility](multidimensional-compatibility.md)
- [SonicStage acceptance threshold hypothesis](sonicstage-compatibility-threshold.md)
- [New-model adaptation guide](new-device-adaptation-guide.md)
- [HD5 firmware-managed file evolution](hd5-dynamic-file-set.md)

*Sony, Walkman, OMGAUDIO and SonicStage are names belonging to their respective rights holders. This independent work is not affiliated with Sony.*
