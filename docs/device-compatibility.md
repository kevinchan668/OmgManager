# Device compatibility and verification status

This table lists device profiles represented in OmgManager V9.3.13. **A profile is not a certification that every feature works on every firmware or variant.**

| Profile | Scope | Cautions |
| --- | --- | --- |
| NW-A608 | Classic A600-family OMGAUDIO profile | Deletion, maintenance timestamps and Simple Mode behavior need device-specific care. |
| NW-S638F | Later S-series OMGAUDIO profile | Historical related-device experiments do not establish validation for every variant. |
| NW-S706F | S700-family OMGAUDIO profile | Individual functions and firmware combinations require verification. |
| NW-HD5 | Early hard-disk Walkman profile | MP3 import is described as supported; ATRAC import is not presently claimed as supported. |

## Validation levels

- **Profile available:** device-specific settings and initial data exist.
- **Sample analyzed:** database sample has been parsed or compared.
- **Operation tested:** named operation succeeded on a named real device, with a documented build and firmware.
- **Regression verified:** the current release passed the same procedure again.

The public project does not yet provide a complete V9.3.13 regression matrix. Please report exact model, firmware, file formats, operation sequence and observed outcome when filing an issue.

## Safety

Always back up the entire OMGAUDIO directory before importing, deleting, renaming playlists, repairing or rewriting databases. Preserve the original backup outside the device. Do not test on the only copy of a music collection. Encrypted or DRM-protected content may have additional restrictions.

[Return to homepage](../README.md)
