# Structural Invariants and Coordinated Write-Back in OMGAUDIO

## Research principle: semantic equivalence is not always physical equivalence

A database can represent the *same* tracks, albums or playlists at an application level and still differ in ways that a Sony Walkman or SonicStage does not accept. OMGAUDIO reverse engineering therefore requires attention to **physical record order, retained historical slots, cross-file identity, index ranges and operation bookkeeping** in addition to logical content.

This page consolidates findings from the project's September 2026 database-structure and write-back notes. It preserves their scope: confirmed results were obtained on named samples, with several key write-back behaviors tested on NW-S638-family workflows. They should not be automatically generalized to every OMGAUDIO device.

## 1. Cross-file track identity

The notes describe OMG track numbers as common references across multiple stores. A content identity can involve:

| Structure | Recorded role |
| --- | --- |
| `10Fxx/100xxxxx.OMA` | Audio payload, EA3 metadata and ContentKey |
| `04CNTINF.DAT` | Historical content registration; 0x290-byte record in researched format |
| `05CIDLST.DAT` | Historical ContentKey registration; 0x30-byte record, 20-byte key at record +0x04 |
| `A_WM/EXCNTINF.DAT` | Active content records; 0x30-byte record and key at +0x04 |
| `A_WM/EXCNTMTA.DAT` | Extended metadata; 0x2A0-byte record in researched format |
| `01TREExx.DAT` | Classification and membership references by OMG number |

The exact set differs among device families and initial-data states; these lengths and offsets are **research-specific**, not a universal published Sony specification.

**Invariant:** when applicable, the same track identity and ContentKey must remain coherent across every active reference. The source notes describe four-way ContentKey consistency including the OMA EA3 header, CIDLST and both extended content tables.

## 2. Historical identity and tombstone preservation

A deletion need not mean erasing every historical trace of a track. The recorded write-back model clears active membership and current metadata while preserving the physical registration slots in `04CNTINF` and `05CIDLST`.

This is a useful distinction:

- **active membership** — what can currently be listed or played;
- **historical registration** — previously allocated identities and slots;
- **reusable empty active slots** — vacated positions available for new imports.

Consequently, the number of physical records is not necessarily the same as the number of active tracks. A generic database compaction routine that truncates history could produce a semantically tidy but physically incompatible result.

## 3. Physical TREE traversal invariants

In the researched TREE model:

- GPLB stores grouping records, recorded at 8 bytes each;
- TPLB stores member OMG IDs, recorded at 2 bytes, big-endian;
- InfoIndex identifies corresponding GINF metadata;
- Level expresses hierarchy; a zero level can indicate an inactive slot in certain contexts;
- Start identifies the beginning of a group's shared member interval.

For multi-level trees, parent and descendant nodes must appear in a valid **physical preorder**. Logical sorting in memory is not enough if the output GPLB remains in the wrong physical sequence.

The September notes give an example of incremental reconstruction: preserve the relative sequence of valid existing nodes, insert missing nodes, flatten in preorder and rebuild affected leaf memberships.

**Non-universal detail:** TREE0A, TREE2F and TREE35 have separate behavior and should not simply be passed through the ordinary hierarchical reconstruction algorithm.

## 4. TREE22 as a shared interval-allocation problem

Playlists provide a good example of a hidden coupling. Individual playlists share a global TPLB member array; their member segments are located by Start boundaries.

Therefore, changing the length of playlist A can shift the Start boundaries for playlists B, C and later records even though those lists' tracks have not changed.

A safe reconstructed playlist tree must preserve:

1. valid active/inactive GPLB ordering;
2. legal InfoIndex–GINF mapping;
3. complete, non-overlapping TPLB segments;
4. member ordering and playlist ordering;
5. synchronized changes to the associated global tree registry.

The notes record that removing the last track from a list results in list deletion rather than persisting an active empty playlist.

## 5. TREE01: content may play but not survive host synchronization

A particularly notable S638 experiment documented tracks that played on the Walkman but disappeared after SonicStage updated the library. The research traced this outcome to missing or inactive valid membership in the base transfer tree (`TREE01`), despite other extended indexes containing the tracks.

In later experiments, transfer batches were represented by valid active TREE01 groups; the imported tracks remained visible after SonicStage processing.

**Research lesson:** a music file passing the device playback test does not prove that the complete host-device bookkeeping is valid. Transfer history and primary grouping records have practical compatibility significance.

## 6. Coordinated deletion and transactional protection

The documented deletion path is a multi-table operation:

- remove track IDs from classification trees and all affected playlists;
- remove empty playlists according to the supported rule;
- clear active content entries without indiscriminately truncating historical tables;
- coordinate GINF totals, TREE information, changed-tree registration and maintenance metadata;
- hold or rename the OMA payload until the database state is validated;
- commit the group of changes and read back the result.

The source notes additionally recommend checking the target files against their loaded fingerprints and refusing to overwrite externally modified states.

These are **engineering safeguards**: they lower the risk of partial writes and errors, but they do not mathematically guarantee crash-proof transaction semantics on every host filesystem or player.

## 7. Timestamp and history are additional state

The S638 write-back notes recorded a strategy in which selected committed DAT files had their filesystem modification times coordinated with the commit time. Later A608 work demonstrated much more complex modification-time batch behavior, including files whose timestamps changed without byte changes.

Therefore:

**An operation's output is not fully described by the final semantic music list, or even by the final DAT byte sequences.** Its history-related records, file inventory and observed timestamp relations may affect compatibility.

The precise checks performed internally by Sony devices and SonicStage remain unknown.

## Summary

The relevant notion of fidelity is **invariant preservation**, not a simple one-to-one equivalence between logical music objects. The combination of record geometry, tree traversal order, shared membership intervals, historical registration and operation metadata explains why apparently small changes can trigger large compatibility differences.

Read together with the [multidimensional compatibility model](multidimensional-compatibility.md) and [SonicStage threshold hypothesis](sonicstage-compatibility-threshold.md).

[Research index](README.md)
