# Kommentare für StickySnips & Textbausteine — wie wir zu dieser Lösung kamen

StickySnips und Textbausteine tragen oft kurze, wenig aussagekräftige Namen — praktisch beim schnellen Sammeln, aber bei mehreren gleichzeitig offenen Einträgen reicht der Name allein oft nicht zur Wiedererkennung. Die Lösung war ein zusätzliches, vom Namen unabhängiges Feld: ein Kommentar.

Für die Umsetzung gab es drei Wege. Eine echte `descript.ion`-Datei im Plugin-Ordner hätte bedeutet, dass der Kommentar nur über den echten Ordner erreichbar wäre, nicht direkt aus dem virtuellen Panel — verworfen. Ein komplett neues Kommentarfeld mit eigener Eingabe-UI wäre Doppelarbeit zu unserem bereits bestehenden DescriptEdit-Plugin gewesen, das dieselbe Art von Datei-Kommentaren bearbeitet — auch verworfen. Am Ende haben wir DescriptEdits bewährte Eingabe-*Bauweise* übernommen, aber als eigenständigen Code direkt in SnipClip umgesetzt: keine Installation von DescriptEdit nötig, nur dieselbe Logik neu gebaut.

Ein eigenes Eingabefenster war ohnehin unumgänglich, denn TCs `RequestProc`-Schnittstelle kennt keinen "frag nach beliebigem Text"-Typ — nur Benutzername, Passwort, Zielordner, URL, Ja/Nein. Für den Bearbeitungsmodus haben wir uns am Lister orientiert: dunkel zum Lesen, hell zum Bearbeiten, Strg+E/Strg+S zum Umschalten und Speichern. Die Farben kommen dabei bewusst aus `[lister] Theme=`, nicht aus dem Haupt-Theme.

Ursprünglich war das Kommentarfeld nur für Textbausteine gedacht, wurde aber auf StickySnips erweitert — beide sind kuratierte, gesicherte Einträge mit demselben Wiedererkennungsproblem. Die Verlaufs-Historie selbst bekommt bewusst keine Kommentare: zu viele, zu flüchtige Einträge, als dass sich das lohnen würde.

Damit man den Überblick behält, zeigt `! Kommentare` alle vergebenen Kommentare gestapelt an, Name bzw. Kategorie jeweils voran.
