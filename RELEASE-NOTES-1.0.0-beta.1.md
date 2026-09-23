# Reforge 1.0.0 Beta 1

This is the first public beta release of the Reforge AC Display Editor. It is intended for testing and feedback. Bugs, incomplete behavior, and unexpected results are possible.

## Highlights

- Create and manage display templates for 1st Gen / 480 x 320 and 2nd Gen / 320 x 240 Reforge displays.
- Start from three locked factory templates and copy factory assets into independent editable versions.
- Use the bundled release asset library without custom-user backgrounds or screensavers.
- Configure template slots, brightness, automatic dimming, automatic black-screen behavior, and optional screensavers through the upload wizard.
- Test compatible USB or Bluetooth connections before uploading a template package.
- Report beta problems through the yellow **Report a Bug** control in the app header.
- Review app information, version details, licensing, notices, contribution terms, and branding rules from the About window.
- Choose whether Reforge should remove or preserve local settings, templates, imported assets, fonts, and backups during uninstall.

## System Requirements

- Windows 10 or Windows 11, 64-bit.
- A compatible Reforge display and firmware are required for device upload features.
- The installer includes the required .NET 8 desktop runtime.

## Downloads

- `Reforge-Setup-1.0.0-beta.1-win-x64.exe`
- `Reforge-Setup-1.0.0-beta.1-win-x64.exe.sha256`

SHA-256:

```text
c7ed1cbc7333b20afb4c9ec05046879fc2a5c1508a05f49b72cf348e4ce6f80e  Reforge-Setup-1.0.0-beta.1-win-x64.exe
```

Verify the downloaded installer against this checksum before running it.

## Important Installation Notice

This beta installer is not code-signed. Microsoft Defender SmartScreen may warn about or block the installer because the publisher is unknown. Follow the checksum verification and temporary SmartScreen instructions in the repository README, and immediately restore the Windows protection setting after installation.

## Known Beta Limitations

- The installer is unsigned and does not yet have established SmartScreen reputation.
- Reforge does not currently provide automatic application updates; install newer beta versions manually from the official GitHub Releases page.
- USB, Bluetooth, and hardware upload behavior depends on the connected device, firmware version, and display generation and remains part of beta testing.
- This public repository distributes the compiled beta application and support documents only; application source, firmware source, hardware designs, and private development material are not included.

## Reporting Bugs

Use the [Reforge app bug form](https://github.com/gonzoinc/reforge/issues/new?template=bug-report.yml). Include the app version, Windows version, affected area, clear reproduction steps, expected result, actual result, and sanitized screenshots or logs when useful.

## License

The beta application is provided under the [PolyForm Noncommercial License 1.0.0](LICENSE.md), together with the repository notices, commercial licensing information, contribution terms, security policy, and trademark rules.
