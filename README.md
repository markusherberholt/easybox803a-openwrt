# Vodafone EasyBox 803A mit OpenWrt

Dokumentierter Konvertierungsweg für eine **Vodafone EasyBox 803A**
(Arcadyan ARV752DPW22): vom Originalzustand über die serielle Konsole,
einen RAM-Test bis zur dauerhaften OpenWrt-Installation.

Ziel ist danach ein frei konfigurierbarer OpenWrt-Router. WLAN, LAN,
Firewall und Zugangsdaten werden ausdrücklich nicht vorgegeben.

Die geprüften Binärdateien werden nicht in Git eingecheckt. Sie gehören als
klar gekennzeichnete Dateien samt `SHA256SUMS` in einen GitHub-Release. Für
eine Konvertierung ausschließlich genau die Dateien eines solchen Releases
verwenden – niemals ähnlich benannte Dateien aus fremden Foren oder Builds.

## Nachweis im Referenzaufbau

| Bestandteil | Stand |
|---|---|
| RAM-Boot und Sysupgrade | praktisch getestet |
| Ethernet-LAN/Switch | praktisch getestet |
| WLAN | praktisch getestet |
| Android-USB-Tethering | praktisch getestet |
| ADSL/ATM/PPPoA/PPPoE-Komponenten | in der Build-Konfiguration enthalten |
| Live-Test an DSL-Leitung | nicht durchgeführt |

OpenWrt 19.07.2 ist historisch und wird upstream nicht mehr sicherheitsgepflegt.
Dieses Projekt dokumentiert einen Hardwareweg für ein Altgerät; es ist keine
allgemeine Router-Empfehlung.

## Anleitung

1. [Vorbereitung und UART](docs/01-HARDWARE-UART.de.md)
2. [Erstkonvertierung](docs/02-ERSTKONVERTIERUNG.de.md)
3. [Betrieb: Ethernet, WLAN, USB, DSL](docs/03-BETRIEB-UPLINKS.de.md)
4. [Build](docs/04-BUILD.de.md)
5. [Recovery und Grenzen](docs/05-RECOVERY.de.md)

## Inhalt

| Pfad | Zweck |
|---|---|
| config/ | verwendete, nicht personenbezogene Build-Konfiguration |
| patches/openwrt/ | OpenWrt-Quelländerungen |
| patches/u-boot/ | U-Boot-Anpassung für dieses Board |
| docs/ | Konvertierung und Betrieb |
| assets/ | UART-Verdrahtungsschema und anonymisierte Referenzfotos |
| SHA256SUMS-source-files.txt | Prüfsummen der veröffentlichten Quellen |

## Transparenz

Die Entwicklung und Dokumentationsstruktur entstand mit Unterstützung von
ChatGPT. Hardwarearbeiten, serielle Übertragungen, Flash-Schritte,
Funktionsprüfungen und Prüfsummenabgleiche wurden am Referenzgerät praktisch
ausgeführt und kontrolliert.

Es wurde nur ein Referenzgerät konvertiert. Rückmeldungen zu anderen
Hardware-Revisionen sind willkommen – bitte niemals Zugangsdaten, WLAN-Schlüssel,
MAC-Adressen, Seriennummern oder Flash-Dumps in Issues veröffentlichen.

## Lizenz

Siehe [LICENSES.md](LICENSES.md).
