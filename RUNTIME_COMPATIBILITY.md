# HTA runtime compatibility

Duplicate Cleaner remains a Windows HTA hosted by `mshta.exe`, which uses the legacy MSHTML/JScript engine rather than Chromium. This runtime is deprecated and is not equivalent to `X-UA-Compatible=edge`.

The interface no longer depends on CSS custom properties: their values are inlined. Flexbox layouts are the baseline, while the grid layout is only a progressive enhancement inside `@supports`; unsupported `backdrop-filter` rules affect decoration only. Search filter controls also use a flex fallback.

Compatibility checks performed on the available Windows host:

- The HTA launched under `mshta.exe` and remained responsive during a short startup smoke test.
- The application script parsed and initialized at top level under Windows Script Host JScript.
- The embedded PowerShell worker parsed and passed the regression suite in Windows PowerShell.

This is not a guarantee across every Windows/MSHTML version. The HTA has not been tested on every supported Windows release or with every screen size and file-system configuration. Keep WebView2 or another maintained desktop host as a migration option if broader modern UI support is required.

## Release readiness

The available regression suite passes on the current Windows host. It covers duplicate scanning and cleanup, search size boundaries and excluded-folder pruning, large-file filtering and cleanup, cancellation and pause/resume, partial failures, quarantine restore, and Recycle Bin scope validation. The embedded PowerShell worker and HTA JScript are also syntax-checked. These checks do not certify the application for every production environment.

Before distributing a release:

- Test launch, window/taskbar/shortcut icon display, navigation, and layout on each supported Windows version with the target `mshta.exe` runtime.
- Exercise Recycle Bin, quarantine, and permanent deletion only with disposable test data, including access-denied files, locked files, another drive, and occupied restore destinations. Permanent deletion and emptying the Recycle Bin are irreversible.
- Run performance and memory tests against representative large and deeply nested folders, including network shares and cloud-synchronized locations.
- Package the HTA and `DuplicateCleaner-K.ico` together, provide a clear install/update/uninstall procedure, and sign any distributed executable or installer according to the organization's release policy.
- Confirm that the application-data and settings locations are writable under standard (non-administrator) user accounts and document where logs, reports, quarantine, and settings are stored.

**Status: not yet production-certified.** The HTA/MSHTML runtime is deprecated, and this workspace does not include a signed installer or a cross-version Windows test matrix. For a supported, maintainable production deployment, plan a migration to WebView2 or another maintained desktop host before broad distribution.
