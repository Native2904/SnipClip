# FontBrightness / BackgroundBrightness — how we got to this solution

Every theme (Alien Blood, Gruvbox, Everforest, or your own custom colors) should be fine-tunable without having to define a whole new color scheme. The obvious first idea: a free percentage value, e.g. `FontBrightness=-37`. That's exactly what we ruled out.

## Why not a free percentage value

An arbitrary number can never be checked against every theme in advance. `-37` might look fine on Gruvbox while making text nearly invisible on Everforest — nobody tested that, it "probably" works until it doesn't. A free-value text field just shifts the risk onto the person using it, without us ever being able to say "we checked this."

The solution: **fixed, curated steps** instead of a free value. `-3` to `+3`, seven steps total, `0` is neutral. A small, finite set — every single step can actually be checked against every existing theme, instead of hoping on a guess.

## Why seven steps, not more or fewer

Too few steps (e.g. just on/off, or three levels) don't give enough control for real fine-tuning. Too many steps (e.g. twenty) would undermine the whole point again — with twenty steps you realistically couldn't check every single one against every theme anymore, and you're back to the same untested trust as before. Seven steps are fine enough for real adjustment, but still small enough to actually go through all of them.

## Why font and background respond by different amounts

`FontBrightness` shifts all text-ish colors together, ±7 percentage points of lightness per step (max ±21 at step 3). `BackgroundBrightness` moves in a narrower range: only ±5 percentage points per step (max ±15).

The background is the single largest connected area on screen and the most sensitive to contrast problems — push it too far and everything on top of it immediately becomes hard to read. Text takes up far less area and can tolerate a somewhat bigger shift before it actually gets uncomfortable. Hence two differently narrow ranges instead of one shared value for both.

## Why HSL lightness instead of a plain RGB shift

A simple shift of all RGB channels by the same amount would quickly wash out or muddy saturated theme colors. Instead, each color is first converted to HSL, only the lightness value gets shifted, then converted back to RGB — hue and saturation stay intact, only brightness changes, the way you'd actually expect.

## The safety net that stays regardless

Even with curated individual steps: nothing stops someone from setting `FontBrightness=3` AND `BackgroundBrightness=3` at the same time — a combination that was each checked individually but can still end up too low-contrast together. That's why SnipClip additionally checks the actual contrast between foreground and background at load time. If it comes out too low, a safe fallback (the unadjusted theme) kicks in automatically, and a warning line appears in the Alt+Enter window — curated steps alone aren't quite enough on their own, the runtime check stays as a second layer.

## Setting

```ini
[Theme]
FontBrightness=0
BackgroundBrightness=0
```

Both -3 to +3, default 0 (unchanged).
