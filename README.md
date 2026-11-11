# Markdown Preview Lock Mode

![VS Code](https://img.shields.io/badge/VS%20Code-1.74%2B-007ACC?logo=visualstudiocode&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)

**Stop VS Code's Markdown preview from replacing itself.**
Press a single shortcut to turn on *lock mode*: every Markdown preview opens **locked**, as a **new tab in the same editor group**. Preview as many files as you like, and none of them replace each other.

---

## The problem

VS Code's built-in Markdown preview is a single shared preview. It follows whichever Markdown file you're on, so when you preview `b.md`, the preview of `a.md` is gone:

```
Before                                    After (lock mode on)
┌──────────────────────────────────┐      ┌──────────────────────────────────────────────────────────┐
│ a.md │ b.md │ Preview b.md       │      │ a.md │ [Preview] a.md │ b.md │ [Preview] b.md          │
└──────────────────────────────────┘      └──────────────────────────────────────────────────────────┘
  previewing b.md replaced a.md's preview    each file keeps its own preview
```

The built-in workaround is manual. You open a preview, then run **Markdown: Toggle Preview Locking**, and repeat for every file. This extension does it for you and puts the previews where you're working.

## Features

- **One-key toggle:** <kbd>Ctrl</kbd>+<kbd>Cmd</kbd>+<kbd>P</kbd> (macOS) / <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>P</kbd> (Windows/Linux) turns lock mode on and off.
- **Locked previews in the same group:** while lock mode is on, <kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>V</kbd> opens a locked preview as a new tab next to your file, not in a side pane.
- **Auto-lock everything else:** previews opened any other way (the preview icon, right-click → *Open Preview*) are locked the moment they open, or the moment you click on an unlocked one.
- **No duplicates:** previewing a file that already has a locked preview just switches to it.
- **Right-click → *Open Locked Preview*:** available in the Explorer, on editor tabs, and inside Markdown editors, whether lock mode is on or off. Select several files in the Explorer to preview them all at once.
- **Status bar indicator:** a `🔒 MD Preview Lock` item shows while lock mode is on. Click it to turn the mode off.
- **Persistent:** the on/off state is a regular setting, so it survives restarts.
- **Non-invasive:** with lock mode off, VS Code behaves exactly as it normally does.

## Installation

### From a release

1. Download the latest `.vsix` from the [Releases](https://github.com/rushilp7/markdown-preview-lock-mode/releases) page.
2. In VS Code, open the Extensions view (<kbd>Cmd</kbd>+<kbd>Shift</kbd>+<kbd>X</kbd>).
3. Click **⋯** → **Install from VSIX…** and choose the file.

Or from a terminal:

```bash
code --install-extension md-locked-preview-menu-2.1.0.vsix
```

### From source

```bash
git clone https://github.com/rushilp7/markdown-preview-lock-mode.git
cd markdown-preview-lock-mode
npx @vscode/vsce package
code --install-extension md-locked-preview-menu-*.vsix
```

## Usage

1. Press <kbd>Ctrl</kbd>+<kbd>Cmd</kbd>+<kbd>P</kbd>. `🔒 MD Preview Lock` appears in the status bar.
2. Open a Markdown file and press <kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>V</kbd>. A `[Preview] a.md` tab opens next to it.
3. Open another Markdown file and press <kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>V</kbd> again. A second `[Preview] b.md` tab opens, and the first one stays.
4. Press <kbd>Ctrl</kbd>+<kbd>Cmd</kbd>+<kbd>P</kbd> again to go back to VS Code's normal behavior. Previews you already locked stay locked.

> **Tip:** drag a preview tab to the edge of the window to see several previews at once.

## Keyboard shortcuts

| Shortcut (macOS) | Shortcut (Windows/Linux) | Action | When |
|---|---|---|---|
| <kbd>Ctrl</kbd>+<kbd>Cmd</kbd>+<kbd>P</kbd> | <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>P</kbd> | Toggle lock mode | Anywhere |
| <kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>V</kbd> | <kbd>Ctrl</kbd>+<kbd>K</kbd> <kbd>V</kbd> | Open a locked preview (new tab, same group) | Lock mode on, in a Markdown editor |

With lock mode off, <kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>V</kbd> keeps its default behavior (the shared preview to the side).

To change a shortcut, open **Keyboard Shortcuts** (<kbd>Cmd</kbd>+<kbd>K</kbd> <kbd>Cmd</kbd>+<kbd>S</kbd>) and search for `Toggle Lock Mode` or `Open Locked Preview`.

## Commands

| Command | ID |
|---|---|
| Markdown: Toggle Lock Mode for New Previews | `markdownPreviewLockMode.toggle` |
| Markdown: Open Locked Preview | `mdLockedPreview.open` |

## Settings

| Setting | Default | Description |
|---|---|---|
| `markdownPreviewLockMode.enabled` | `false` | Whether lock mode is on. The toggle shortcut flips this, and writes it to whichever scope (user, workspace or folder) currently controls it. |
| `markdownPreviewLockMode.openToSide` | `false` | Set to `true` to open locked previews in the group to the side instead of as a new tab in the same group. |

## How it works

The extension doesn't replace VS Code's Markdown preview. It drives the built-in one:

- **Opening locked previews:** it calls the built-in `markdown.showPreview` command with `{ locked: true }`, which creates a locked preview in the current editor group. With `openToSide` enabled, it calls `markdown.showLockedPreviewToSide` instead.
- **Auto-locking:** it listens for tab changes. When the focused tab is an unlocked Markdown preview (title `Preview <file>`) and lock mode is on, it runs the built-in `markdown.preview.toggleLock`, which acts on the focused preview. That's the same thing you'd do by hand from the Command Palette.

## Known limitations

- **The preview icon** in the editor's top-right corner always opens its preview to the side. Extensions can't change what that built-in button does. The preview is still locked automatically. <kbd>Option</kbd>-click the icon to open it in the same group instead.
- **Only the built-in preview is affected.** Third-party preview extensions (e.g. Markdown Preview Enhanced) have their own single/multiple preview settings.
- **Locked-preview detection uses the tab title** (`[Preview] <file>` vs. `Preview <file>`). This matches VS Code's own titles but may not work with every display language.
- **Previews opened as a custom editor**, for example via `"workbench.editorAssociations": { "*.md": "vscode.markdown.preview.editor" }`, are already one-per-file and never replaced, so the extension leaves them alone.

## Requirements

- VS Code **1.74** or later
- The built-in **Markdown Language Features** extension, which ships with VS Code and is enabled by default

## Project structure

```
.
├── extension.js     # all extension logic (no build step, no dependencies)
├── package.json     # manifest: commands, keybindings, menus, settings
├── .vscodeignore    # files excluded from the packaged .vsix
├── .gitignore
├── README.md
└── LICENSE
```

## Contributing

Issues and pull requests are welcome. To try changes locally, open the folder in VS Code and press <kbd>F5</kbd> (choose **VS Code Extension Development** if prompted) to launch an Extension Development Host. Or package it with `npx @vscode/vsce package` and install the `.vsix`.

## License

[MIT](LICENSE)