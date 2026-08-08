# Own Basic theme — how we got to this solution

Each of our TC plugins has its own theme system (dark/light, several color palettes). The question was: should the "Basic" default theme look identical across every plugin, or should each plugin get its own?

We deliberately chose the latter. When several of our plugins are open at once — SnipClip, RecentTab, more to come — you should be able to tell at a glance which window belongs to which plugin, without having to read the window title first. A shared default theme would defeat that recognition effect.

SnipClip's Basic theme is called **Alien Blood** — dark green, toxic glowing accents, taken from the publicly available Gogh palette (not invented ourselves). RecentTab has its own orange/dark scheme. Both are deliberately different.

The three shared themes (Gruvbox, Everforest, Solarized) are unaffected by this — they look the same across every one of our plugins, since they're fixed, externally-defined palettes rather than a signature design of our own.

## A deliberate gap

Alien Blood currently has no official light variant — the Gogh palette only defines the dark version. Rather than inventing a light variant that doesn't really belong to the palette, `Mode=light` on Basic simply falls back to the same dark values. Anyone who really wants a light Basic look can use `Name=custom` with their own chosen colors.

## Setting

```ini
[Theme]
Name=basic
Mode=dark
```
