# Reforge reference plugins

This folder contains complete plugin packages that can be inspected, copied, and adapted for Reforge.

> [!NOTE]
> Plugin support is still under development and is not included in the current Reforge 1.0.0 Beta 2 public release. These files are provided now so plugin authors can review the package format and prepare compatible integrations.

| Plugin | Purpose | Hardware |
| --- | --- | --- |
| [Reforge CAN Mapper](can-mapper/README.md) | Creates display functions from user-defined, passively received CAN fields | 1st-gen DCC Advanced Version with an external 3.3 V-safe CAN interface |

Each plugin folder contains its readable source files and an import-ready `.rfgp` package when one is available. See [Building Reforge Plugins](../plugin.md) for the complete manifest contract, supported inputs, editor workflow, and safety requirements.
