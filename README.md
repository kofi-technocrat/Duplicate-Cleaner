# Duplicate Cleaner

Duplicate Cleaner is a Windows utility for finding duplicate and large files, reviewing cleanup choices, and recovering disk space. It is currently implemented as an HTML Application (HTA) with an embedded Windows PowerShell worker.

**Owner and copyright:** Kofi Technocrat  
**License:** [MIT](./LICENSE)

> **Important:** Cleanup can permanently remove data. Review every selection and destination before proceeding. Permanent deletion and Empty Recycle Bin cannot be undone. Test with disposable files before using these actions on important data.

## Features

- Find duplicate files by content hash and review each duplicate group together.
- Choose which copy to keep; the suggested copy is based on its location relative to the scan root.
- Search files by name or path, with size, extension, date, and exclusion filters.
- Find large files with minimum and optional maximum size filters, sorting, and result filtering.
- See scan progress, checked-file and match counts, elapsed time, and pause/resume/cancel controls.
- Choose to move selected files to the Recycle Bin, quarantine them for later restoration, or permanently delete them with an additional confirmation.
- Review quarantine batches and restore selected files. If an original path is occupied, restore under a new name or skip it.
- Preview selected-file totals and estimated recoverable space. Hard-linked files and online-only cloud placeholders are handled cautiously.
- Empty the Recycle Bin for the scan folder's drive or all drives, with an explicit confirmation.
- Save reusable scan profiles and export search results and duplicate reports as CSV.
- Keep application logs, reports, settings, and quarantine data outside scanned folders.

## Requirements

- Windows with the HTA host (`mshta.exe`) and Windows PowerShell available.
- `DuplicateCleaner.hta` and `DuplicateCleaner-K.ico` kept together for the application icon.
- Access to the folders being scanned. Some protected files or locations may be inaccessible without additional permissions; the app reports items it could not check in its operation log.

The app is designed to run as a standard user; do not run it as Administrator unless your environment specifically requires that. Elevated access can increase the impact of accidental cleanup.

## Run

1. Download or copy the application files to a folder on the computer.
2. Keep `DuplicateCleaner.hta` and `DuplicateCleaner-K.ico` in the same folder.
3. Double-click `DuplicateCleaner.hta`.

No installer is currently included. For a shortcut, create a Windows shortcut to the HTA and set the shortcut icon to `DuplicateCleaner-K.ico`. The icon shown for `.hta` files themselves in Explorer is controlled by the Windows file association and may not use the application's icon.

## Application data

User data is stored separately from the application files:

- Settings and saved scan profiles: `%APPDATA%\DuplicateCleaner\settings.json`
- Logs, generated reports, and quarantine batches: `%LOCALAPPDATA%\DuplicateCleaner\`

Quarantined files remain on disk and continue to use storage. Keep the quarantine folder until its contents are restored or you intentionally remove them. Uninstalling or deleting the application folder does not remove these AppData files.

## Safety notes

- Moving a file to the Recycle Bin may not free space until the relevant Recycle Bin is emptied.
- Quarantine supports restoration but also consumes disk space.
- Permanent deletion bypasses the Recycle Bin and is irreversible.
- Empty Recycle Bin permanently deletes items in the selected scope, which can include files unrelated to this application.
- Hard links can share the same underlying disk data; removing one name may not reclaim its reported file size.
- Cloud-only placeholders and linked/reparse-point files are not treated as ordinary cleanup candidates.
- Estimated recovery and measured free-space changes are informational; other disk activity and filesystem behavior can affect the actual result.

## Run regression tests

From Windows PowerShell:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tests\DuplicateCleaner.Tests.ps1
```

The suite creates and removes its test files under the system temporary directory. It does not empty the user's Recycle Bin.

## Runtime and production status

This application runs in the legacy MSHTML/JScript HTA runtime, not Chromium. The HTA runtime is deprecated, and `X-UA-Compatible=edge` does not make it equivalent to a modern browser. Automated checks and tests pass on the development Windows host, but this project has not been certified across Windows versions or hardware configurations.

Before broad distribution, test supported Windows versions, scan performance on representative local/network/cloud folders, cleanup and restore behavior with disposable data, and standard-user permissions. An MSI can install the HTA and icon, but it does not replace or modernize the HTA runtime. For a maintainable production application, plan a migration to a supported desktop host such as .NET with WebView2.

See [RUNTIME_COMPATIBILITY.md](./RUNTIME_COMPATIBILITY.md) for compatibility checks and release-readiness notes.
