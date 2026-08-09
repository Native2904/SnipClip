# SnipClip

A Total Commander plugin that turns your clipboard into a searchable, persistent directory. No separate tool, no tray icon, no window of its own sitting around – SnipClip lives entirely inside TC, as a virtual panel under `\SnipClip`.

<img width="1920" height="862" alt="2026-08-09_081458" src="https://github.com/user-attachments/assets/552e62ff-a180-4be1-9244-d2659b0321cb" />


*[Deutsche Version](README.md) · [Русская версия](README.ru.md)*

## Chapters
- [What it can](#what-it-can)
- [Specific](#specific)
- [Architecture, briefly](#architecture-briefly)
- [Installation](#installation)
- [Build](#build)
- [License](#license)

## What it can

You copy as usual – Ctrl+C, from any program. SnipClip picks that up in the background (ClipWatch, an invisible window registered with Windows for clipboard changes, no polling) and drops the text into the `\SnipClip` panel as a virtual file.

**Passwords stay out.** When an app deliberately copies something it doesn't want tracked – Bitwarden, KeePassXC, 1Password, Chrome incognito – it marks that with an official Windows signal. SnipClip honors it and leaves those copies completely untouched. Honest caveat: this is a voluntary request, not an enforced block – some sources simply don't set the signal. Anyone who wants to capture everything anyway for their own testing can turn that off.

**The history is fundamentally immutable** – but not entirely without an escape hatch anymore. Originally, only "! Verlauf leeren" (clear everything at once) could ever remove anything. Now, if something wrong ends up in there, a single entry can also be deleted – guarded by its own extra confirmation on top of TC's normal delete prompt. Anyone who prefers the original strict rule can turn this back off.

**Editing works, just not on the original.** F3 opens a snip in SnipClip's own Lister plugin. Ctrl+E switches to edit mode (the background turns light), you correct it, Ctrl+S writes straight back to the clipboard. The history row itself stays unchanged, you just see noted whether and how much it was last edited.

**What matters to you, you pin.** F5 or drag & drop into a category under `! StickySnips` – create categories with F7. Pinned snips survive even once the rest of the history has long rotated out.

**Merge several fragments into one**, before you use them – code fragments or text pieces you copy one after another into the same typed name under `! Textbaustein` get appended there instead of replaced. Details on why this only works one at a time: see the [Textbaustein note](notes/textbaustein/textbaustein.en.md).

**Copy a file instead of text**, and SnipClip ignores it by default. Turn on path resolution and the file path gets captured as text instead, collected under `! Pfade`, completely separate from the main history.

**New clips appear live**, no manual Ctrl+R needed – once you've interacted with the panel once (e.g. Alt+Enter), the view refreshes automatically after every new capture.

Alt+Enter on a single entry shows source, category (for pinned snips), timestamp (absolute and relative), edit status, and the full content. Alt+Enter on one of the special rows shows an overview instead – runtime, session stats, source ranking, StickySnips counts, edit statistics, actual disk usage.

## Specific

### The panel structure: `! menu`

All command rows (`! ClipWatch pausieren`, `! Verlauf leeren`, `! StickySnips`, `! Pfade`, `! Textbaustein`) live tucked under a single row by default: `! menu`. Keeps the root uncluttered – only your actual clips sit there directly visible.

```ini
[menu]
RootButtons=
```

If you'd rather have certain commands directly at root (e.g. because you click into StickySnips often), list their keys comma-separated: `RootButtons=Sticky,Path`. Available keys: `Pause`, `Clear`, `Sticky`, `Path`, `Textbaustein`.

### `[history]` – history basics

- **`max_entries`** (default 100) – max entries at once, oldest gets dropped automatically
- **`name_preview_length`** (default 20) – characters used for the panel name
- **`persist_history`** (default 1) – survives a TC restart or not
- **`dedup_consecutive`** (default 1) – two identical copies in a row create only one entry
- **`AllowSingleDelete`** (default 1) – whether individual entries can be deleted at all

### `[Theme]` – look

Its own "Basic" theme ("Alien Blood") as a recognition trait. For consistency with other plugins: `Name=gruvbox|everforest|solarized`, each with `Mode=dark|light`. `NoColors=1` under `[Settings]` disables any theme entirely.

**`FontBrightness=`/`BackgroundBrightness=`** (each -3 to +3, default 0) – fine-tune brightness through fixed, curated steps. Why exactly seven steps, and why font/background respond by different amounts: see the [brightness note](notes/basic-theme/basic-theme.en.md). If a combination ends up too low-contrast, a safe fallback kicks in automatically, with a warning line in the Alt+Enter window.

### `[lister]` – the editor

Deliberately uses a different theme than the rest of the plugin (dark to read, light to edit): `gruvbox`, `everforest`, or `solarized`. Why saving here works without TC's upload error: see the [AutoReUpload note](notes/autoreupload/autoreupload.en.md).

**`MonoFont=`/`MonoFontName=`** – your own monospace font instead of Consolas. Already pre-configured for JetBrains Mono (bundled in the `fonts\` folder, SIL Open Font License).

### `[sticky]` – the second, curated list

**`default_category`** (default "Unsortiert") – kicks in when you drag straight onto `! StickySnips` itself instead of a category.

### `[textbaustein]` – merging fragments

**`separator_blank_lines`** (default 1) – how many blank lines sit between appended pieces. Details: [Textbaustein note](notes/textbaustein/textbaustein.en.md).

### `[path_resolve]` – file paths instead of text

Off by default. Once enabled, SnipClip converts file copies to text:

| Format | Example |
|---|---|
| `local` | `C:\Users\Name\File.txt` |
| `forwardslash` | `C:/Users/Name/File.txt` |
| `unc` | `\\server\share\File.txt` |
| `fileuri` | `file:///C:/Users/Name/File.txt` |
| `wsl` | `/mnt/c/Users/Name/File.txt` |

`AllowExtensions`/`AllowFolders` restrict this via a semicolon-separated list.

### `[Settings]` – misc

- **`RespectClipboardExclude`** (default 1) – honor the password-manager signal
- **`StartPaused`** (default 0) – ClipWatch starts active or deliberately inactive
- **`LiveRefreshAfterCapture`** (default 1) – panel refreshes automatically after a new capture

### Custom columns
Four extra columns via TC's column configuration: **Edited**, **Diff**, **Source**, **Age**.

## Architecture, briefly

Two separate DLLs in the same TC process: `SnipClip.wfx`/`.wfx64` (panel, history, StickySnips, Textbaustein, ClipWatch) and `SnipClipLister.wlx`/`.wlx64` (just the Ctrl+E editor). Why both use the `TC_SnipClip_` window/message prefix: see the [window class naming note](notes/window-class-naming/window-class-naming.en.md).

## Installation

**WFX:** Configuration → Options → Plugins → File System Plugins → Configure → Add, select `SnipClip.wfx64`.

**WLX:** Configuration → Options → Plugins → Lister Plugins → Configure → Add, select `SnipClipLister.wlx64`, then manually assign it to the `snip` extension.

**Recommended:** set `AutoReUpload=2` in `wincmd.ini` – see the [AutoReUpload note](notes/autoreupload/autoreupload.en.md).

## Build

MinGW-w64 (64-bit under `C:\mingw64`, optionally 32-bit under `C:\mingw64\mingw32`). Run `build_debug.bat`, binaries land in `release\`.

## License

MIT.
