# Textbaustein — wie wir zu dieser Lösung kamen

Die Ausgangsidee: Du baust dir gerade etwas zusammen — einen Code-Block aus mehreren Fragmenten, die du nacheinander aus verschiedenen Stellen kopierst, oder einen Text aus mehreren Bausteinen. Bevor du das fertige Ergebnis irgendwo einsetzt, willst du es erst als ein zusammenhängendes Stück sehen und prüfen — nicht Fragment für Fragment einzeln einfügen und hoffen, dass es passt.

Der erste Gedanke war, mehrere Einträge im Panel zu markieren und in einem Rutsch irgendwohin zu ziehen — TC sollte das doch als "eine Aktion" erkennen können. Stimmt technisch sogar, über eine offizielle, aber selten genutzte Funktion (`FsStatusInfo`). Nur: TCs eigener Kopieren-Dialog fragt bei mehreren markierten Dateien gar keinen Namen ab, nur ein Zielverzeichnis. Ein "alles auf einmal markieren und zusammenführen" hätte also gar nicht funktioniert.

Die einfachere Lösung: Kopierst du Fragment 1 in einen Textbaustein namens "y", und später Fragment 2 ebenfalls nach "y" — beide landen am selben Ziel. Wir müssen nur entscheiden, was passiert, wenn dort schon was liegt: anhängen statt überschreiben. Kein Multi-Select-Erkennen nötig, kein Batch-Tracking — nur: gleicher Name = gleiches Ziel = wird angehängt.

## Wie es funktioniert — am Beispiel eines Code-Bausteins

Stell dir vor, du baust dir eine kleine Konfigurationsdatei aus drei Fragmenten zusammen, die du aus drei verschiedenen alten Projekten kopierst:

1. Fragment 1 kopieren (der Kopfbereich) — landet in der Historie
2. Per F5 auf `! Textbaustein` ziehen, Namen eintippen, z. B. `config-neu`
3. Fragment 2 kopieren (der mittlere Teil), wieder auf `! Textbaustein` ziehen, denselben Namen `config-neu` eintippen — wird angehängt, mit Leerzeile dazwischen
4. Fragment 3 (der Abschluss) genauso anhängen
5. F3 auf `config-neu` — zeigt jetzt alle drei Stücke als ein zusammenhängendes Ganzes, genau in der Reihenfolge, wie du sie angehängt hast
6. Erst jetzt, fertig zusammengesetzt, kopierst/fügst du das Ergebnis dort ein, wo es hingehört

Die einzelnen Original-Fragmente bleiben dabei unverändert in der Historie stehen — der Textbaustein ist eine zusätzliche Zusammenstellung, kein Ersatz.

## Zum Namen selbst

Der Name, den du beim Kopieren eingibst, ist nur ein flüchtiges Etikett für diesen Sammelplatz — kein Ort, an dem der Text am Ende bleibt. Ein einzelner Buchstabe oder ein kurzes Kürzel reicht völlig, sogar bewusst: Du tippst ihn ja bei jedem einzelnen Fragment erneut ein, kurz ist hier besser als sprechend. Die eigentliche, ordentlich benannte Datei entsteht erst später, wenn du das fertige Ergebnis dort einfügst, wo es hingehört.

## Die Grenze, die man kennen sollte

Mehrere Fragmente gemeinsam markieren und in einem Zug rüberziehen führt sie nicht automatisch zusammen — dafür müsste TC beim Mehrfach-Kopieren einen gemeinsamen Namen abfragen, tut es aber nicht. Es funktioniert zuverlässig nur einzeln, nacheinander, mit demselben getippten Namen jedes Mal — dafür ohne Rätselraten, in welcher Reihenfolge was gelandet ist.

## Einstellung

`[textbaustein] separator_blank_lines=1` in `SnipClip.ini` — wie viele Leerzeilen zwischen den angehängten Stücken stehen, 0 bis 3.
