# SnipClip

A Total Commander plugin that turns your clipboard into a searchable, persistent directory. No separate tool, no tray icon, no window of its own sitting around – SnipClip lives entirely inside TC, as a virtual panel under `\SnipClip`.

<img width="1920" height="870" alt="2026-11-04_154122" src="https://github.com/user-attachments/assets/1e95f1d2-10dc-456d-a67d-240995033a27" />

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

**The history is fundamentally immutable** – but not entirely without an escape hatch anymore. Originally, only "! Clear history" (clear everything at once, now with its own confirmation) could ever remove anything. Now, if something wrong ends up in there, a single entry can also be deleted – guarded by its own extra confirmation on top of TC's normal delete prompt. Anyone who prefers the original strict rule can turn this back off.

**Editing works, just not on the original.** F3 opens a snip in SnipClip's own Lister plugin. Ctrl+E switches to edit mode (the background turns light), you correct it, Ctrl+S writes straight back to the clipboard. The history row itself stays unchanged, you just see noted whether and how much it was last edited.

**What matters to you, you pin.** F5 or drag & drop into a category under `! StickySnips` – create categories with F7. Pinned snips survive even once the rest of the history has long rotated out. Inside StickySnips, F6 moves a pinned snip (or a pinned screenshot) to another category, F5 makes an independent copy. A pinned snip can also be dragged into `! Text blocks\Draft` as a fragment – it stays pinned.

**Merge several fragments into one**, before you use them – code fragments or text pieces you copy one after another into the same typed name under `! Text blocks\Draft` get appended there instead of replaced. Done collecting? Renaming out of `Draft\` via F6 secures the result for good. Details on why appending only works one at a time: see the [Textbaustein note](notes/textbaustein/textbaustein.en.html).

**Copy a file instead of text**, and SnipClip ignores it by default. Turn on path resolution and the file path gets captured as text instead, collected under `! Paths`, completely separate from the main history.

**Screenshots land under `! Screenshots`**, saved as `.bmp`, once `[image_capture] FishImage=1` is set: every image is captured except from programs listed in `Exclude=` (e.g. your image viewer). Opt-out rather than a whitelist on purpose – tools like FastStone Capture close their capture window right after writing the image, so the source often can't be determined any more; such images are still captured, named `Screenshot_<number>`. A screenshot SnipClip itself just handed out (F3/Quick View) that comes straight back is recognized as an echo and not stored again. The same image copied twice in a row is stored once (`[image_capture]` `dedup_consecutive=1`, default). Two independent storage limits (`max_screenshots`, `max_total_size_mb`) keep the collection from growing unchecked – whichever hits first trims the oldest unpinned screenshot. Pinned or linked screenshots are never trimmed.

**Screenshots can be pinned too** – either standalone (like text: F5/drag & drop from `! Screenshots` into a normal StickySnips category) or linked to an existing note by having both land in the same subfolder. Linked entries show their own column ("Verknuepft") in `! Screenshots` with the linked note's name. Alt+Enter on a screenshot shows source, capture time, category and file size. Deleting either side of a link only dissolves the link itself – the other element stays fully intact. Details and why it's built this way: see the [linking note](notes/screenshot-linking/screenshot-linking.en.html).

**Every entry automatically shows its source app's icon** in front of the name (extracted from the actual `.exe`) – linked entries get their own chain-link symbol instead. If the source is unknown or that `.exe` is gone, a fallback icon kicks in (built-in, or your own `.ico` via `FallbackSourceIcon=`).

**Optional capture popup** (`[notifications] Enabled=1`): a small window in a screen corner shows a preview of every capture (text or thumbnail) with its source program – never takes the focus, a click dismisses it.

**Optional cloud upload** (`[cloud_upload] Enabled=1`): Alt+Enter gets an "Upload" button – screenshots go to ImgBB, text to Pastebin, the URL lands on the clipboard. Needs your own free API key per service; without one the button stays hidden and nothing leaves your PC.

**New clips appear live**, no manual Ctrl+R needed – whenever the active panel shows SnipClip, it refreshes automatically after every new capture. Other panels are left alone (no pointless re-reading of an FTP or network folder on every copy).

Alt+Enter on a single entry shows source, category (for pinned snips), timestamp (absolute and relative), edit status, and the full content. Alt+Enter on one of the special rows shows an overview instead – runtime, session stats, source ranking, StickySnips counts, edit statistics, actual disk usage.

## Specific

### Language

All panel rows, folders, dialogs and the Lister status bar follow Total Commander's own language: the plugin reads TC's current language file name (`WCMD_DEU.LNG` → section `[deu]`, no language file → `[eng]`) and takes its texts from `SnipClip.lng`. Included: English, German, Russian, Ukrainian, Danish. Missing keys fall back to English. To force a language regardless of TC:

```ini
[Settings]
Language=deu      ; auto (default) = follow TC
```

Row names are recognized in every language of `SnipClip.lng`, so a directory-hotlist path saved while TC ran in German keeps working after switching TC to English.

### Clip names

The panel name is a content preview: whitespace (indentation, line breaks, tabs) is collapsed into single spaces and leading whitespace skipped, so the visible characters show actual content. Characters not allowed in file names become `-`. The `_<number>` suffix keeps names unique. The capture time is not part of the name on purpose – it's already the row's date column and the **Age** column.

### The panel structure: `! menu`

All command rows (`! Pause ClipWatch`, `! Clear history`, `! StickySnips`, `! Paths`, `! Text blocks`, `! Comments`, `! Screenshots`) live tucked under a single row by default: `! menu`. Keeps the root uncluttered – only your actual clips sit there directly visible.

```ini
[menu]
RootButtons=
```

If you'd rather have certain commands directly at root (e.g. because you click into StickySnips often), list their keys comma-separated: `RootButtons=Sticky,Path`. Available keys: `Pause`, `Clear`, `Sticky`, `Path`, `Textbaustein`, `Comments`, `Screenshots`.

Each special row has its own small icon (instead of TC's default file/folder symbol) – makes them easier to tell apart at a glance, especially when several sit next to each other in `! menu`.

### `[history]` – history basics

- **`max_entries`** (default 100) – max entries at once, oldest gets dropped automatically
- **`name_preview_length`** (default 20) – characters used for the panel name
- **`persist_history`** (default 1) – survives a TC restart or not
- **`dedup_consecutive`** (default 1) – two identical copies in a row create only one entry
- **`max_chars`** (default 1000000, 0 = no limit) – larger copies are skipped entirely, not truncated
- **`AllowSingleDelete`** (default 1) – whether individual entries can be deleted at all

### `[Theme]` – look

Its own "Basic" theme ("Alien Blood") as a recognition trait. For consistency with other plugins: `Name=gruvbox|everforest|solarized`, each with `Mode=dark|light`. `NoColors=1` under `[Settings]` disables any theme entirely.

**`FontBrightness=`/`BackgroundBrightness=`** (each -3 to +3, default 0) – fine-tune brightness through fixed, curated steps. Why exactly seven steps, and why font/background respond by different amounts: see the [brightness note](notes/basic-theme/basic-theme.en.html). If a combination ends up too low-contrast, a safe fallback kicks in automatically, with a warning line in the Alt+Enter window.

**`TimeBasedMode=`** (default 0) – when set, the current hour decides light/dark instead of `Mode=` (`LightStartHour=`/`DarkStartHour=`, default 6-18 light). Takes effect live, no restart needed. Only affects the main dialogs (Properties/Overview) - the Lister and the Textbaustein comment mask have their own independent Ctrl+E light/dark switch, unaffected by this.

### `[lister]` – the editor

Deliberately uses a different theme than the rest of the plugin (dark to read, light to edit): `gruvbox`, `everforest`, or `solarized`. Why saving here works without TC's upload error: see the [AutoReUpload note](notes/autoreupload/autoreupload.en.html).

**`MonoFont=`/`MonoFontName=`** – your own monospace font instead of Consolas. Already pre-configured for JetBrains Mono (bundled in the `fonts\` folder, SIL Open Font License).

### `[sticky]` – the second, curated list

**`default_category`** (default "Unsortiert") – kicks in when you drag straight onto `! StickySnips` itself instead of a category.

### `[textbaustein]` – merging fragments

Restructured: appending only works inside `! Text blocks\Draft\` now – the root of `! Text blocks` itself is "secured", nothing gets auto-added there anymore. Finish collecting, then rename out of `Draft\` via **F6** (e.g. `Draft\y` → `config-final`) – changing folder and setting the final name happen in one step, through TC's own rename dialog.

**`separator_blank_lines`** (default 1) – how many blank lines sit between appended pieces.

**`highlight_last_append`** (default 1) – opening a Textbaustein with F3 automatically selects the most recently appended piece (plain Windows text selection, disappears on the first click).

**Comments**: StickySnips and Textbausteine can carry a short recognition note (handy for short names like "y" or "z"). Alt+Enter shows the comment, the "edit" button at the bottom-left opens a small separate mask with **Ctrl+E**/**Ctrl+S** – same convention as the Lister. `! Comments` lists every comment set so far at a glance, stacked. Details, and why this is its own solution rather than DescriptEdit directly: see the [comments note](notes/comments/comments.en.html).

### `[path_resolve]` – file paths instead of text

Off by default (`Enabled=1` to turn it on, `Format=` picks one of the formats below). Once enabled, SnipClip converts file copies to text:

| Format | Example |
|---|---|
| `local` | `C:\Users\Name\File.txt` |
| `forwardslash` | `C:/Users/Name/File.txt` |
| `unc` | `\\server\share\File.txt` |
| `fileuri` | `file:///C:/Users/Name/File.txt` |
| `wsl` | `/mnt/c/Users/Name/File.txt` |

`AllowExtensions`/`AllowFolders` restrict this via a semicolon-separated list. Folders match as whole folder names (`C:\Project` does not also match `C:\Projects`).

### `[Settings]` – misc

- **`RespectClipboardExclude`** (default 1) – honor the password-manager signal
- **`StartPaused`** (default 0) – ClipWatch starts active or deliberately inactive
- **`LiveRefreshAfterCapture`** (default 1) – panel refreshes automatically after a new capture

### Recording from TC start (Autorun)
TC only loads SnipClip when `\SnipClip` is opened for the first time – nothing is recorded before that. If you use the Autorun plugin, you can start SnipClip together with TC. Put this block into `autorun.cfg` **before** `Pragma AutorunFinalizeSection` (also included as `autorun.snipclip.example.cfg`):

```
If FileExist(COMMANDER_PATH & "\plugins\WFX\SnipClip\SnipClip.wfx") Then
  LoadLibrary "%COMMANDER_PATH%\plugins\WFX\SnipClip\SnipClip.wfx" SnipClipAutostart
EndIf
```

- The path must match the entry in `wincmd.ini` under `[FileSystemPlugins]` **exactly**. Always give `.wfx` – on 64-bit TC, Autorun picks the `.wfx64` itself.
- No extra program runs: Autorun loads the same DLL that TC later uses as file system plugin and only calls `SnipClipAutostart`. When you open `\SnipClip` for the first time, everything is already there.
- To switch it off, comment the block out. `StartPaused=1` applies here too.

### Popup hotkey – clips in every program
With `[hotkey] Popup=` (e.g. `Win+Alt+V`) a system-wide key combination opens a small menu with the latest clips and the StickySnips at the mouse pointer – in every program, not only in TC. Pick with the mouse or digit 1–0; SnipClip switches back to the previous program and pastes with Ctrl+V (`AutoPaste=0`: clipboard only). If another program already owns the combination, SnipClip says so once; the status is also shown in the Alt+Enter overview. **Avoid Ctrl+Alt+letter** – on many keyboards Ctrl+Alt is AltGr. Works as soon as SnipClip runs – with the Autorun autostart, from TC start.

### Custom columns
Five extra columns via TC's column configuration: **Edited**, **Diff**, **Source**, **Age**, **Verknuepft** (only populated for screenshots, shows the name of a linked note).

## Architecture, briefly

Two separate DLLs in the same TC process: `SnipClip.wfx`/`.wfx64` (panel, history, StickySnips, Textbaustein, ClipWatch) and `SnipClipLister.wlx`/`.wlx64` (just the Ctrl+E editor). Why both use the `TC_SnipClip_` window/message prefix: see the [window class naming note](notes/window-class-naming/window-class-naming.en.html).

## Installation

**WFX:** Configuration → Options → Plugins → File System Plugins → Configure → Add, select `SnipClip.wfx64`.

**WLX:** Configuration → Options → Plugins → Lister Plugins → Configure → Add, select `SnipClipLister.wlx64` – it registers itself for `.snip` files (detect string `EXT="SNIP"`).

**Settings:** on a new install, copy `SnipClip.example.ini` to `SnipClip.ini` and adjust it. An update never touches your `SnipClip.ini` – take new settings over from `SnipClip.example.ini`; missing keys use their default.

**Recommended:** set `AutoReUpload=2` in `wincmd.ini` – see the [AutoReUpload note](notes/autoreupload/autoreupload.en.html).

## Build

MinGW-w64 (64-bit under `C:\mingw64`, optionally 32-bit under `C:\mingw64\mingw32`). `build_release.bat` builds optimized binaries, `build_debug.bat` debug ones; both assemble a ready-to-ship `release\` folder including `SnipClip.lng`, `icons\`, `fonts\` and `notes\`.

## License

MIT.
