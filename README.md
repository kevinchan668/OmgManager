# OmgManager — Sony Walkman OMGAUDIO Manager

**Manage your classic Sony Walkman beyond SonicStage.**

OmgManager is a Windows desktop application for managing OMGAUDIO music libraries used by selected classic Sony Network Walkman players. It offers direct playlist editing, music import/export, M3U-based workflows, standalone conversion, and database maintenance, alongside research into Sony's proprietary music-library format.

**[简体中文](README.zh-CN.md)** · **[Installation guide](docs/installation.md)** · **[Releases](https://github.com/kevinchan668/OmgManager/releases)** · **[OMGAUDIO research](docs/research/README.md)**

> **Distribution status:** OmgManager V9.3.13 is intended for binary release. The application's source code is **not publicly available at this stage**. This is an independent, unofficial project, not endorsed by Sony.

## Features

| Feature | Description |
| --- | --- |
| Music library management | Browse tracks, albums and artists; import and remove music in supported OMGAUDIO libraries. |
| Direct playlist editing | Create, rename, delete, reorder and edit playlists on the device; back up and restore playlists through the app's PL format. |
| M3U import | Import music from existing local M3U lists, or create device playlists from matching library tracks. |
| Music export | Export compatible tracks from a Walkman to a PC; output depends on source format and protection. |
| Standalone conversion | MP3 conversion works without connecting a Walkman; ATRAC encoding requires an optional external Sony tool. |
| Database maintenance | Initialize an OMGAUDIO folder on a PC without connecting a device, and perform selected library maintenance and repair operations. |

## Screenshots

The project includes screenshots of the library, albums and artists, playlist management, M3U import, audio conversion, export and database maintenance. See the [feature-by-feature screenshot guide](docs/screenshots.md) and the [original screenshot archive](https://drive.google.com/file/d/1MAxqb5qpS63dOKp-a4rLRIrddwVUqL5T/view). The screenshots were captured from V9.3.12; the published application is V9.3.13.

## Download and install

**Target release: V9.3.13.** Obtain the official ZIP from [GitHub Releases](https://github.com/kevinchan668/OmgManager/releases) **when the release is published**. The desktop application targets Windows and .NET Framework 4.8.

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

Read the [consolidated English research overview](docs/research/README.md). Observations, interpretations and unresolved hypotheses are distinguished explicitly.

## Important limitations

- Database writes may permanently damage a device music library without a valid backup.
- Not all device profiles or functions have been validated on every firmware.
- DRM-protected content is not guaranteed to be exportable or convertible.
- ATRAC encoding depends on a separately obtained proprietary utility and may be unavailable.
- Source code is not part of this public repository at present.

## Legal and attribution

OmgManager is an independent project, not affiliated with or endorsed by Sony. Walkman, SonicStage and other product names are trademarks of their respective owners. Third-party executable redistribution requires compliance with the relevant licenses and attribution obligations. No open-source license for the OmgManager application code is implied by this documentation repository.
