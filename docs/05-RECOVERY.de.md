# 5. Recovery und Grenzen

Vor jedem dauerhaften Schritt: Release-Prüfsummen prüfen, UART angeschlossen
lassen, stabile Stromversorgung verwenden und keine Übertragung unterbrechen.

Solange der Original-DANUBE-Bootloader nur M zum Laden und Y zum Starten des
temporären U-Boot benutzt, bleibt der Flash unverändert. Bei Fehlern im
RAM-Test: Strom trennen, Verdrahtung prüfen und Abschnitt 2 von vorn beginnen.
E und U sind im Original-Bootloader keine Diagnosebefehle und dürfen nicht
verwendet werden.

Wenn der dauerhafte NOR-U-Boot nicht startet, nicht blind erneut flashen und
keine fremden Flash-Befehle übernehmen. Für das Referenzgerät existiert ein
UART-Recovery-Artefakt, dessen vollständiger Wiederherstellungsweg aber nicht
an einem zweiten, absichtlich defekten Gerät wiederholt wurde. Es wird deshalb
nicht als anfängergeeignete Notfallprozedur veröffentlicht.

Dieses Projekt beschreibt einen erfolgreichen Referenzweg, aber keine Garantie
für jedes Exemplar, jede Hardware-Revision, Stromversorgung oder DSL-Leitung.
Bitte Fehler anonymisiert als GitHub Issue melden.
