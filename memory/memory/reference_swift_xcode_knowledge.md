# Reference — Swift / SwiftUI / Xcode lessons

**Status:** Started 2026-05-16 as a running knowledge base for Mac app development. Each entry is a real bug or pattern surfaced during ChroniCal / future Swift project work. Add as we accumulate — fresh entries at the top so the most recent is easiest to find.

## Why this memo
Jefe is building ChroniCal (and will build more Mac apps after that — Splat Haus is the next confirmed one). Swift + SwiftUI + AppKit has a lot of subtle behavior that's easier to look up than re-derive. Capture lessons here so future instances don't re-debug from scratch.

---

## SwiftUI: `@AppStorage` requires reading the value to subscribe
**Symptom:** A view declared `@AppStorage("foo")` but its body didn't re-render when the storage value changed.
**Cause:** SwiftUI's dependency tracking works on property *access*, not property *declaration*. Just declaring `@AppStorage("foo") private var foo: Double = 1.0` doesn't subscribe — the property must be **read** during the view's `body` execution to register the subscription.
**Fix:** Read the value in body, even if just `let _ = foo`. Better: use the read for something useful so it isn't a "magic dependency" line.

```swift
@AppStorage("textScale") private var textScale: Double = 1.0

var body: some View {
    // Reading textScale here registers the @AppStorage subscription.
    // Doing it as an assignment to a static var is double duty.
    HapFont.scale = textScale
    return HSplitView { … }
}
```

**Verified:** ChroniCal v1.0 (2026-05-16). Same rule applies to `@State`, `@Binding`, `@Environment`, `@ObservedObject` — all SwiftUI property wrappers subscribe via access during body, not via declaration.
**Reference:** Apple docs on `AppStorage`; WWDC22 "Demystify SwiftUI" (body invalidation is per-access).

---

## SwiftUI: Static mutable state + view re-render
**Pattern:** When you need a global mutable value (like a font scale or theme override) consumed by many `View` bodies, you can't make a static enum observe Combine publishers. Common approaches:

1. **Static var + parent reads + child reconstruction.** Parent view reads the source-of-truth (`@AppStorage`/`@State`) and assigns to a static var inside its body. Children that the parent re-constructs see the new static value when their own bodies run. Works when the static is consulted during view construction (not just runtime).
2. **Environment value.** Define a custom `EnvironmentKey` and inject via `.environment(\.myKey, value)` at the root. Every view that wants to react declares `@Environment(\.myKey) var value`. Heavier touch but explicit.
3. **`.id(value)` modifier.** Forces SwiftUI to recreate the view tree when `value` changes. Aggressive — any internal `@State` in the subtree is *reset* on each change. Avoid unless your subtree has no critical state.

For ChroniCal we used (1). See the @AppStorage lesson above for the dependency-tracking gotcha.

---

## SwiftUI Commands: menu actions and run-loop modes
**Context:** Keyboard shortcuts and menu items in a SwiftUI `Commands` block fire from the menu / event-tracking run-loop mode. Most SwiftUI redraws process on the default run-loop mode.

**Symptom:** A menu action mutates state, but the visible UI doesn't update until the user clicks somewhere else.
**Workarounds:**
1. `DispatchQueue.main.async { state = newValue }` — hops the mutation onto the next default-mode tick.
2. `Task { @MainActor in state = newValue }` — same idea via async.
3. Most often the actual cause is the dependency-tracking gotcha above (state changes but no view is subscribed) — try fixing that first, the runloop-hop is often a red herring.

**Verified:** ChroniCal ⌘+/⌘-/⌘0 zoom commands (2026-05-16). Tried the runloop hop first — didn't fix. Real cause was the `@AppStorage`-read issue. Kept the `DispatchQueue.main.async` wrap as defensive belt-and-suspenders.

---

## SwiftUI File menu: wiring Save / Save As from commands
**Problem:** Commands live at the App scope; the Save action lives in the View. Need to bridge.

**Pattern: `FocusedValue`.** Define a `FocusedValueKey` keyed to a struct of actions; publish from the view via `.focusedValue(\.myActions, …)`; consume in a `Commands` struct via `@FocusedValue(\.myActions) var actions`.

```swift
struct SaveActions {
    var save: () -> Void
    var saveAs: () -> Void
    var canSave: Bool
}

private struct SaveActionsKey: FocusedValueKey {
    typealias Value = SaveActions
}

extension FocusedValues {
    var saveActions: SaveActions? {
        get { self[SaveActionsKey.self] }
        set { self[SaveActionsKey.self] = newValue }
    }
}

// In View:
.focusedValue(\.saveActions, SaveActions(
    save: { saveXML() },
    saveAs: { saveXMLAs() },
    canSave: …
))

// In App.commands:
struct SaveCommands: Commands {
    @FocusedValue(\.saveActions) private var actions
    var body: some Commands {
        CommandGroup(replacing: .saveItem) {
            Button("Save") { actions?.save() }
                .keyboardShortcut("s", modifiers: .command)
                .disabled(actions?.canSave != true)
            Button("Save As\u{2026}") { actions?.saveAs() }
                .keyboardShortcut("s", modifiers: [.command, .shift])
        }
    }
}
```

**Verified:** ChroniCal v1.0.

---

## SwiftUI Help menu: opening a dedicated window
Add a `Window` scene with an `id`, then in `Commands` use `@Environment(\.openWindow)` to open it.

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup { ContentView() }
        Window("Help", id: "help") { HelpView() }
            .defaultSize(width: 700, height: 640)
            .windowResizability(.contentSize)
        // …
        .commands { HelpCommands() }
    }
}

struct HelpCommands: Commands {
    @Environment(\.openWindow) private var openWindow
    var body: some Commands {
        CommandGroup(replacing: .help) {
            Button("App FAQ") { openWindow(id: "help") }
                .keyboardShortcut("?", modifiers: .command)
        }
    }
}
```

`Window` (macOS 13+) is the right primitive for help-style standalone windows — system-managed, ⌘W closes it, lives independently of the main window.

---

## Xcode: PBXFileSystemSynchronizedRootGroup auto-bundles files
**Xcode 15+** synchronizes folder contents to the project automatically when the folder is registered as a `PBXFileSystemSynchronizedRootGroup`. Drop a new `.swift` or `.py` file into the folder on disk → it auto-appears in the build, no `project.pbxproj` edits needed.

**Check:** look for `PBXFileSystemSynchronizedRootGroup` in `project.pbxproj`. If present, you can just `Write` new source files into the folder.

**Verified:** ChroniCal `gui/ChronCal/ChronCal/` is a synchronized group. Added `LintReviewPane.swift` and `HelpView.swift` by writing files directly; Xcode picked them up on next build.

---

## Xcode MCP: `xcrun mcpbridge` requires xcode-select pointing at Xcode.app
**Path:** `/Applications/Xcode.app/Contents/Developer/usr/bin/mcpbridge` (Xcode 26.3+, ~791KB, owned by root:wheel).

**Setup:**
```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
claude mcp add --scope user --transport stdio xcode -- xcrun mcpbridge
claude mcp list   # expect: xcode ✓ Connected
```

Then in Xcode: Settings → Intelligence → MCP → toggle "Allow external agents to use Xcode tools."

**MCP surfaces load at session start** — restart Claude Code (or open a fresh window) after `claude mcp add` for the tools to appear.

**Trap:** If `xcode-select` points at `/Library/Developer/CommandLineTools` (default after some macOS updates), `xcrun mcpbridge` fails with "unable to find utility." CommandLineTools doesn't ship `mcpbridge` — only the full Xcode.app does.

**Verified:** ChroniCal session 2026-05-16 (Alia).

---

## SwiftUI NSViewRepresentable: re-applying state in `updateNSView`
When a SwiftUI parent re-renders and recreates an `NSViewRepresentable`, the wrapped AppKit view persists and only `updateNSView` is called. Don't put initial-only setup there. Do put state synchronization there — re-applying fonts, colors, text content, etc., on every parent re-render.

```swift
struct XMLEditorView: NSViewRepresentable {
    @Binding var text: String
    var isErrorReport: Bool = false

    func makeNSView(context: Context) -> NSScrollView {
        // One-time NSTextView setup
    }

    func updateNSView(_ scrollView: NSScrollView, context: Context) {
        let textView = scrollView.documentView as! NSTextView
        // Re-apply any state that depends on parent re-render
        if textView.string != text { textView.string = text }
        // Re-apply font when the parent's font scale may have changed
        textView.font = HapFont.nsMono(13)
    }
}
```

**Trap:** State drift if you only set things in `makeNSView`. Symptom: app-level scale changes don't propagate to wrapped AppKit views.

---

## macOS: revealing a file in Finder
Use `NSWorkspace.shared.activateFileViewerSelecting([url])` — single-file selection, opens Finder window, scrolls to and highlights the file.

For just opening a folder (no specific selection): `NSWorkspace.shared.open(url)`.

For opening a file in its default app: `NSWorkspace.shared.open(url)`.

---

## SwiftUI markdown rendering in Text
`Text(LocalizedStringKey(stringContainingMarkdown))` renders inline markdown natively on **macOS 12+**:
- `**bold**`
- `*italic*`
- `` `code` ``
- `[link label](https://url)`

Doesn't support block-level features (headings, lists, code blocks). For richer rendering, use `AttributedString` with `markdown:` initializer, or `Markdown` package.

**Verified:** ChroniCal FAQ HelpView (2026-05-16).

---

## TBD entries (capture when they come up)
- App sandboxing setup + entitlements for sync drive access
- Code signing / notarization workflow for distribution
- py2app vs PyInstaller for bundling Python deps in a Mac app
- Bridging SwiftUI to AppKit menus more deeply (NSMenu manipulation)
- Animations and transitions across view modes
- @Observable macro (Swift 5.9+) vs ObservableObject
- Async/await + structured concurrency patterns in SwiftUI
