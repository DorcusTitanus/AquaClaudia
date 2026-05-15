---
name: Apps ship self-contained
description: Default to bundling scripts/resources inside app binaries so they run on any machine, not just Jefe's dev rig
type: feedback
---

# Apps Ship Self-Contained

## The Rule
When building any deliverable application (macOS .app, iOS .ipa, Windows .exe, packaged Electron, packaged Python via PyInstaller/Briefcase, plugin .ccx/.zxp, etc.), **default to bundling all scripts, assets, templates, and runtime dependencies inside the binary.** Do not assume the user's filesystem has a particular path, a sync drive, a repo clone, or a dev tool installed.

## Why
Jefe builds apps to ship to other people (clients like Happenstance, students, designers, etc.). An app that reads `/Volumes/SomeDrive/...` or `~/repo/scripts/...` works only on Jefe's machine, by accident. The moment he hands it off, it breaks.

## How to Apply (by platform)
- **macOS Swift / SwiftUI:** Add resources to **Build Phases → Copy Bundle Resources**. Read them via `Bundle.main.url(forResource:withExtension:subdirectory:)`. Write user output to `~/Library/Application Support/<AppName>/` (use `FileManager.default.url(for: .applicationSupportDirectory, ...)`).
- **iOS:** Same as macOS — Bundle for read, Documents/App Support for write.
- **Adobe UXP (Photoshop/InDesign plugins):** Plugin manifest's `requiredPermissions` plus `manifest.json` resources. Everything that ships with the plugin lives inside the .ccx archive.
- **Electron:** Bundle assets in `app.asar` via electron-builder's `extraResources`. Read via `app.getAppPath()`.
- **Python frozen apps (PyInstaller/Briefcase/py2app):** Use `sys._MEIPASS` (PyInstaller) or `__file__` resolution patterns. Avoid `os.getcwd()`.

## Trade-off vs External Files
External files (option 2 from the ChroniCal discussion) are faster for *development iteration* — edit script, click button, no rebuild. But that speed comes at the cost of shipability. Default to bundled; if dev iteration is painful, add a hybrid pattern (read from bundle, override with env var or debug-only path) rather than making external the default.

## Anti-Patterns to Reject
- Hardcoded `/Volumes/...` or `~/Documents/...` paths in shipping code
- `repoRoot` / `let projectPath = "/Users/jefe/..."` constants
- Assuming Python interpreter at a specific path (use `Bundle.main` or `/usr/bin/env python3` if shelling out)
- Assuming sync drives mounted

## Established
2026-05-15 on Shai-Hulud during ChroniCal Wall-mode work. Triggered by realization that the Ghola-hardcoded paths were going to bite Happenstance the moment the .app left Jefe's network. Decision: bundle scripts inside the .app, make this the default for every app project going forward.
