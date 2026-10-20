# Reforge first-gen Basic firmware 1.0.0 Beta 1

This ESP32-C3 production image embeds the updated first-gen OEM template. Its temperature bar grows blue downward from the center when colder and red upward when hotter; the opposite color stays off.

Download the merged BIN, its `.sha256` checksum, and the JSON build/segment manifest from this release. The image is 4,194,304 bytes and has SHA-256:

```text
7b043f8d52e4bc090f5a3a9a6c00ec13504d11fb146ac7b8ae7fa302a094cb3a
```

This is a full-flash image for **first-gen Basic only**, written at address `0x00000000`. It is not compatible with the app's Advanced USB updater. A Basic-specific standalone updater is planned but is not included here. Full-image flashing overwrites the complete flash, including any settings/data partitions.

The image compiled successfully and its four merged regions were checked byte-for-byte. This rebuilt image has **not** been flashed or physically validated. Use only with the matching Basic hardware and a supervised, compatible flashing procedure.
