# Reforge 1.0.0 Beta 1

This refreshed 1.0.0 Beta 1 installer replaces the retired beta-walkthrough build. Reforge remains beta software; bugs and incomplete behavior are possible.

## Highlights

- Create and manage display templates for 1st Gen / 480 x 320 and 2nd Gen / 320 x 240 Reforge displays.
- Start from three locked factory templates and copy factory assets into independent editable versions.
- Use the bundled asset library and the first-gen OEM template's new **Center out** temperature-bar asset. The original cumulative bar asset remains available separately.
- Configure template slots, brightness, automatic dimming, automatic black-screen behavior, and optional screensavers through the upload wizard.
- Test compatible USB or Bluetooth connections before uploading a template package.
- Check for app and first-gen Advanced firmware updates from the app's Update screen. App updates download a complete installer; Advanced firmware updates require USB.
- Report beta problems through the app's **Report a Bug** control.
- Review app information, version details, licensing, notices, contribution terms, and branding rules from the About window.
- Installing over a previous version preserves local settings, templates, imported assets, fonts, and backups. Uninstall asks separately before removing user data.

## System Requirements

- Windows 10 or Windows 11, 64-bit.
- A compatible Reforge display and firmware are required for device upload features.
- The installer includes the required .NET 8 desktop runtime.

## Downloads

- `Reforge-Setup-1.0.0-beta.1-win-x64.exe`
- `Reforge-Setup-1.0.0-beta.1-win-x64.exe.sha256`

SHA-256:

```text
6b1a0de7549255fa1f06bcdf4c7d33d3706e27709fcfbd61278d3470a71e117d  Reforge-Setup-1.0.0-beta.1-win-x64.exe
```

Verify the downloaded installer against this checksum before running it.

## Important Installation Notice

This beta installer is not code-signed. Microsoft Defender SmartScreen may warn about or block the installer because the publisher is unknown. Follow the checksum verification and temporary SmartScreen instructions in the repository README, and immediately restore the Windows protection setting after installation.

## Known Beta Limitations

- The installer is unsigned and does not yet have established SmartScreen reputation.
- The Update screen checks published releases but does not silently install app updates. The full installer must still be run by the user.
- In-app firmware updating supports only first-gen Advanced over USB. First-gen Basic has a separate firmware package and requires a compatible flashing workflow.
- The new Basic and Advanced firmware images have not yet been flashed or bench-validated; software build and checksum verification do not establish hardware acceptance.
- USB, Bluetooth, and hardware upload behavior depends on the connected device, firmware version, and display generation and remains part of beta testing.
- This public repository distributes the compiled beta application and support documents only; application source, firmware source, hardware designs, and private development material are not included.

## Reporting Bugs

Use the [Reforge app bug form](https://github.com/gonzoinc/reforge/issues/new?template=bug-report.yml). Include the app version, Windows version, affected area, clear reproduction steps, expected result, actual result, and sanitized screenshots or logs when useful.

## License

The beta application is provided under the [PolyForm Noncommercial License 1.0.0](LICENSE.md), together with the repository notices, commercial licensing information, contribution terms, security policy, and trademark rules.
