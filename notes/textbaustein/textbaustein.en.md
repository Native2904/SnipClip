# Textbaustein — how we got to this solution

The starting idea: you're assembling something — a code block from several fragments you copy one after another from different places, or a text built from several building blocks. Before you actually use the finished result somewhere, you want to see and check it as one connected piece — not paste fragment after fragment separately and hope it fits together.

The first thought was to mark several entries in the panel and drag them somewhere in one motion — surely TC could recognize that as "one action". Technically true, via an official but rarely used function (`FsStatusInfo`). Except: TC's own copy dialog doesn't ask for a name at all when multiple files are selected, only a target folder. So "select everything at once and merge" wouldn't have worked anyway.

The simpler solution: copy fragment 1 into a Textbaustein named "y", and later fragment 2 also to "y" — both land at the same target. We just need to decide what happens when something's already there: append instead of overwrite. No multi-select detection needed, no batch tracking — just: same name = same target = gets appended.

## How it works — using a code snippet as an example

Imagine you're assembling a small config file from three fragments copied from three different old projects:

1. Copy fragment 1 (the header section) — lands in the history
2. Drag it onto `! Textbaustein` (F5), type a name, e.g. `config-new`
3. Copy fragment 2 (the middle part), drag it onto `! Textbaustein` again, type the SAME name `config-new` — gets appended, with a blank line in between
4. Append fragment 3 (the closing part) the same way
5. F3 on `config-new` — now shows all three pieces as one connected whole, in exactly the order you appended them
6. Only now, fully assembled, do you copy/paste the result to where it belongs

The individual original fragments stay unchanged in the history — the Textbaustein is an additional composition, not a replacement.

## About the name itself

The name you type when copying is just a fleeting label for this collection spot — not a place where the text ends up staying. A single letter or a short abbreviation is entirely enough, deliberately so: you'll type it again for every single fragment, so short beats descriptive here. The actual, properly named file only comes into existence later, when you paste the finished result where it belongs.

## The limitation worth knowing

Marking several fragments together and dragging them over in one motion does NOT merge them automatically — that would require TC to ask for a shared name during a multi-select copy, which it doesn't. It only works reliably one at a time, in sequence, typing the same name each time — but without any guesswork about which order things landed in.

## Setting

`[textbaustein] separator_blank_lines=1` in `SnipClip.ini` — how many blank lines sit between appended pieces, 0 to 3.
