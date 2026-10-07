# Reforge Advanced Plugin Alpha 1

This prerelease is an early user-facing test of Reforge Plugin API v1 for the **1st-gen DCC Advanced Version**. The 1st-gen DCC Basic Version and 2nd-gen DCC boards do not support plugins.

## Downloads

- `Reforge-Setup-1.1.0-plugin-alpha.1-win-x64.exe` — self-contained Windows 10/11 installer.
- `Reforge-CAN-Mapper.rfgp` — import-ready configurable CAN reference plugin.
- `Reforge-Setup-1.1.0-plugin-alpha.1-win-x64.exe.sha256` — SHA-256 checksum for the installer.
- `Reforge-CAN-Mapper.rfgp.sha256` — SHA-256 checksum for the plugin package.

## What can be tested

- Import and validate `.rfgp` plugin packages.
- Review a plugin's description, author, permissions, and Advanced connector assignments.
- Enable, disable, update, and remove installed plugins.
- Open plugin Settings and create up to 16 user-defined CAN fields with CAN ID, frame type, bit layout, data type, byte order, scaling, units, limits, and stale timeout.
- Use enabled CAN fields as friendly Function choices in user-created custom templates.
- Import and inspect the included Reforge CAN Mapper reference package.

## CAN Mapper hardware reference

The public repository includes an editable KiCad CAN interface example with a schematic, routed PCB, component list, and board images. It is an **untested reference circuit**, not a validated production accessory. Never connect CAN-H, CAN-L, 5 V, or 12 V directly to the Reforge plugin connector.

## Current limitations

- Firmware export, on-device plugin adapters, and live plugin-value rendering are not implemented yet.
- Type-aware live previews and alarm editing are not complete.
- Graph assets and the finished Graph workflow are coming soon.
- Plugins are declarative packages; Plugin API v1 does not execute third-party DLLs, scripts, or native code.
- The installer is not code-signed, so Microsoft Defender SmartScreen may warn or block it. Download only from this official repository and verify the attached SHA-256 checksum.

See [Building Reforge Plugins](plugin.md) and the [Reforge CAN Mapper guide](plugins/can-mapper/README.md) for the complete package, wiring, and editor workflow.
