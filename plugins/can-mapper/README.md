# Reforge CAN Mapper reference plugin

Reforge CAN Mapper is a configurable, receive-only CAN plugin for the **1st-gen DCC Advanced Version**. It lets a user describe CAN fields in Reforge Settings and then use those values as functions in custom display templates.

> [!IMPORTANT]
> This package can be imported by **Reforge Advanced Plugin Alpha 1** for app-side testing. Firmware export and live on-device CAN rendering are not complete, and plugins are not included in Reforge 1.0.0 Beta 2.

> [!WARNING]
> Never connect vehicle CAN-H or CAN-L directly to the Reforge expansion connector. A separately powered, 3.3 V-safe CAN transceiver or interface circuit is required.

## Included files

- [`Reforge-CAN-Mapper.rfgp`](Reforge-CAN-Mapper.rfgp) — import-ready plugin package
- [`plugin.json`](plugin.json) — readable plugin manifest
- [`assets/can-mapper.ico`](assets/can-mapper.ico) — plugin icon

The `.rfgp` file is a ZIP-format package containing the manifest, icon, and package README. The readable files beside it are included so authors can inspect and adapt the example without unpacking the package first.

## What the plugin does

- Uses the ESP32-S3 TWAI controller in listen-only mode.
- Receives CAN through connector pin 1 (`EXP_IO1`).
- Uses connector pin 2 (`EXP_IO2`) as the controller-owned transmit-idle signal.
- Defaults to a CAN bitrate of 500 kbit/s.
- Starts without vehicle-specific CAN definitions.
- Lets the user create up to 16 CAN field mappings in the plugin's Settings screen.
- Exposes each saved field as a friendly Function choice in the custom-template editor.

The plugin does not transmit CAN frames, send OBD-II requests, or run executable plugin code.

## Required hardware

This reference targets the **1st-gen DCC Advanced Version**. The Basic Version and 2nd-gen boards do not support plugins.

Use a separately powered CAN interface with 3.3 V-safe logic. Connect it as follows:

| Reforge connector pin | Signal | CAN interface connection |
| ---: | --- | --- |
| 1 | `EXP_IO1` | CAN transceiver receiver output |
| 2 | `EXP_IO2` | CAN transceiver driver input held recessive for listen-only operation |
| 3 | `EXP_IO3` | Not used by this plugin |
| 4 | `EXP_GND` | CAN interface logic ground |

The editable [CAN interface hardware reference](../../hardware/can-interface/README.md) includes a KiCad schematic, routed PCB, component list, and preview images. That circuit is an untested reference design, not a validated production accessory.

## Import and configure

1. In a plugin-capable Reforge build, open **Plugins**.
2. Select **Import Plugin** and choose `Reforge-CAN-Mapper.rfgp`.
3. Review the requested permissions and hardware assignments.
4. Open the plugin's **Settings** screen.
5. Add the CAN fields broadcast by the connected ECU.
6. Enable the plugin after reviewing its configuration.

Each field can define its CAN ID, standard or extended frame type, byte and bit offsets, bit length, byte order, value type, scale, offset, units, precision, limits, and stale timeout. Enum fields can also define a list of display states.

Reforge gives every saved field a stable internal identifier. Renaming its friendly display name does not break an existing template binding.

## Put CAN data on a screen

After enabling the plugin, open a user-created custom template in the screen editor:

1. Add or select an asset.
2. Open the asset's **Function** setting.
3. Choose the mapped CAN field by its friendly name.
4. Configure the asset's formatting and placement.
5. Save the template.

See [Bind plugin data to an asset](../../plugin.md#bind-plugin-data-to-an-asset) for the complete workflow. Alarm support uses the same mapped function; see [Add an alarm or alert](../../plugin.md#add-an-alarm-or-alert).

> [!NOTE]
> Type-aware live previews, alarm editing, firmware export, and on-device plugin rendering are still being completed. Graph assets and the finished Graph workflow are also coming soon.

## Adapt the reference

To create a separate plugin based on this example:

1. Copy this folder.
2. Change the manifest `id`, `name`, `version`, `description`, and `author`.
3. Keep the required Advanced hardware profile and listen-only CAN assignments unless Reforge documents another supported contract.
4. Replace the icon if desired.
5. Rebuild the `.rfgp` package using the instructions in [Building Reforge Plugins](../../plugin.md#build-an-rfgp-package).

Plugin IDs must be unique, stable, lowercase reverse-domain identifiers. Increase the numeric dotted `version` whenever updating an installed plugin with the same ID.
