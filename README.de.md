# SnipClip

Ein Total-Commander-Plugin, das deine Zwischenablage in ein durchsuchbares, dauerhaftes Verzeichnis verwandelt. Kein separates Tool, kein Tray-Icon, kein eigenes Fenster, das ständig rumsteht – SnipClip lebt komplett innerhalb von TC, als virtuelles Panel unter `\SnipClip`.

<img width="1919" height="870" alt="2026-08-23_141233" src="https://github.com/user-attachments/assets/93827a4b-ad71-45ea-9146-432d2e1525c8" />


*[English version](README.md) · [Русская версия](README.ru.md)*

## Chapters
- [What it can](#what-it-can)
- [Specific](#specific)
- [Architektur, kurz erklärt](#architektur-kurz-erklärt)
- [Installation](#installation)
- [Build](#build)
- [License](#license)

## What it can

Du kopierst wie immer – Strg+C, egal aus welchem Programm. SnipClip fängt das im Hintergrund ab (ClipWatch, ein unsichtbares Fenster, das sich bei Windows für Zwischenablage-Änderungen anmeldet, kein Polling) und legt den Text als virtuelle Datei im `\SnipClip`-Panel ab.

**Passwörter bleiben außen vor.** Kopiert eine App bewusst etwas, das nicht mitgeschnitten werden soll – Bitwarden, KeePassXC, 1Password, Chrome im Inkognito-Modus –, markiert sie das mit einem offiziellen Windows-Signal. SnipClip hält sich daran und lässt diese Kopien komplett unangetastet. Ehrlich dazu: Das ist eine freiwillige Bitte, keine erzwungene Sperre – manche Quellen setzen das Signal schlicht nicht. Wer für eigene Tests trotzdem alles mitschneiden will, kann das per Schalter abstellen.

**Die Historie ist grundsätzlich unveränderlich** – aber nicht mehr ganz ohne Ausweg. Ursprünglich galt: nur `! Verlauf leeren` (alles auf einmal) durfte je etwas entfernen. Mittlerweile lässt sich, wenn doch mal was Falsches drinsteht, auch ein einzelner Eintrag löschen – gesichert durch eine eigene, zusätzliche Bestätigung obendrauf auf TCs normale Lösch-Nachfrage. Wer die alte, strikte Regel lieber komplett beibehalten will, schaltet das per Einstellung wieder ab.

**Bearbeiten geht, nur nicht am Original.** F3 öffnet einen Snip in SnipClips eigenem Lister-Plugin. Strg+E schaltet auf Bearbeitungsmodus (der Hintergrund wird hell), du korrigierst, Strg+S schreibt direkt zurück in die Zwischenablage. Die Historie-Zeile selbst bleibt unverändert, du siehst nur zusätzlich vermerkt, dass und wie stark zuletzt editiert wurde.

**Was dir wichtig ist, pinnst du.** F5 oder Drag & Drop in eine Kategorie unter `! StickySnips` – Kategorien legst du mit F7 an. Gepinnte Snips überleben, auch wenn der Rest der Historie längst rausrotiert ist.

**Mehrere Fragmente zu einem Ganzen zusammenführen**, bevor du sie einsetzt – Code-Bausteine oder Textstücke, die du nacheinander in denselben getippten Namen unter `! Textbaustein\Entwurf` kopierst, werden dort angehängt statt ersetzt. Fertig gesammelt? Per F6 aus `Entwurf\` heraus umbenennen sichert das Ergebnis endgültig. Details dazu, warum das Anhängen nur einzeln nacheinander funktioniert: siehe [Textbaustein-Handbrief](notes/textbaustein/textbaustein.de.html).

**Kopierst du mal eine Datei statt Text**, ignoriert SnipClip das standardmäßig. Schaltest du die Pfad-Auflösung aktiv ein, wird der Dateipfad stattdessen als Text erfasst und landet gesammelt unter `! Pfade`, komplett getrennt von der Haupt-Historie.

**Screenshots landen unter `! Screenshots`** – aber nur von Programmen, die du explizit erlaubst (`[image_capture_sources]` in der INI, whitelist-basiert, gleiches Prinzip wie bei der Pfad-Auflösung). Legt z. B. FastStone Capture nach dem Schließen des Aufnahme-Fensters ein Bild in die Zwischenablage, wird das automatisch als `.bmp` gesichert. Zwei unabhängige Speicherlimits (`max_screenshots`, `max_total_size_mb`) verhindern, dass die Sammlung unkontrolliert wächst – wer zuerst zuschlägt, entfernt den ältesten Screenshot.

**Screenshots lassen sich auch pinnen** – entweder eigenständig (wie Text: F5/Drag & Drop aus `! Screenshots` in eine normale StickySnips-Kategorie) oder verknüpft mit einer bestehenden Notiz, indem beide im selben Unterordner landen. Verknüpfte Einträge zeigen in `! Screenshots` eine eigene Spalte ("Verknuepft") mit dem Namen der zugehörigen Notiz. Löschst du eine Seite einer Verknüpfung, wird nur die Verknüpfung aufgelöst – das jeweils andere Element bleibt vollständig erhalten. Details und warum das so gelöst wurde: siehe [Verknüpfungs-Handbrief](notes/screenshot-linking/screenshot-linking.de.html).

**Jeder Eintrag zeigt automatisch das Icon seiner Quell-Anwendung** vor dem Namen (extrahiert aus der jeweiligen `.exe`) – verknüpfte Einträge bekommen stattdessen ein eigenes Kettenglied-Symbol. Ist die Quelle unbekannt oder die `.exe` inzwischen weg, greift ein Ersatz-Icon (eingebaut, oder eigene `.ico` über `FallbackSourceIcon=`).

**Neue Clips tauchen live auf**, ohne manuelles Strg+R – sobald du einmal mit dem Panel interagiert hast (z. B. Alt+Enter), aktualisiert sich die Ansicht automatisch nach jedem neuen Capture.

Alt+Enter auf einen einzelnen Eintrag zeigt Herkunft, Kategorie (bei gepinnten Snips), Zeitpunkt (absolut und relativ), Bearbeitungsstatus und den vollen Inhalt. Alt+Enter auf eine der Sonderzeilen zeigt stattdessen eine Gesamtübersicht – Laufzeit, Session-Statistik, Quellen-Ranking, StickySnips-Zahlen, Bearbeitungs-Statistik, echte Speicherbelegung.

## Specific

### Die Panel-Struktur: `! menu`

Alle Befehlszeilen (`! ClipWatch pausieren`, `! Verlauf leeren`, `! StickySnips`, `! Pfade`, `! Textbaustein`, `! Kommentare`, `! Screenshots`) stecken standardmäßig gesammelt unter einer einzigen Zeile: `! menu`. Das hält die Wurzel aufgeräumt – nur deine echten Clips stehen direkt sichtbar da.

```ini
[menu]
RootButtons=
```

Willst du bestimmte Befehle lieber direkt an der Wurzel haben (z. B. weil du oft in StickySnips reinklickst), trägst du ihre Schlüssel kommagetrennt ein: `RootButtons=Sticky,Path`. Verfügbare Schlüssel: `Pause`, `Clear`, `Sticky`, `Path`, `Textbaustein`, `Comments`.

Jede Sonderzeile hat ihr eigenes kleines Icon (statt TCs Standard-Datei-/Ordner-Symbol) – erleichtert das Erkennen auf einen Blick, besonders wenn mehrere davon nebeneinander in `! menu` stehen.

### `[history]` – Verlauf, Grundeinstellungen

- **`max_entries`** (Standard 100) – maximale Einträge gleichzeitig, ältester fliegt automatisch raus
- **`name_preview_length`** (Standard 20) – Zeichen für den Panel-Namen
- **`persist_history`** (Standard 1) – überlebt einen TC-Neustart oder nicht
- **`dedup_consecutive`** (Standard 1) – zwei identische Kopien hintereinander erzeugen nur einen Eintrag
- **`AllowSingleDelete`** (Standard 1) – ob einzelne Einträge überhaupt löschbar sind

### `[Theme]` – Optik

Eigenes "Basic"-Theme ("Alien Blood") als Wiedererkennungsmerkmal. Für Konsistenz mit anderen Plugins: `Name=gruvbox|everforest|solarized`, je mit `Mode=dark|light`. `NoColors=1` unter `[Settings]` schaltet jedes Theme komplett ab.

**`FontBrightness=`/`BackgroundBrightness=`** (je -3 bis +3, Standard 0) – Helligkeit über feste, kuratierte Stufen feinjustieren. Warum genau sieben Stufen und warum Font/Hintergrund unterschiedlich stark reagieren: siehe [Brightness-Handbrief](notes/basic-theme/basic-theme.de.html). Ergibt eine Kombination zu wenig Kontrast, greift automatisch ein Sicherheitswert, mit Warnzeile im Alt+Enter-Fenster.

**`TimeBasedMode=`** (Standard 0) – ist das gesetzt, entscheidet die aktuelle Uhrzeit über hell/dunkel statt `Mode=` (`LightStartHour=`/`DarkStartHour=`, Standard 6-18 Uhr hell). Live wirksam, kein Neustart nötig. Betrifft nur die Haupt-Dialoge (Properties/Übersicht) – der Lister und die Textbaustein-Kommentarmaske haben ihren eigenen, unabhängigen Strg+E-Hell/Dunkel-Wechsel, den das nicht überschreibt.

### `[lister]` – der Editor

Nutzt bewusst ein anderes Theme als der Rest des Plugins (dunkel zum Lesen, hell zum Bearbeiten): `gruvbox`, `everforest` oder `solarized`. Warum das Speichern hier ohne TCs Hochladen-Fehlermeldung funktioniert: siehe [AutoReUpload-Handbrief](notes/autoreupload/autoreupload.de.html).

**`MonoFont=`/`MonoFontName=`** – eigene Monospace-Schrift statt Consolas. Bereits vorkonfiguriert für JetBrains Mono (im `fonts\`-Ordner mitgeliefert, SIL Open Font License).

### `[sticky]` – die zweite, kuratierte Liste

**`default_category`** (Standard "Unsortiert") – greift, wenn du direkt auf `! StickySnips` selbst ziehst statt in eine Kategorie.

### `[textbaustein]` – Fragmente zusammenführen

Neu strukturiert: Anhängen funktioniert nur noch innerhalb von `! Textbaustein\Entwurf\` – die Wurzel von `! Textbaustein` selbst ist "gesichert", dort landet nichts mehr automatisch dazu. Fertig sammeln, dann per **F6** aus `Entwurf\` heraus umbenennen (z. B. `Entwurf\y` → `config-final`) – Ordnerwechsel und finaler Name entstehen dabei in einem Schritt, über TCs eigenen Umbenennen-Dialog.

**`separator_blank_lines`** (Standard 1) – wie viele Leerzeilen zwischen angehängten Stücken.

**`highlight_last_append`** (Standard 1) – öffnest du einen Textbaustein per F3, wird der zuletzt angehängte Teil automatisch markiert (reine Windows-Textauswahl, verschwindet beim ersten Klick).

**Kommentare**: StickySnips und Textbausteine können eine kurze Notiz zur Wiedererkennung bekommen (praktisch bei kurzen Namen wie "y" oder "z"). Alt+Enter zeigt den Kommentar an, der Button "bearbeiten" unten links öffnet eine eigene kleine Maske mit **Strg+E**/**Strg+S** – gleiches Bedienprinzip wie im Lister. `! Kommentare` zeigt alle vergebenen Kommentare auf einen Blick, gestapelt. Details und warum eine eigene Lösung statt DescriptEdit direkt: siehe [Kommentar-Handbrief](notes/comments/comments.de.html).

### `[path_resolve]` – Datei-Pfade statt Text

Standardmäßig aus. Aktiviert, wandelt SnipClip Datei-Kopien in Text um:

| Format | Beispiel |
|---|---|
| `local` | `C:\Users\Name\Datei.txt` |
| `forwardslash` | `C:/Users/Name/Datei.txt` |
| `unc` | `\\server\freigabe\Datei.txt` |
| `fileuri` | `file:///C:/Users/Name/Datei.txt` |
| `wsl` | `/mnt/c/Users/Name/Datei.txt` |

`AllowExtensions`/`AllowFolders` grenzen das per Semikolon-Liste ein.

### `[Settings]` – Sonstiges

- **`RespectClipboardExclude`** (Standard 1) – Passwort-Manager-Signal respektieren
- **`StartPaused`** (Standard 0) – ClipWatch startet aktiv oder bewusst inaktiv
- **`LiveRefreshAfterCapture`** (Standard 1) – Panel aktualisiert sich automatisch nach neuem Capture

### Custom-Spalten
Fünf zusätzliche Spalten über TCs Spalten-Konfiguration: **Edited**, **Diff**, **Source**, **Age**, **Verknuepft** (nur bei Screenshots belegt, zeigt den Namen einer verknüpften Notiz).

## Architektur, kurz erklärt

Zwei separate DLLs im selben TC-Prozess: `SnipClip.wfx`/`.wfx64` (Panel, Historie, StickySnips, Textbaustein, ClipWatch) und `SnipClipLister.wlx`/`.wlx64` (nur der Strg+E-Editor). Warum beide Fenster/Nachrichten mit `TC_SnipClip_`-Präfix arbeiten: siehe [Fensterklassen-Handbrief](notes/window-class-naming/window-class-naming.de.html).

## Installation

**WFX:** Konfiguration → Optionen → Plugins → Dateisystem-Plugins → Konfigurieren → Hinzufügen, `SnipClip.wfx64` wählen.

**WLX:** Konfiguration → Optionen → Plugins → Lister-Plugins → Konfigurieren → Hinzufügen, `SnipClipLister.wlx64` wählen, danach manuell der Endung `snip` zuordnen.

**Empfohlen:** `AutoReUpload=2` in `wincmd.ini` setzen – siehe [AutoReUpload-Handbrief](notes/autoreupload/autoreupload.de.html).

## Build

MinGW-w64 (64-Bit unter `C:\mingw64`, optional 32-Bit unter `C:\mingw64\mingw32`). `build_debug.bat` ausführen, Binaries landen in `release\`.

## License

MIT.
