# Install OmgManager V9.3.13

## Download

The intended public distribution is a precompiled ZIP attached to the project's [GitHub Releases](https://github.com/kevinchan668/OmgManager/releases). If no release is visible, the binary has **not yet been published here**.

OmgManager is designed for Windows and .NET Framework 4.8. Extract the ZIP to a normal writable directory, keep its folder structure intact, and launch `OmgManager.exe`.

## Important: optional Sony ATRAC encoder

**`psp_at3tool.exe` is NOT included.** The default converter supports MP3. ATRAC encoding is enabled only when a compatible optional Sony executable is detected.

If you lawfully possess such a copy, place it at:

```text
OmgManager/Tool/psp_at3tool.exe
```

Then restart the application. The surrounding `Tool` directory also contains the separately distributed `ffmpeg.exe` and `lame.exe` dependencies.

[Third-party GitHub archive for reference](https://github.com/scx567888/ATRAC-Codec-TOOL/blob/master/psp_at3tool.exe). This is not a Sony-authorized download source and does not establish any right to obtain, use or redistribute the file. You are responsible for checking your permissions.

If the optional file is absent, do not expect ATRAC encoding. MP3 and unrelated management features do not require it.

## Before connecting a player

1. Read the [device compatibility notes](device-compatibility.md).
2. Back up the **complete** OMGAUDIO folder to a separate location.
3. Keep a second backup if testing repair, deletion or rewriting.
4. Verify the player and music collection after any database write.

## Support

When reporting an issue, include the exact device model, firmware, Windows version, application version, requested operation and non-sensitive error details. Do not upload copyrighted music or device-specific secrets.

[Return to homepage](../README.md)
