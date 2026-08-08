# AutoReUpload — wie wir zu dieser Lösung kamen

Wenn du in SnipClips eigenem Lister (Strg+E) etwas bearbeitest und mit Strg+S speicherst, landet der korrigierte Text direkt in der Windows-Zwischenablage — nicht in der Datei, die TC dafür heruntergeladen hat. Trotzdem zeigte TC anfangs bei jedem Öffnen einen Hochladen-Fehler ("Fehler beim Hochladen der Datei!").

Der Grund: Seit Version 8.50 bringt Total Commander eine Resync-Funktion mit — ursprünglich für FTP und ähnliche Plugins gedacht. Ändert sich die letzte Schreibzeit einer heruntergeladenen Datei, versucht TC automatisch, sie zurück ins Plugin hochzuladen. Ohne eine passende Antwort auf diesen Versuch zeigt TC eine Fehlermeldung — auch wenn eigentlich nichts schiefgelaufen ist.

Wir zweckentfremden diesen Mechanismus für unseren lokalen Anwendungsfall: SnipClip beantwortet den Hochladen-Versuch einfach mit einem stillen Erfolg, ohne den Inhalt tatsächlich zu übernehmen (der ist ja über die Zwischenablage längst da, wo er hin soll). `AutoReUpload=2` in `wincmd.ini` unter `[Configuration]` sorgt dafür, dass TC das automatisch versucht, statt jedes Mal nachzufragen.

## Was das für dich bedeutet

Ohne `AutoReUpload=2` gesetzt zu haben, kann es sein, dass TC beim Schließen des Lister-Fensters nach einer Bearbeitung fragt, ob hochgeladen werden soll. Ein Klick auf "Ja" schadet nicht — SnipClip nimmt den Versuch entgegen und tut nichts Schädliches damit — ist aber ein unnötiger Klick, den `AutoReUpload=2` erspart.

## Einstellung

In `wincmd.ini` (Total Commander → Konfiguration → Einstellungen direkt ändern):

```ini
[Configuration]
AutoReUpload=2
```
