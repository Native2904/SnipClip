# FontBrightness / BackgroundBrightness — wie wir zu dieser Lösung kamen

Jedes Theme (egal ob Alien Blood, Gruvbox, Everforest oder eigene Custom-Farben) sollte sich fein nachjustieren lassen, ohne gleich ein komplett neues Farbschema definieren zu müssen. Die naheliegende erste Idee: ein freier Prozentwert, z. B. `FontBrightness=-37`. Genau das haben wir verworfen.

## Warum kein freier Prozentwert

Ein beliebiger Zahlenwert lässt sich nie gegen jedes Theme im Voraus prüfen. Bei `-37` könnte Gruvbox noch gut aussehen, während dieselbe Zahl bei Everforest den Text fast unsichtbar macht — niemand hat das getestet, es funktioniert "wahrscheinlich", bis es das eben nicht tut. Ein Textfeld für einen freien Wert verlagert das Risiko einfach auf den Nutzer, ohne dass wir je sagen könnten: "Das haben wir geprüft."

Die Lösung: **feste, kuratierte Stufen** statt eines freien Werts. `-3` bis `+3`, sieben Stufen insgesamt, `0` ist neutral. Eine überschaubare, endliche Menge — jede einzelne Stufe lässt sich tatsächlich gegen jedes vorhandene Theme durchprüfen, statt auf Verdacht zu hoffen.

## Warum sieben Stufen und nicht mehr oder weniger

Zu wenige Stufen (z. B. nur an/aus oder drei Stufen) geben zu wenig Kontrolle für echtes Feintuning. Zu viele Stufen (z. B. zwanzig) würden das eigentliche Prinzip wieder aufweichen — bei zwanzig Stufen müsste man realistisch nicht mehr jede einzelne gegen jedes Theme durchprüfen, dann ist man wieder beim ungeprüften Vertrauen von vorhin. Sieben Stufen sind fein genug für echte Anpassung, aber noch klein genug, um sie alle tatsächlich durchzugehen.

## Warum Font und Hintergrund unterschiedlich stark reagieren

`FontBrightness` verschiebt alle textartigen Farben gemeinsam, mit ±7 Prozentpunkten Helligkeit pro Stufe (maximal ±21 bei Stufe 3). `BackgroundBrightness` bewegt sich enger: nur ±5 Prozentpunkte pro Stufe (maximal ±15).

Der Hintergrund ist die größte zusammenhängende Fläche am Bildschirm und reagiert am empfindlichsten auf Kontrastprobleme — kippt der Hintergrund zu weit, wird sofort alles darüber schwer lesbar. Text nimmt dagegen deutlich weniger Fläche ein und verträgt eine etwas größere Verschiebung, bevor es wirklich unangenehm wird. Deshalb zwei unterschiedlich enge Spielräume statt eines gemeinsamen Werts für beide.

## Warum HSL-Helligkeit statt einfacher RGB-Verschiebung

Eine simple Verschiebung aller RGB-Kanäle um denselben Betrag hätte gesättigte Theme-Farben schnell verwaschen oder trüb wirken lassen. Stattdessen wird jede Farbe zuerst in HSL umgerechnet, nur der Lightness-Wert verschoben, dann zurück nach RGB — Farbton und Sättigung bleiben dabei erhalten, nur die Helligkeit ändert sich, so wie man's erwarten würde.

## Die Absicherung, die trotzdem bleibt

Auch mit kuratierten Einzelstufen: Nichts hindert jemanden daran, `FontBrightness=3` UND `BackgroundBrightness=3` gleichzeitig zu setzen — eine Kombination, die einzeln geprüft wurde, aber zusammen trotzdem zu kontrastarm werden kann. Deshalb prüft SnipClip beim Laden zusätzlich den tatsächlichen Kontrast zwischen Vordergrund und Hintergrund. Fällt der zu niedrig aus, greift automatisch ein Sicherheitswert (unverändertes Theme), und im Alt+Enter-Fenster erscheint eine Warnzeile — kuratierte Stufen allein reichen also nicht ganz, die Laufzeit-Prüfung bleibt als zweite Absicherung bestehen.

## Einstellung

```ini
[Theme]
FontBrightness=0
BackgroundBrightness=0
```

Beide -3 bis +3, Standard 0 (unverändert).
