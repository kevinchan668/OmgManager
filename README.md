# OmgManager — Sony Walkman OMGAUDIO Manager

**Edit playlists directly on your Walkman. Rescue and migrate music libraries. Rebuild OMGAUDIO databases without erasing your collection.**

OmgManager is a Windows desktop application for managing OMGAUDIO music libraries used by selected classic Sony Network Walkman players. It offers direct playlist editing, music import/export, M3U-based workflows, standalone conversion, and database maintenance, alongside research into Sony's proprietary music-library format.

**[简体中文](README.zh-CN.md)** · **[Installation guide](docs/installation.md)** · **[Releases](https://github.com/kevinchan668/OmgManager/releases)** · **[OMGAUDIO research](docs/research/README.md)**

> **Distribution status:** OmgManager V9.3.13 is available as a binary release. The application's source code is **not publicly available at this stage**. This is an independent, unofficial project, not endorsed by Sony.

## Why OmgManager is different

OmgManager is more than a tool for copying music onto a Walkman. It is designed for **direct management of existing on-device OMGAUDIO libraries**, including playlists that are no longer present in a SonicStage PC library.

### Signature capabilities

| Highlight | What it means in practice |
| --- | --- |
| **Edit playlists directly on the player** | Create, rename, delete, reorder and change playlist members **without requiring the same playlist to exist in a local SonicStage library**. Unlike the traditional PC-library-first workflow, the device's existing playlists are the starting point. |
| **Back up and restore playlists in one operation** | Use OmgManager's **PL playlist format** to export and re-import playlist sets. Particularly useful for devices with many carefully curated playlists. |
| **Build a device playlist from M3U** | Use an existing M3U playlist to create a device playlist **when the referenced tracks are already present in the Walkman's library**. M3U-based music import is also supported. |
| **Recover music from OMA containers** | Export supported music from the player's OMA containers back to PC-usable audio formats, subject to codec and protection limitations. Export playlist information as part of a migration workflow. |
| **Move a collection between different Walkman models** | Export music and playlists from one player, then import them into another supported model, allowing for target-device format and protection requirements. This is a **conversion-and-rebuild workflow**, not a claim that OMGAUDIO folders are interchangeable. |
| **Reorganize and repair an existing music library** | The application's library rebuild/repair workflow aims to reorganize database records and music files **in place, retaining the music collection**, toward a clean state comparable to initialization followed by one complete import of tracks and playlists. Make a full backup first; behavior is model-dependent. |
| **Prepare an OMGAUDIO library without connecting a Walkman** | Initialize an OMGAUDIO folder at an arbitrary PC location for offline preparation and research. This does not establish that every created library is valid for every player. |
| **SonicStage handover research** | In tested workflows, OmgManager-generated changes can be accepted by SonicStage without repair/reinitialization even though every byte and timestamp is not identical to Sony output. The precise acceptance constraints vary by model and remain under study. |

**Why this matters:** SonicStage's usual playlist editing workflow relies on a matching PC-side music library. If only the player retains an old playlist, restoring that exact PC environment may be impractical. OmgManager focuses on managing **the collection that is already on the device**.

## Everyday music-management features

| Feature | Description |
| --- | --- |
| Library browsing | Browse tracks, artists, albums and playlists in supported OMGAUDIO libraries. |
| Music import and deletion | Import compatible audio and delete tracks with coordinated database updates. |
| M3U workflow | Use M3U files for music import or playlist creation where matching tracks exist. |
| Standalone conversion | Convert audio without connecting a Walkman; MP3 is available by default, while ATRAC encoding needs a separately supplied compatible encoder. |
| Device-aware database maintenance | Inspect, initialize, rebuild and maintain OMGAUDIO data, with model-specific limitations and backup requirements. |

## Screenshots

### Main interface — Music Library

![OmgManager music library main window](assets/screenshots/02%20Music%20Library.jpg)

*The main music library view. Screenshot captured from V9.3.12; the current binary release is V9.3.13.*

### Playlist editing and M3U import

| Direct playlist management | Edit playlist contents |
| --- | --- |
| ![Device playlists](assets/screenshots/05%20Playlists.jpg) | ![Edit a playlist](assets/screenshots/08%20Edit%20Playlist%201.jpg) |

| Import music using M3U | Export music to a PC |
| --- | --- |
| ![M3U-based import](assets/screenshots/07%20Import%20from%20M3U.jpg) | ![Music export](assets/screenshots/12%20Export%20Music.jpg) |

Browse the [complete, main-interface-first screenshot gallery](docs/screenshots.md) for all 13 screens, including album and artist views, audio conversion, and database maintenance.

## Download and install

**Current release: V9.3.13.** Download the official ZIP from [GitHub Releases](https://github.com/kevinchan668/OmgManager/releases/tag/v9.3.13). The desktop application targets Windows and .NET Framework 4.8.

1. Extract the distribution into a writable folder.
2. Launch `OmgManager.exe`.
3. **Back up the entire OMGAUDIO directory before modifying any device library.**
4. Check [device compatibility](docs/device-compatibility.md) and limitations before using write or repair operations.

### MP3 by default — optional ATRAC encoding

The official V9.3.13 ZIP **does not include `psp_at3tool.exe`**. MP3 conversion and unrelated music-management functions do not require it. ATRAC encoding becomes available only when a compatible encoder is detected.

If you lawfully possess a compatible Sony encoder, put it at `OmgManager/Tool/psp_at3tool.exe` and restart the application:

```text
OmgManager/
├── OmgManager.exe
└── Tool/
    ├── ffmpeg.exe
    ├── lame.exe
    └── psp_at3tool.exe  (optional; supplied by the user)
```

A [third-party GitHub archive](https://github.com/scx567888/ATRAC-Codec-TOOL/blob/master/psp_at3tool.exe) is provided **for reference only**, not as an official Sony distribution or evidence of permission to download, use or redistribute the file. Users are responsible for applicable rights and terms. OmgManager does not distribute or license the Sony encoder.

## Device compatibility

The application contains profiles for **NW-A608**, **NW-S638F**, **NW-S706F** and **NW-HD5**. A device profile is not proof that every function or closely related model has been tested. See [device compatibility](docs/device-compatibility.md) for verification boundaries and cautions.

## OMGAUDIO reverse-engineering research

This project publishes research on the data structures connecting OMA/EA3 audio containers, track identities, classification trees, playlists, transfer records and database maintenance. Main topics include:

- **ContentKey and track identity:** relationships across the OMA header and OMGAUDIO tables.
- **Classification structures:** TREE, GPLB, TPLB, Level, Start and InfoIndex.
- **TREE22:** global playlist membership layout and offset management.
- **TREE01:** transfer/batch records and observed SonicStage synchronization behavior.
- **Timestamp and Simple Mode experiments:** why file changes and modification times cannot be reduced to one universal rule.

Read the [English OMGAUDIO Research Library](docs/research/README.md), including the [five-dimensional compatibility framework](docs/research/multidimensional-compatibility.md), [structural write-back invariants](docs/research/structural-invariants.md), [SonicStage compatibility thresholds](docs/research/sonicstage-compatibility-threshold.md) and [experimental methodology](docs/research/research-methodology.md). Observations, interpretations and unresolved hypotheses are distinguished explicitly.

## Important limitations

- Database writes may permanently damage a device music library without a valid backup.
- Not all device profiles or functions have been validated on every firmware.
- DRM-protected content is not guaranteed to be exportable or convertible.
- ATRAC encoding depends on a separately obtained proprietary utility and may be unavailable.
- Source code is not part of this public repository at present.

## Legal and attribution

OmgManager is an independent project, not affiliated with or endorsed by Sony. Walkman, SonicStage and other product names are trademarks of their respective owners. Third-party executable redistribution requires compliance with the relevant licenses and attribution obligations. No open-source license for the OmgManager application code is implied by this documentation repository.
