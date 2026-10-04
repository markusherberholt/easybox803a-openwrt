# 2. Erstkonvertierung vom Originalzustand

Reihenfolge: **erst im RAM testen, erst danach U-Boot dauerhaft schreiben,
erst danach OpenWrt dauerhaft installieren.** Bis zum NOR-U-Boot-Schritt wird
kein Flash verändert.

## Release-Dateien prüfen

Im **Host-Fenster (Ubuntu)**:

~~~sh
cd /pfad/zum-heruntergeladenen-release
sha256sum -c SHA256SUMS
~~~

Bei einem Fehler nicht fortfahren. Benötigt werden:

| Datei | Zweck |
|---|---|
| u-boot-19.07.2-arv752dpw22-brn-stack40000-gpio19-ramboot-final.bin | temporärer RAM-U-Boot |
| u-boot-19.07.2-arv752dpw22-nor-gpio19-ramboot-final.bin | dauerhafter NOR-U-Boot |
| openwrt-19.07.2-arv752dpw22-initramfs-kernel-cca3-final.bin | OpenWrt nur im RAM |
| openwrt-19.07.2-arv752dpw22-squashfs-sysupgrade-cca3-final.bin | dauerhafte Firmware |

## A. Originalen Bootloader anhalten

Im **Seriell-Fenster (Picocom)** die EasyBox einschalten. Dreimal die
Leertaste drücken, bis dieser Prompt erscheint:

~~~text
[DANUBE Boot]:
~~~

Dann ! eingeben. Das Menü enthält M für Upload in den RAM. Im originalen
DANUBE-Bootloader **niemals E oder U verwenden**.

## B. Temporären U-Boot nur in den RAM laden

1. M eingeben.
2. Bei RAM upload destination: (default:0x80002000) nur Enter drücken.
3. Sobald CCCC... erscheint, Picocom mit Ctrl+A, dann Ctrl+X beenden.
4. Im **Host-Fenster**:

~~~sh
ram_uboot='/pfad/zum-release/u-boot-19.07.2-arv752dpw22-brn-stack40000-gpio19-ramboot-final.bin'
sudo stty -F /dev/ttyUSB0 115200 cs8 -cstopb -parenb -ixon -ixoff -crtscts raw
sudo sh -c "sx -vv '$ram_uboot' < /dev/ttyUSB0 > /dev/ttyUSB0"
~~~

5. Nach Transfer complete Picocom wieder öffnen.
6. Am DANUBE-Prompt Y eingeben und bei 0x80002000 nur Enter drücken.
7. Beim U-Boot-Autoboot eine Taste drücken. Erwartet wird:

~~~text
ARV752DPW22 #
~~~

Wenn dieser Prompt nicht erscheint: Strom trennen und bei Abschnitt A neu
beginnen. Der Original-Flash ist bis hierhin unverändert.

## C. OpenWrt im RAM testen

Am U-Boot-Prompt:

~~~text
loadx 0x81000000
~~~

Bei CCCC... Picocom beenden, dann im **Host-Fenster**:

~~~sh
initramfs='/pfad/zum-release/openwrt-19.07.2-arv752dpw22-initramfs-kernel-cca3-final.bin'
sudo stty -F /dev/ttyUSB0 115200 cs8 -cstopb -parenb -ixon -ixoff -crtscts raw
sudo sh -c "sx -vv '$initramfs' < /dev/ttyUSB0 > /dev/ttyUSB0"
~~~

Picocom erneut öffnen und starten:

~~~text
bootm 0x81000000
~~~

Jetzt läuft OpenWrt nur im RAM. Ethernet verbinden. Im **Host-Fenster** erhält
der tatsächlich angeschlossene Ethernet-Port eine Adresse im gleichen Netz
(Beispiel enp2s0 ersetzen):

~~~sh
sudo ip addr flush dev enp2s0
sudo ip addr add 192.168.1.2/24 dev enp2s0
sudo ip link set enp2s0 up
ping -c 3 192.168.1.1
~~~

Im **Seriell-Fenster** mindestens prüfen:

~~~sh
ubus call system board
ip -br link
logread | tail -n 50
~~~

Nur bei funktionierendem RAM-Test weiter.

## D. NOR-U-Boot dauerhaft schreiben

UART angeschlossen lassen, Strom nicht unterbrechen und ausschließlich die
geprüfte NOR-Datei verwenden.

1. Abschnitt A und B erneut ausführen.
2. Am temporären U-Boot-Prompt laden:

~~~text
loadx 0x81000000
~~~

Bei CCCC... Picocom beenden, dann im **Host-Fenster**:

~~~sh
nor_uboot='/pfad/zum-release/u-boot-19.07.2-arv752dpw22-nor-gpio19-ramboot-final.bin'
sudo stty -F /dev/ttyUSB0 115200 cs8 -cstopb -parenb -ixon -ixoff -crtscts raw
sudo sh -c "sx -vv '$nor_uboot' < /dev/ttyUSB0 > /dev/ttyUSB0"
~~~

3. Picocom wieder öffnen. Im **Seriell-Fenster**:

~~~text
printenv fileaddr filesize
run write-uboot-nor
cmp.b 0x81000000 0xB0000000 $filesize
protect on 0xB0000000 +$filesize
reset
~~~

printenv muss fileaddr und filesize ausgeben. Der eingebaute Schreibablauf hebt
den Schutz auf, löscht die Größe der geladenen Datei und kopiert sie an
0xB0000000. cmp.b darf **keine Unterschiede** melden. protect on stellt den
Schutz wieder her, weil der eingebaute Kurzablauf das nicht selbst erledigt.

Nach reset muss direkt der neue U-Boot-Prompt erscheinen.

## E. OpenWrt dauerhaft installieren

Mit dem neuen U-Boot Abschnitt C wiederholen: Initramfs laden, bootm ausführen
und Ethernet testen. Danach im **Host-Fenster**:

~~~sh
sysupgrade_image='/pfad/zum-release/openwrt-19.07.2-arv752dpw22-squashfs-sysupgrade-cca3-final.bin'
scp "$sysupgrade_image" root@192.168.1.1:/tmp/
~~~

Im **SSH-Fenster zum temporären OpenWrt**:

~~~sh
cd /tmp
sha256sum openwrt-19.07.2-arv752dpw22-squashfs-sysupgrade-cca3-final.bin
sysupgrade -T openwrt-19.07.2-arv752dpw22-squashfs-sysupgrade-cca3-final.bin
sysupgrade -n openwrt-19.07.2-arv752dpw22-squashfs-sysupgrade-cca3-final.bin
~~~

Die ausgegebene SHA-256-Summe muss zum Release passen. sysupgrade -T muss
erfolgreich sein. Danach nicht unterbrechen; der Router startet neu. Root-Passwort
setzen und erst dann das eigene Netz konfigurieren.
