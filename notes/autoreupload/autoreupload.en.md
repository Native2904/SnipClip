# AutoReUpload — how we got to this solution

When you edit something in SnipClip's own Lister (Ctrl+E) and save with Ctrl+S, the corrected text goes straight back to the Windows clipboard — not into the file TC downloaded for that view. Even so, TC used to show an upload error every time ("Error uploading file!").

The reason: since version 8.50, Total Commander ships a resync mechanism — originally meant for FTP and similar plugins. If a downloaded file's last-write time changes, TC automatically tries to upload it back into the plugin. Without a proper answer to that attempt, TC shows an error — even though nothing actually went wrong.

We repurpose this mechanism for our local use case: SnipClip just answers the upload attempt with a silent success, without actually pulling in the content (it's already where it needs to be, via the clipboard). `AutoReUpload=2` in `wincmd.ini` under `[Configuration]` makes TC do this automatically instead of asking every time.

## What this means for you

Without `AutoReUpload=2` set, TC may ask whether to upload when you close the Lister window after editing. Clicking "Yes" doesn't cause any harm — SnipClip accepts the attempt and does nothing destructive with it — but it's an unnecessary click that `AutoReUpload=2` saves you.

## Setting

In `wincmd.ini` (Total Commander → Configuration → Change settings directly):

```ini
[Configuration]
AutoReUpload=2
```
