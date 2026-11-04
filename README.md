# SnipClip

Ein Total-Commander-Plugin, das deine Zwischenablage in ein durchsuchbares, dauerhaftes Verzeichnis verwandelt. Kein separates Tool, kein Tray-Icon, kein eigenes Fenster, das ständig rumsteht – SnipClip lebt komplett innerhalb von TC, als virtuelles Panel unter `\SnipClip`.

<img width="1920" height="870" alt="2026-11-04_154122" src="https://github.com/user-attachments/assets/1e95f1d2-10dc-456d-a67d-240995033a27" />


*[English version](README.en.md) · [Русская версия](README.ru.md) · [Українська версія](README.uk.md) · [Dansk version](README.da.md)*

## Kapitel
- [Was es kann](#was-es-kann)
- [Im Detail](#im-detail)
- [Architektur, kurz erklärt](#architektur-kurz-erklärt)
- [Installation](#installation)
- [Build](#build)
- [License](#license)

## Was es kann

Du kopierst wie immer – Strg+C, egal aus welchem Programm. SnipClip fängt das im Hintergrund ab (ClipWatch, ein unsichtbares Fenster, das sich bei Windows für Zwischenablage-Änderungen anmeldet, kein Polling) und legt den Text als virtuelle Datei im `\SnipClip`-Panel ab.

**Passwörter bleiben außen vor.** Kopiert eine App bewusst etwas, das nicht mitgeschnitten werden soll – Bitwarden, KeePassXC, 1Password, Chrome im Inkognito-Modus –, markiert sie das mit einem offiziellen Windows-Signal. SnipClip hält sich daran und lässt diese Kopien komplett unangetastet. Ehrlich dazu: Das ist eine freiwillige Bitte, keine erzwungene Sperre – manche Quellen setzen das Signal schlicht nicht. Wer für eigene Tests trotzdem alles mitschneiden will, kann das per Schalter abstellen.

**Die Historie ist grundsätzlich unveränderlich** – aber nicht mehr ganz ohne Ausweg. Ursprünglich galt: nur `! Verlauf leeren` (alles auf einmal, inzwischen mit eigener Rückfrage) durfte je etwas entfernen. Mittlerweile lässt sich, wenn doch mal was Falsches drinsteht, auch ein einzelner Eintrag löschen – gesichert durch eine eigene, zusätzliche Bestätigung obendrauf auf TCs normale Lösch-Nachfrage. Wer die alte, strikte Regel lieber komplett beibehalten will, schaltet das per Einstellung wieder ab.

**Bearbeiten geht, nur nicht am Original.** F3 öffnet einen Snip in SnipClips eigenem Lister-Plugin. Strg+E schaltet auf Bearbeitungsmodus (der Hintergrund wird hell), du korrigierst, Strg+S schreibt direkt zurück in die Zwischenablage. Die Historie-Zeile selbst bleibt unverändert, du siehst nur zusätzlich vermerkt, dass und wie stark zuletzt editiert wurde.

**Was dir wichtig ist, pinnst du.** F5 oder Drag & Drop in eine Kategorie unter `! StickySnips` – Kategorien legst du mit F7 an. Gepinnte Snips überleben, auch wenn der Rest der Historie längst rausrotiert ist. Innerhalb der StickySnips verschiebt F6 einen gepinnten Snip (oder einen gepinnten Screenshot) in eine andere Kategorie, F5 legt eine unabhängige Kopie an. Ein gepinnter Snip lässt sich außerdem als Fragment nach `! Textbaustein\Entwurf` ziehen – er bleibt dabei gepinnt.

**Mehrere Fragmente zu einem Ganzen zusammenführen**, bevor du sie einsetzt – Code-Bausteine oder Textstücke, die du nacheinander in denselben getippten Namen unter `! Textbaustein\Entwurf` kopierst, werden dort angehängt statt ersetzt. Fertig gesammelt? Per F6 aus `Entwurf\` heraus umbenennen sichert das Ergebnis endgültig. Details dazu, warum das Anhängen nur einzeln nacheinander funktioniert: siehe [Textbaustein-Handbrief](notes/textbaustein/textbaustein.de.html).

**Kopierst du mal eine Datei statt Text**, ignoriert SnipClip das standardmäßig. Schaltest du die Pfad-Auflösung aktiv ein, wird der Dateipfad stattdessen als Text erfasst und landet gesammelt unter `! Pfade`, komplett getrennt von der Haupt-Historie.

**Screenshots landen unter `! Screenshots`**, gesichert als `.bmp`, sobald `[image_capture] FishImage=1` gesetzt ist: Erfasst wird jedes Bild außer von Programmen in `Exclude=` (z. B. dein Bildbetrachter). Bewusst Ausschlussliste statt Whitelist – Werkzeuge wie FastStone Capture schließen ihr Aufnahmefenster direkt nach dem Schreiben, die Quelle ist dann oft nicht mehr ermittelbar; solche Bilder werden trotzdem erfasst und heißen `Screenshot_<Nummer>`. Ein Screenshot, den SnipClip selbst gerade ausgegeben hat (F3/Schnellansicht) und der sofort zurückkommt, wird als Echo erkannt und nicht erneut gespeichert. Dasselbe Bild zweimal hintereinander wird nur einmal gespeichert (`[image_capture]` `dedup_consecutive=1`, Standard). Zwei unabhängige Speicherlimits (`max_screenshots`, `max_total_size_mb`) verhindern, dass die Sammlung unkontrolliert wächst – wer zuerst zuschlägt, entfernt den ältesten nicht gepinnten Screenshot. Gepinnte oder verknüpfte Screenshots werden nie rotiert.

**Screenshots lassen sich auch pinnen** – entweder eigenständig (wie Text: F5/Drag & Drop aus `! Screenshots` in eine normale StickySnips-Kategorie) oder verknüpft mit einer bestehenden Notiz, indem beide im selben Unterordner landen. Verknüpfte Einträge zeigen in `! Screenshots` eine eigene Spalte ("Verknuepft") mit dem Namen der zugehörigen Notiz. Alt+Enter auf einen Screenshot zeigt Quelle, Aufnahmezeit, Kategorie und Dateigröße. Löschst du eine Seite einer Verknüpfung, wird nur die Verknüpfung aufgelöst – das jeweils andere Element bleibt vollständig erhalten. Details und warum das so gelöst wurde: siehe [Verknüpfungs-Handbrief](notes/screenshot-linking/screenshot-linking.de.html).

**Jeder Eintrag zeigt automatisch das Icon seiner Quell-Anwendung** vor dem Namen (extrahiert aus der jeweiligen `.exe`) – verknüpfte Einträge bekommen stattdessen ein eigenes Kettenglied-Symbol. Ist die Quelle unbekannt oder die `.exe` inzwischen weg, greift ein Ersatz-Icon (eingebaut, oder eigene `.ico` über `FallbackSourceIcon=`).

**Optionales Capture-Popup** (`[notifications] Enabled=1`): Ein kleines Fenster in einer Bildschirmecke zeigt bei jeder Erfassung eine Vorschau (Text oder Miniatur) samt Quellprogramm – nimmt nie den Fokus, ein Klick schließt es.

**Optionaler Cloud-Upload** (`[cloud_upload] Enabled=1`): Alt+Enter bekommt einen Button „Hochladen“ – Screenshots gehen zu ImgBB, Text zu Pastebin, die URL landet in der Zwischenablage. Braucht je Dienst einen eigenen kostenlosen API-Schlüssel; ohne ihn bleibt der Button unsichtbar und nichts verlässt deinen PC.

**Neue Clips tauchen live auf**, ohne manuelles Strg+R – zeigt das aktive Panel SnipClip, aktualisiert es sich automatisch nach jedem neuen Capture. Das andere Panel bleibt unberührt (kein sinnloses Neueinlesen eines FTP- oder Netzwerkordners bei jeder Kopie). Erfasst wird immer, unabhängig davon, was TC gerade zeigt – steht SnipClip im inaktiven Panel, genügt dort ein Strg+R.

Alt+Enter auf einen einzelnen Eintrag zeigt Herkunft, Kategorie (bei gepinnten Snips), Zeitpunkt (absolut und relativ), Bearbeitungsstatus und den vollen Inhalt. Alt+Enter auf eine der Sonderzeilen zeigt stattdessen eine Gesamtübersicht – Laufzeit, Session-Statistik, Quellen-Ranking, StickySnips-Zahlen, Bearbeitungs-Statistik, echte Speicherbelegung.

## Im Detail

### Sprache

Alle Panel-Zeilen, Ordner, Dialoge und die Statusleiste des Listers folgen der Sprache von Total Commander: Das Plugin liest den Namen von TCs aktueller Sprachdatei (`WCMD_DEU.LNG` → Abschnitt `[deu]`, keine Sprachdatei → `[eng]`) und holt seine Texte aus `SnipClip.lng`. Enthalten: Englisch, Deutsch, Russisch, Ukrainisch, Dänisch. Fehlende Schlüssel fallen auf Englisch zurück. Eine Sprache unabhängig von TC erzwingen:

```ini
[Settings]
Language=deu      ; auto (Standard) = TC folgen
```

Zeilennamen werden in jeder Sprache der `SnipClip.lng` erkannt – ein Pfad in der Verzeichnis-Hotlist, gespeichert unter deutschem TC, funktioniert auch nach dem Umstellen auf Englisch weiter.

### Clipnamen

Der Panel-Name ist eine Inhaltsvorschau: Leerraum (Einrückung, Zeilenumbrüche, Tabs) wird zu einzelnen Leerzeichen zusammengefasst, führender Leerraum übersprungen – die sichtbaren Zeichen zeigen also echten Inhalt. Zeichen, die in Dateinamen nicht erlaubt sind, werden zu `-`. Das Suffix `_<Nummer>` hält die Namen eindeutig. Der Zeitpunkt steht bewusst nicht im Namen – er ist schon die Datumsspalte der Zeile und die Spalte **Age**.

### Die Panel-Struktur: `! Menü`

Alle Befehlszeilen (`! ClipWatch pausieren`, `! Verlauf leeren`, `! StickySnips`, `! Pfade`, `! Textbaustein`, `! Kommentare`, `! Screenshots`) stecken standardmäßig gesammelt unter einer einzigen Zeile: `! Menü`. Das hält die Wurzel aufgeräumt – nur deine echten Clips stehen direkt sichtbar da.

```ini
[menu]
RootButtons=
```

Willst du bestimmte Befehle lieber direkt an der Wurzel haben (z. B. weil du oft in StickySnips reinklickst), trägst du ihre Schlüssel kommagetrennt ein: `RootButtons=Sticky,Path`. Verfügbare Schlüssel: `Pause`, `Clear`, `Sticky`, `Path`, `Textbaustein`, `Comments`, `Screenshots`. Liegen alle Befehle an der Wurzel, wird `! Menü` ausgeblendet.

Jede Sonderzeile hat ihr eigenes kleines Icon (statt TCs Standard-Datei-/Ordner-Symbol) – erleichtert das Erkennen auf einen Blick, besonders wenn mehrere davon nebeneinander in `! Menü` stehen.

### `[history]` – Verlauf, Grundeinstellungen

- **`max_entries`** (Standard 100) – maximale Einträge gleichzeitig, ältester fliegt automatisch raus
- **`name_preview_length`** (Standard 20) – Zeichen für den Panel-Namen
- **`persist_history`** (Standard 1) – überlebt einen TC-Neustart oder nicht
- **`dedup_consecutive`** (Standard 1) – zwei identische Kopien hintereinander erzeugen nur einen Eintrag
- **`max_chars`** (Standard 1000000, 0 = unbegrenzt) – größere Kopien werden komplett übersprungen, nicht abgeschnitten
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

Standardmäßig aus (`Enabled=1` schaltet ein, `Format=` wählt eines der Formate unten). Aktiviert, wandelt SnipClip Datei-Kopien in Text um:

| Format | Beispiel |
|---|---|
| `local` | `C:\Users\Name\Datei.txt` |
| `forwardslash` | `C:/Users/Name/Datei.txt` |
| `unc` | `\\server\freigabe\Datei.txt` |
| `fileuri` | `file:///C:/Users/Name/Datei.txt` |
| `wsl` | `/mnt/c/Users/Name/Datei.txt` |

`AllowExtensions`/`AllowFolders` grenzen das per Semikolon-Liste ein. Ordner gelten als ganze Ordnernamen (`C:\Projekt` passt nicht zugleich auf `C:\Projekte`).

### `[Settings]` – Sonstiges

- **`RespectClipboardExclude`** (Standard 1) – Passwort-Manager-Signal respektieren
- **`StartPaused`** (Standard 0) – ClipWatch startet aktiv oder bewusst inaktiv
- **`LiveRefreshAfterCapture`** (Standard 1) – Panel aktualisiert sich automatisch nach neuem Capture

### Aufzeichnung ab TC-Start (Autorun)
TC lädt SnipClip erst, wenn `\SnipClip` zum ersten Mal geöffnet wird – vorher wird nichts aufgezeichnet. Wer das Autorun-Plugin nutzt, kann SnipClip schon beim TC-Start anwerfen. Diesen Block in die `autorun.cfg` **vor** `Pragma AutorunFinalizeSection` einfügen (liegt auch als `autorun.snipclip.example.cfg` bei):

```
If FileExist(COMMANDER_PATH & "\plugins\WFX\SnipClip\SnipClip.wfx") Then
  LoadLibrary "%COMMANDER_PATH%\plugins\WFX\SnipClip\SnipClip.wfx" SnipClipAutostart
EndIf
```

- Der Pfad muss **genau** dem Eintrag in `wincmd.ini` unter `[FileSystemPlugins]` entsprechen. Immer `.wfx` angeben – unter 64-Bit-TC nimmt Autorun selbst die `.wfx64`.
- Es läuft kein zusätzliches Programm: Autorun lädt dieselbe DLL, die TC später als Dateisystem-Plugin benutzt, und ruft nur `SnipClipAutostart` auf. Beim ersten Öffnen von `\SnipClip` ist alles schon da.
- Abschalten: Block auskommentieren. `StartPaused=1` gilt auch hier.

### Popup-Hotkey – Clips in jedem Programm
Mit `[hotkey] Popup=` (z. B. `Win+Alt+V`) öffnet eine systemweite Tastenkombination an der Maus ein kleines Menü mit den letzten Clips und den StickySnips – in jedem Programm, nicht nur in TC. Auswahl mit Maus oder Ziffer 1–0; SnipClip wechselt zurück ins vorherige Programm und fügt mit Strg+V ein (`AutoPaste=0`: nur Zwischenablage). Ist die Kombination schon von einem anderen Programm belegt, meldet SnipClip das einmal; der Status steht auch in der Alt+Enter-Übersicht. **Keine Strg+Alt+Buchstabe-Kombination wählen** – Strg+Alt ist AltGr, Strg+Alt+Q würde das @ blockieren. Funktioniert, sobald SnipClip läuft – mit dem Autorun-Autostart also ab TC-Start.

### Custom-Spalten
Fünf zusätzliche Spalten über TCs Spalten-Konfiguration: **Edited**, **Diff**, **Source**, **Age**, **Verknuepft** (nur bei Screenshots gefüllt, zeigt den Namen der verknüpften Notiz).

## Architektur, kurz erklärt

Zwei separate DLLs im selben TC-Prozess: `SnipClip.wfx`/`.wfx64` (Panel, Historie, StickySnips, Textbaustein, ClipWatch) und `SnipClipLister.wlx`/`.wlx64` (nur der Strg+E-Editor). Warum beide Fenster/Nachrichten mit `TC_SnipClip_`-Präfix arbeiten: siehe [Fensterklassen-Handbrief](notes/window-class-naming/window-class-naming.de.html).

## Installation

**WFX:** Konfiguration → Optionen → Plugins → Dateisystem-Plugins → Konfigurieren → Hinzufügen, `SnipClip.wfx64` wählen.

**WLX:** Konfiguration → Optionen → Plugins → Lister-Plugins → Konfigurieren → Hinzufügen, `SnipClipLister.wlx64` wählen – er meldet sich selbst für `.snip`-Dateien an (Erkennungsstring `EXT="SNIP"`).

**Einstellungen:** Bei einer Neuinstallation `SnipClip.example.ini` nach `SnipClip.ini` kopieren und anpassen. Bei einem Update bleibt deine `SnipClip.ini` unangetastet – neue Einstellungen aus `SnipClip.example.ini` übernehmen, fehlende Schlüssel gelten mit ihrem Standardwert.

**Empfohlen:** `AutoReUpload=2` in `wincmd.ini` setzen – siehe [AutoReUpload-Handbrief](notes/autoreupload/autoreupload.de.html).

## Build

MinGW-w64 (64-Bit unter `C:\mingw64`, optional 32-Bit unter `C:\mingw64\mingw32`). `build_release.bat` baut optimierte Binaries, `build_debug.bat` Debug-Versionen; beide stellen einen versandfertigen `release\`-Ordner zusammen, inklusive `SnipClip.lng`, `icons\`, `fonts\` und `notes\`.

## License

MIT.
