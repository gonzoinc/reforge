# Reforge reference plugins

This folder contains complete plugin packages that can be inspected, copied, and adapted for Reforge.

> [!NOTE]
> Plugin support is still under development. These packages can be imported by **Reforge Advanced Plugin Alpha 1** for app-side testing. Firmware export and live on-device plugin data are not complete, and plugins are not included in Reforge 1.0.0 Beta 2.

| Plugin | Purpose | Hardware |
| --- | --- | --- |
| [Reforge CAN Mapper](can-mapper/README.md) | Creates display functions from user-defined, passively received CAN fields | 1st-gen DCC Advanced Version with an external 3.3 V-safe CAN interface |

Each plugin folder contains its readable source files and an import-ready `.rfgp` package when one is available. See [Building Reforge Plugins](../plugin.md) for the complete manifest contract, supported inputs, editor workflow, and safety requirements.
