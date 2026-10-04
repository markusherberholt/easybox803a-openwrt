# 4. Build und Reproduzierbarkeit

Referenz: OpenWrt **v19.07.2**, Commit
33732f4a9c17921b782167a0dcaba9703d4e6753, Ziel arcadyan_arv752dpw22
(Lantiq/xway).

~~~sh
git clone --branch v19.07.2 --depth 1 https://github.com/openwrt/openwrt.git openwrt-19.07.2
cd openwrt-19.07.2
./scripts/feeds update -a
./scripts/feeds install -a
cp /pfad/zum/repository/config/openwrt-19.07.2-arv752dpw22.config .config
make defconfig
~~~

Patches prüfen und anwenden:

~~~sh
git apply --check /pfad/zum/repository/patches/openwrt/0001-lantiq-arv752dpw22-add-cca3-bootarg.patch
git apply --check /pfad/zum/repository/patches/openwrt/0002-linux-firmware-skip-qca99x0-board-download.patch
git apply /pfad/zum/repository/patches/openwrt/0001-lantiq-arv752dpw22-add-cca3-bootarg.patch
git apply /pfad/zum/repository/patches/openwrt/0002-linux-firmware-skip-qca99x0-board-download.patch
install -m 0644 /pfad/zum/repository/patches/u-boot/0300-arv752dpw22-stack-switch-and-ramboot.patch package/boot/uboot-lantiq/patches/
make -j"$(nproc)" V=s
~~~

Die U-Boot-Anpassung vergrößert den Stack-Abstand für den BRN-RAM-Boot, gibt
den AR8216-Switch-Reset über GPIO19 frei und kopiert die Firmware vor bootm
von NOR in den RAM. Der deaktivierte QCA-Download ist nur ein Workaround für
den historischen Build, keine Laufzeitänderung.
