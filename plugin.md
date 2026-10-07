# Building Reforge Plugins

Reforge Plugin API v1 lets authors add input data to Reforge through a validated, declarative package. A plugin describes its hardware requirements, input adapter, data functions, settings, and optional assets in `plugin.json`. Reforge owns the drivers, scheduling, validation, editor integration, and rendering.

Plugins do not contain executable code. There is no public C++, DLL, script, HTTP, or native runtime API in v1.

> [!IMPORTANT]
> Plugin API v1 targets only the manufactured **1st-gen DCC Advanced Rev B 1.0** board (`reforge-first-gen-rev-b-v1`). The 1st-gen DCC Basic versions do not support plugins, and neither do 2nd-gen DCC boards.

> [!NOTE]
> This guide documents the plugin v1 authoring contract while plugin support is still under development. Plugins are not included in the current Reforge 1.0.0 Beta 2 public release. Development builds can import, validate, configure, enable, update, and remove packages and expose enabled plugin functions in the custom-template editor. Firmware export, on-device adapters, and live plugin-value rendering remain unfinished.

## Quick start

1. Create a folder containing `plugin.json` at its root.
2. Choose one supported runtime adapter: `canSignals` or `gpioDigital`.
3. Declare the expansion signals, permissions, and functions the plugin uses.
4. Optionally add `README.md`, an icon, and other files under `assets/`.
5. ZIP the contents of the folder, not the folder itself, and rename the archive to `.rfgp`.
6. In a plugin-capable Reforge release, open **Plugins**, select **Import Plugin**, and choose the `.rfgp` file.
7. Review its permissions and configuration, then explicitly enable it. New imports and updates are disabled by default.

An installed and enabled plugin's functions appear in the Function picker for user-created custom templates. Reforge saves a collision-safe binding such as `com.example.reforge.rpm.rpm`, while the UI displays the function's friendly `function_name`.

## Package layout

```text
my-plugin/
  plugin.json       required
  README.md         optional
  assets/           optional
    plugin.ico
    warning.svg
```

The `.rfgp` file is a ZIP archive with `plugin.json` directly at the archive root.

Only these package locations are accepted:

- `plugin.json`
- `README.md`
- files below `assets/`

Allowed file types are `.json`, `.template`, `.txt`, `.md`, `.svg`, `.png`, `.jpg`, `.jpeg`, `.webp`, `.gif`, `.ico`, `.ttf`, `.otf`, `.rgb565`, and `.rgb565a`. A package may contain at most 512 files, each file may be at most 8 MiB, and the unpacked package may be at most 32 MiB. The manifest may be at most 256 KiB.

Package paths must be relative and portable. Reforge rejects path traversal, absolute paths, links/reparse points, duplicate case-insensitive paths, executable or script content, unknown root locations, and unsupported file types.

## Build an `.rfgp` package

From the plugin folder in PowerShell:

```powershell
$items = @("plugin.json")
if (Test-Path -LiteralPath "README.md") { $items += "README.md" }
if (Test-Path -LiteralPath "assets") { $items += "assets" }

Compress-Archive -Path $items -DestinationPath "..\my-plugin.zip" -Force
Move-Item -LiteralPath "..\my-plugin.zip" -Destination "..\my-plugin.rfgp" -Force
```

On Linux or macOS:

```bash
zip -r ../my-plugin.rfgp plugin.json README.md assets/
```

Use a numeric dotted version such as `1.0.0`. To update an installed plugin, keep the same plugin `id` and increase `version`.

## Public API surfaces

The plugin API is expressed through manifest fields rather than callable code endpoints.

| Surface | Manifest value | Purpose | Current app support |
| --- | --- | --- | --- |
| Passive CAN input | `runtime.kind: "canSignals"` | Decode receive-only CAN frames into functions. | Manifest/package validation implemented; device runtime pending. |
| Digital input | `runtime.kind: "gpioDigital"` | Read a switch or conditioned digital signal. | Manifest/package validation implemented; device runtime pending. |
| Fixed author settings | `configuration.kind: "declaredSettings"` | Show typed settings declared by the package. | UI and persistence implemented. |
| User-created CAN fields | `configuration.kind: "canSignalMappings"` | Let users define up to 16 CAN fields in Reforge Settings. | UI, validation, persistence, and editor function discovery implemented; device runtime pending. |
| Plugin functions | `functions` | Publish typed values to the custom-template Function picker. | Catalog and editor discovery implemented; live values pending. |
| Main-screen alerts | `mainScreenWidgets` | Declare bounded, host-rendered alert content and triggers. | Contract validation exists; editor/runtime support is pending. |
| Plugin-owned screens | `screens` | Reserved manifest field. | Not supported; keep it empty. |

## Permissions

Declare only the permissions the package needs.

| Permission | Allows |
| --- | --- |
| `ac.read` | Read-only access to decoded A/C state when a supported binding is available. |
| `exp.io.read` | Claim and read an allowed expansion input. Required when `hardware.expansion` is not empty. |
| `can.read` | Use the receive-only `canSignals` adapter. |
| `assets.use` | Include or reference files under `assets/`. |

No public v1 permission allows GPIO output, CAN transmission, OBD-II requests, UART writes, direct display drawing, touch access, SD access, BLE access, USB access, or modification of core A/C state.

## 1st-gen DCC Advanced hardware endpoints

Plugin manifests claim logical connector signals on the 1st-gen DCC Advanced Rev B board, never raw GPIO numbers. These endpoints do not exist as a plugin contract on the 1st-gen DCC Basic boards.

| Signal | Rev B connector | Supported modes | Notes |
| --- | --- | --- | --- |
| `EXP_IO1` | J2 pin 4 | `gpio.digitalInput`, `twai.rx` | Direct, unprotected 3.3 V GPIO18 input. |
| `EXP_IO2` | J2 pin 5 | `gpio.digitalInput`, `twai.listen.txIdle` | Direct, unprotected 3.3 V GPIO15. Public plugins cannot drive it; TX-idle is host-owned. |
| `EXP_IO3` | J2 pin 6 | `gpio.digitalInput` | Direct, unprotected 3.3 V GPIO7 input. |
| `EXP_POWER` | J2 pin 7 | none | No-connect and unavailable to plugins. |
| `EXP_GND` | J2 pin 8 | reference only | Signal ground; not a power source. |

> [!WARNING]
> Never connect vehicle CAN-H, CAN-L, 5 V, 12 V, or another unconditioned vehicle signal directly to `EXP_IO1` through `EXP_IO3`. These are unprotected ESP32-S3 3.3 V pins. CAN requires a separately powered, protected CAN transceiver with 3.3 V-safe logic. J2 does not provide expansion power.

## Example: configurable passive CAN mapper

This is the recommended package shape when users should define their own CAN IDs and bit fields in Reforge. The host-owned Settings UI creates the functions; therefore `runtime.signals` and `functions` start empty.

```json
{
  "formatVersion": 1,
  "id": "com.example.reforge.can-mapper",
  "name": "Example CAN Mapper",
  "version": "1.0.0",
  "description": "Create display functions from passive CAN broadcasts.",
  "author": "Example Author",
  "apiVersion": 1,
  "permissions": [
    "exp.io.read",
    "can.read"
  ],
  "hardware": {
    "boardProfile": "reforge-first-gen-rev-b-v1",
    "expansion": [
      { "signal": "EXP_IO1", "mode": "twai.rx" },
      { "signal": "EXP_IO2", "mode": "twai.listen.txIdle" }
    ],
    "can": {
      "interface": "twai",
      "mode": "listenOnly",
      "bitrate": 500000,
      "rx": "EXP_IO1",
      "txIdle": "EXP_IO2",
      "transceiver": "external-3v3-logic"
    }
  },
  "runtime": {
    "kind": "canSignals",
    "signals": [],
    "inputs": []
  },
  "settings": [],
  "functions": [],
  "screens": [],
  "mainScreenWidgets": [],
  "configuration": {
    "kind": "canSignalMappings",
    "maxFields": 16
  }
}
```

After importing it, open the plugin's **Settings** and add CAN fields. Each field accepts:

- Friendly name.
- PID / CAN ID in hexadecimal, from `0x0` through `0x1FFFFFFF`.
- Standard or extended frame selection. Standard IDs cannot exceed `0x7FF`.
- Byte offset `0-7`, bit offset `0-7`, and bit length `1-64`; the selected bits must fit in one eight-byte frame.
- Byte order: `little` or `big`.
- Value type: `unsigned`, `signed`, `boolean`, or `enum`.
- Scale and offset.
- Units, decimal precision `0-6`, optional minimum/maximum, and stale timeout `0-60000` ms.
- A comma-separated state list for enum fields.

Reforge assigns each field a stable internal ID, so changing its friendly name does not break existing template bindings.

## Example: fixed CAN signal

Use a fixed declaration when the plugin author controls the CAN layout. This example reads a 16-bit big-endian value from bytes 0 and 1 of standard CAN ID `0x123`, then applies `value * 0.25`.

```json
{
  "formatVersion": 1,
  "id": "com.example.reforge.engine-speed",
  "name": "Engine Speed",
  "version": "1.0.0",
  "description": "Publishes engine speed from a fixed passive CAN frame.",
  "author": "Example Author",
  "apiVersion": 1,
  "permissions": ["exp.io.read", "can.read"],
  "hardware": {
    "boardProfile": "reforge-first-gen-rev-b-v1",
    "expansion": [
      { "signal": "EXP_IO1", "mode": "twai.rx" },
      { "signal": "EXP_IO2", "mode": "twai.listen.txIdle" }
    ],
    "can": {
      "interface": "twai",
      "mode": "listenOnly",
      "bitrate": 500000,
      "rx": "EXP_IO1",
      "txIdle": "EXP_IO2",
      "transceiver": "external-3v3-logic"
    }
  },
  "runtime": {
    "kind": "canSignals",
    "signals": [
      {
        "function": "rpm",
        "canId": "0x123",
        "byteOffset": 0,
        "bitOffset": 0,
        "bitLength": 16,
        "endian": "big",
        "scale": 0.25,
        "offset": 0
      }
    ],
    "inputs": []
  },
  "settings": [],
  "functions": [
    {
      "id": "rpm",
      "function_name": "Engine Speed",
      "description": "Current engine speed",
      "type": "number",
      "units": "rpm",
      "min": 0,
      "max": 9000,
      "precision": 0,
      "states": [],
      "assignments": ["text", "digits", "gauge", "bar", "graph"],
      "updateHz": 10,
      "staleMs": 1000,
      "fallback": 0,
      "defaultPreview": 1500
    }
  ],
  "screens": [],
  "mainScreenWidgets": [],
  "configuration": {
    "kind": "declaredSettings",
    "maxFields": 0
  }
}
```

Supported CAN bitrates are `125000`, `250000`, `500000`, and `1000000`. The interface must be `twai`, mode must be `listenOnly`, and transceiver must be `external-3v3-logic`.

## Example: digital input

This plugin publishes a conditioned active-low switch connected to `EXP_IO3`.

```json
{
  "formatVersion": 1,
  "id": "com.example.reforge.warning-input",
  "name": "Warning Input",
  "version": "1.0.0",
  "description": "Publishes a conditioned digital warning input.",
  "author": "Example Author",
  "apiVersion": 1,
  "permissions": ["exp.io.read"],
  "hardware": {
    "boardProfile": "reforge-first-gen-rev-b-v1",
    "expansion": [
      { "signal": "EXP_IO3", "mode": "gpio.digitalInput" }
    ]
  },
  "runtime": {
    "kind": "gpioDigital",
    "signals": [],
    "inputs": [
      {
        "function": "warningActive",
        "signal": "EXP_IO3",
        "pull": "up",
        "invert": true,
        "debounceMs": 25
      }
    ]
  },
  "settings": [],
  "functions": [
    {
      "id": "warningActive",
      "function_name": "Warning Active",
      "type": "boolean",
      "states": [],
      "assignments": ["visibility", "alert", "text", "assetGroupBinding"],
      "updateHz": 10,
      "staleMs": 1000,
      "fallback": false,
      "defaultPreview": false
    }
  ],
  "screens": [],
  "mainScreenWidgets": [],
  "configuration": {
    "kind": "declaredSettings",
    "maxFields": 0
  }
}
```

Digital inputs support pull modes `none`, `up`, and `down`, inversion, and a debounce time from 0 through 5000 ms. A digital input must publish to a declared boolean function and must reference an expansion signal claimed as `gpio.digitalInput`.

## Declared settings

Add package-owned settings to `settings`. Reforge renders and persists these values in the standard Settings dialog.

```json
"settings": [
  {
    "id": "showUnits",
    "name": "Show units",
    "type": "boolean",
    "default": true,
    "values": []
  },
  {
    "id": "displayMode",
    "name": "Display mode",
    "type": "enum",
    "default": "street",
    "values": ["street", "track"]
  },
  {
    "id": "label",
    "name": "Label",
    "type": "text",
    "default": "RPM",
    "values": []
  }
]
```

Supported types are `boolean`, `int32`, `uint32`, `number`, `text`, and `enum`. Every setting requires a type-correct default. Text is limited to 256 characters. Enum values are case-sensitive, unique, and limited to 64 entries. A package may declare at most 32 settings.

Settings currently provide app-side configuration and persistence. Do not assume a setting can be substituted into a runtime adapter field until firmware export defines that mapping.

## Functions and editor bindings

Each fixed function has:

- `id`: stable local identifier, 1-64 characters, starting with a letter and containing only letters, numbers, `_`, or `-`.
- `function_name`: friendly label shown in Reforge.
- `type`: `boolean`, `number`, `enum`, or `text`.
- Optional `units`, numeric `min`/`max`, and `precision` from 0 through 6.
- `states`: required for `enum`; empty for other types.
- `assignments`: editor capabilities the function may drive.
- `updateHz`: 1-20 Hz.
- `staleMs`: non-negative timeout.
- Optional type-correct `fallback` and `defaultPreview` values.

Supported assignment values are:

- `visibility`
- `alert`
- `text`
- `digits`
- `gauge`
- `bar`
- `graph`
- `assetGroupBinding`

Only declare assignments that make sense for the function type. The editor currently discovers enabled plugin functions, but type-aware live preview/rendering and the Gauge, Bar, Graph, and Alert workflows are still being implemented.

## Optional main-screen alert declaration

The contract can validate a bounded alert declaration, but alert placement, editing, export, and device rendering are not complete. Treat this as forward-facing API, not a currently usable device feature.

```json
"mainScreenWidgets": [
  {
    "id": "warningAlert",
    "name": "Warning Alert",
    "type": "alert",
    "content": {
      "kind": "text",
      "text": "WARNING"
    },
    "trigger": {
      "function": "warningActive",
      "operator": "equals",
      "value": true
    },
    "colors": {
      "foreground": "#ff1f1f",
      "background": "transparent"
    },
    "dismissal": {
      "mode": "tapOrTriggerCleared",
      "timeoutMs": 0
    },
    "severity": "critical"
  }
]
```

Supported trigger operators are `equals`, `lessThan`, `lessThanOrEqual`, `greaterThan`, `greaterThanOrEqual`, `between`, `outsideRange`, `stale`, `error`, and `unavailable`. Numeric operators require a number function. Timed dismissal requires `timeoutMs` from 1000 through 120000.

## Manifest rules and limits

- JSON property names and enum values are case-sensitive.
- Unknown JSON properties are rejected.
- `formatVersion` and `apiVersion` must both be `1`.
- Plugin IDs use lowercase reverse-domain form, for example `com.example.reforge.engine-speed`, and may be at most 128 characters.
- Names may be at most 80 characters, descriptions 400, authors 120, and versions 32.
- A plugin may declare at most 16 fixed functions or configurable CAN fields.
- At most four plugins and 48 total functions may be enabled on one device profile.
- A package may declare at most four main-screen widgets.
- `screens` must be empty because plugin-owned screens are not supported.
- Enabled plugins cannot claim the same expansion signal.
- Built-in OEM templates remain locked. Plugin functions are for user-created custom templates.

## Import and test checklist for plugin-capable builds

1. Confirm `plugin.json` is at the package root.
2. Confirm the plugin ID is unique and the version is numeric and higher than the installed version when updating.
3. Import the `.rfgp` from **Plugins > Import Plugin**.
4. Read every validation error; Reforge reports the manifest path and reason.
5. Open **Settings** and confirm declared settings or CAN fields save and reopen correctly.
6. Enable the plugin and confirm its friendly function names appear in the Function picker for a user-created custom template.
7. Disable the plugin and confirm existing bindings remain visible as unavailable rather than being silently deleted.
8. Do not treat successful import or editor discovery as proof of live hardware behavior. Device export/runtime support and physical electrical validation are separate acceptance steps.

Return to the [Reforge project overview](README.md).
