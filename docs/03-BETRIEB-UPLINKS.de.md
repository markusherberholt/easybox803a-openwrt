# 3. Betrieb: Ethernet, WLAN, USB und DSL

Nach der Erstinstallation ist die EasyBox ein normales OpenWrt-System.

## Ethernet und Administration

Ethernet-LAN/Switch ist im Referenzaufbau praktisch getestet. Zuerst per Kabel
anmelden, ein Root-Passwort setzen und prüfen:

~~~sh
ubus call system board
ip -br link
swconfig list 2>/dev/null || true
~~~

LuCI ist anschließend an der konfigurierten LAN-Adresse erreichbar.

## WLAN

In LuCI: **Network → Wireless → Edit**. Land korrekt setzen, eigene SSID und
ein starkes WPA2-PSK bzw. WPA2/WPA3-Kennwort wählen, speichern und mit einem
Client testen. Das Repository enthält keine WLAN-Schlüssel.

## Android-USB-Tethering

Die Build-Konfiguration enthält kmod-usb-net-rndis und kmod-usb-net-cdc-ether.
Android-USB-Tethering wurde praktisch getestet.

1. Telefon per USB verbinden und USB-Tethering aktivieren.
2. In LuCI unter **Network → Interfaces** ein DHCP-Client-Interface auf usb0
   erstellen, beispielsweise mit Namen tether.
3. Es der Firewall-Zone wan zuordnen.
4. Prüfen:

~~~sh
ifstatus tether
ping -c 3 -I usb0 1.1.1.1
~~~

Nach einem Neustart des Telefons kann Tethering erneut aktiviert werden müssen.

## DSL

Die Konfiguration enthält kmod-atm, kmod-ltq-adsl-danube, ppp-mod-pppoa und
ppp-mod-pppoe. Eine echte DSL-Leitung wurde nicht getestet.

Für DSL werden ausschließlich die Vertragswerte des eigenen Providers genutzt:
Anschlusstyp, Zugangsdaten sowie bei ATM VPI/VCI und Kapselung. In LuCI wird
die passende DSL-/PPP-Schnittstelle unter **Network → Interfaces** eingerichtet.
Danach Synchronisation und Einwahl über logread und dmesg prüfen.
