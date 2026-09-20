# Changelog

All notable changes to SnipClip are documented here.

## 1.7

### Fixed

- **32-bit plugin failed to load** ("This is not a valid plugin!", `SnipClip.wfx`). Total Commander requires plain ANSI base exports (no "W" suffix) in addition to the Unicode ones (`FsFindFirstW`, `FsFindNextW`, `FsInitW`, ...) under 32-bit — without them TC rejects the plugin outright, before `FsInitW` is ever called. Added `FsFindFirst`, `FsFindNext`, and `FsInit` as thin wrappers delegating to the existing Unicode implementation. 64-bit TC was never affected (accepts Unicode-only plugins).

- **Ukrainian language (`Language=ukr`) was ignored.** `ukr` was missing from the language-selection check and from automatic detection via the Windows system language (`LANG_UKRAINIAN`). Both added.

- **Plugin's own icon didn't show** (SnipClip displayed a generic folder icon instead, both in TC and in Windows Explorer, on both 32-bit and 64-bit). Cause: `#include <windows.h>` in the same `.rc` file as the `ICON` resource pulled in SIMD intrinsics headers during `windres` compilation, whose numeric-literal syntax threw off `windres`'s own resource scanner ("digit exceeds base") badly enough to silently drop the `ICON` line that followed it, while the dialog resources further down were unaffected. Moved the icon resource into its own `#include`-free `snip_clip_icon.rc`, compiled and linked separately.

- **Heap overflow loading persisted StickySnip text files** (32-bit crash). An odd file size rounded the buffer allocation down instead of up, so `fread()` wrote one byte past the end of the allocation.

- **Heap overflow decoding bitmaps** (screenshot capture-notification popup, 32-bit crash). Pixel bytes copied were sized from the source buffer's remaining length instead of the actual destination buffer size.

- Fixed a small, unguarded buffer write in `FsGetDefRootName` at `maxlen == 1` (removed the redundant first line responsible for it).

### Known, not part of this fix

- 32-bit builds should actually be tested after every significant change going forward, not just built alongside 64-bit — several of the bugs above would otherwise have surfaced individually and immediately, instead of silently stacking up unnoticed.
