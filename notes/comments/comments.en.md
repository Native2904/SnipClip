# Comments for StickySnips & Textbaustein — how we got to this solution

StickySnips and Textbausteine often carry short, not-very-descriptive names — practical when collecting quickly, but once several are open at once, the name alone often isn't enough to tell them apart. The solution was an additional field, independent of the name: a comment.

Three approaches were on the table for this. A real `descript.ion` file in the plugin folder would have meant the comment was only reachable through the actual folder, not directly from the virtual panel — ruled out. A completely new comment field with its own input UI would have duplicated work already done in our existing DescriptEdit plugin, which edits the same kind of file comments — also ruled out. In the end we reused DescriptEdit's proven input *approach*, but as standalone code directly inside SnipClip: no need to install DescriptEdit, just the same logic rebuilt.

A dedicated input window was unavoidable either way, since TC's `RequestProc` interface has no "ask for arbitrary text" type — only username, password, target folder, URL, yes/no. For edit mode we followed the Lister's lead: dark to read, light to edit, Ctrl+E/Ctrl+S to switch and save. The colors deliberately come from `[lister] Theme=`, not the main theme.

The comment field was originally built just for Textbausteine, then extended to StickySnips — both are curated, secured entries with the same recognition problem. The history itself deliberately gets no comments: too many, too transient entries for it to be worth it.

To keep an overview, `! Kommentare` lists every comment that's been set, stacked with the name or category in front of each one.
