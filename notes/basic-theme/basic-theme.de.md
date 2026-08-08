# Eigenes Basic-Theme — wie wir zu dieser Lösung kamen

Jedes unserer TC-Plugins hat ein eigenes Theme-System (Hell/Dunkel, mehrere Farbpaletten). Die Frage war: Soll das "Basic"-Standardtheme über alle Plugins hinweg identisch aussehen, oder soll jedes Plugin sein eigenes bekommen?

Wir haben uns bewusst für Letzteres entschieden. Wenn mehrere unserer Plugins gleichzeitig offen sind — SnipClip, RecentTab, künftige weitere — soll man auf den ersten Blick erkennen, welches Fenster gerade zu welchem Plugin gehört, ohne erst den Fenstertitel lesen zu müssen. Ein gemeinsames Standard-Theme würde diesen Wiedererkennungseffekt zunichtemachen.

SnipClips Basic-Theme heißt **Alien Blood** — dunkles Grün, giftig-leuchtende Akzente, aus der öffentlich einsehbaren Gogh-Palette übernommen (nicht selbst erfunden). RecentTab hat sein eigenes Orange/Dunkel-Schema. Beide sind bewusst unterschiedlich.

Die drei gemeinsamen Themes (Gruvbox, Everforest, Solarized) bleiben davon unberührt — die sehen in jedem unserer Plugins gleich aus, weil sie feste, extern definierte Paletten sind, kein eigenes Signature-Design.

## Eine bewusste Lücke

Alien Blood hat aktuell keine offizielle Hell-Variante — die Gogh-Palette definiert nur die dunkle Fassung. Statt eine Hell-Variante zu erfinden, die nicht wirklich zur Palette gehört, fällt `Mode=light` bei Basic einfach auf dieselben dunklen Werte zurück. Wer unbedingt eine helle Basic-Optik will, nutzt `Name=custom` mit selbst gewählten Farben.

## Einstellung

```ini
[Theme]
Name=basic
Mode=dark
```
