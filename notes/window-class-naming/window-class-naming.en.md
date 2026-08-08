# Window class naming convention — how we got to this solution

Total Commander can have several of our plugins loaded at once — SnipClip, RecentTab, more to come, all in the same TC process. If a plugin registers a background window (like SnipClip's ClipWatch, which listens for clipboard changes) under a generic name like "BackgroundWindow", another plugin running at the same time could accidentally use the exact same name — Windows only knows window classes system-wide, not separated per plugin.

That's why every window and every named Windows message a plugin registers itself gets prefixed with the plugin's name: `TC_SnipClip_BackgroundWnd_v1` instead of just `BackgroundWnd`. This prevents collisions between plugins running at the same time, without the plugins needing to coordinate with each other at runtime at all — the name alone is enough.

The trailing version number (`_v1`) is deliberately included: if the meaning of this window class or message ever changes later (e.g. a new message format), a new version number keeps that change cleanly separated, without confusing old, still-running plugin instances.

## Where this applies in SnipClip

- ClipWatch background window: `TC_SnipClip_BackgroundWnd_v1`
- The registered message between the WFX and WLX (Lister edit → history): `SnipClip_SuppressNextCapture`

Both names live centrally in `snip_shared.h`, so the WFX and WLX are guaranteed to use the exact same value without maintaining it in two places in the code.
