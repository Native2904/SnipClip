# Fensterklassen-Namenskonvention — wie wir zu dieser Lösung kamen

Total Commander kann mehrere unserer Plugins gleichzeitig geladen haben — SnipClip, RecentTab, künftige weitere, alle im selben TC-Prozess. Registriert ein Plugin ein Hintergrundfenster (wie SnipClips ClipWatch, das auf Zwischenablage-Änderungen lauscht) unter einem generischen Namen wie "BackgroundWindow", könnte ein anderes, gleichzeitig laufendes Plugin versehentlich denselben Namen verwenden — Windows kennt Fensterklassen nur systemweit, nicht pro Plugin getrennt.

Deshalb bekommt jedes Fenster und jede benannte Windows-Nachricht, die ein Plugin selbst registriert, ein Präfix mit dem Plugin-Namen: `TC_SnipClip_BackgroundWnd_v1` statt einfach nur `BackgroundWnd`. Das verhindert Kollisionen zwischen gleichzeitig laufenden Plugins, ganz ohne dass sich die Plugins zur Laufzeit miteinander absprechen müssten — der Name allein reicht.

Die angehängte Versionsnummer (`_v1`) ist bewusst mit drin: Ändert sich später einmal die Bedeutung dieser Fensterklasse oder Nachricht (z. B. ein neues Nachrichtenformat), lässt sich das über eine neue Versionsnummer sauber trennen, ohne alte, noch laufende Plugin-Instanzen zu verwirren.

## Wo das bei SnipClip zur Anwendung kommt

- ClipWatch-Hintergrundfenster: `TC_SnipClip_BackgroundWnd_v1`
- Die registrierte Nachricht zwischen WFX und WLX (Lister-Bearbeitung → Historie): `SnipClip_SuppressNextCapture`

Beide Namen stehen zentral in `snip_shared.h`, damit WFX und WLX garantiert denselben Wert verwenden, ohne ihn an zwei Stellen im Code pflegen zu müssen.
