# Reforge 1.0.0 Beta 2

Beta 2 adds a guided in-app testing workflow and the website handoff used to collect structured feedback and optional conversion-display waitlist interest.

## Download both files

- `Reforge-Setup-1.0.0-beta.2-win-x64.exe` — self-contained Windows 10/11 installer.
- `Reforge-Beta-Asset-Samples.zip` — sample SVG artwork used by the asset, asset-group, and blank-template wizard tasks.

SHA-256 checksum files are attached for both downloads.

## Beta Wizard

The right-side Beta Wizard guides testers through:

1. Reading Help.
2. Copying a factory template.
3. Making and saving five distinct template edits.
4. Exporting the edited template.
5. Reimporting it as a copy.
6. Importing a normal asset.
7. Importing and temperature-mapping an asset group.
8. Building a blank template with five distinct assigned assets and the mapped temperature group.
9. Completing the upload-wizard walkthrough without requiring hardware or performing an upload.

Quantified tasks now include progress bars. Each task has a help button for notes and blocker reports. After all tasks are completed or resolved, Reforge securely opens the Beta Onboarding form. Joining the conversion-display waitlist is optional and requires email verification.

## Other Beta 2 changes

- Startup no longer waits more than four seconds before opening the main window; remaining asset loading continues in the background.
- Factory asset groups load as groups instead of inflating startup counts with every section.
- Duplicate factory/default assets were removed and default templates reuse the shared asset records.
- New templates support Fahrenheit or Celsius, and the editor test slider follows the selected template scale.
- Temperature asset-group mapping supports Fahrenheit or Celsius while retaining one normalized saved mapping.
- Dashboard navigation, modal sizing, help controls, feedback dialogs, and dark-theme contrast were corrected during guided testing.

## Scope and limitations

This remains an app-only beta. End-user Reforge display hardware is not available yet. The final upload action is intentionally disabled; the walkthrough provides a completion action so the software flow can still be tested.

The installer is not code-signed, so Microsoft Defender SmartScreen may warn or block it. Download only from this official repository and verify the attached SHA-256 checksum before installation.
