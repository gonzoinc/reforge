# Reforge first-gen Advanced firmware 1.0.2

This ESP32-S3 firmware embeds the updated first-gen OEM template in slot 1. Its temperature bar grows blue downward from the center when colder and red upward when hotter; the opposite color stays off. Valid SD overrides and slot 2 remain supported.

Download the merged BIN, its `.sha256` checksum, and the JSON flash-segment manifest from this release. The compact image is 1,189,840 bytes and has SHA-256:

```text
cddfff1a1ed831ca47de6824ba38108a43c53c7d63ca2c97d2f23196926228e0
```

Firmware 1.0.2 reports its version over USB and Bluetooth. The Reforge app's Advanced-only USB updater validates the image and four occupied regions, then writes those regions without overwriting the NVS settings partition. Do not use this firmware on first-gen Basic or second-gen hardware. A generic full-image flash at address `0x00000000` can overwrite NVS even though the compact image contains padding there.

The image compiled successfully; all four segments and the untouched NVS region were verified locally. This rebuilt image and the in-app update path have **not** been physically flashed or bench-validated. This beta release is not a hardware acceptance result.
