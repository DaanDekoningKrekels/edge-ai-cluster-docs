# Mendel Linux

## Introductie

In dit onderdeel wordt er onderzocht hoe Mendel verschilt van de Debian Linux distributie waarop het gebaseerd is, als ook hoe de communicatie met de Edge TPU gebeurt. Dit met twee doelen.
Als eerste om te weten te komen hoe een Debian, aangepast voor een Beowulf cluster, verder moet aangepast worden om op een Coral Dev Board, code naam *enterprise*, te kunnen draaien.
Het tweede doel is het kunnen detecteren en delen van de onderlinge Edge TPU's in de cluster zodat de hoofd node deze kan utiliseren.

---

## Wat is Mendel?

Mendel is een *lightweight derivative*, of verlichte aftakking  van de upstream Debian 10 (buster) Linux distributie. Deze gebruikt nog wel de upstream Debian binary packages om compatibiliteit te behouden en up to date te blijven met security patches. De wijzigingen zijn voornamelijk om de specifieke hardware van de Coral Dev Board te kunnen ondersteunen en AI toepassingen te kunnen draaien. Verdere wijzigingen zijn vooral omissies uit Debian die niet van toepassing zijn voor embedded systems als de Coral Dev Board.

> “To support Coral‘s hardware, we needed to build a version of Debian that produced initial bootable eMMC images and supported our specific peripherals.”

> “We generally discourage [building your own image from our tooling for one of our boards], as we put a great deal of effort into making sure our releases run well on the boards we target. Mendel is a proper Linux distribution: we work with packages, not images, and any image should be possible to upgrade to the latest release with a simple apt-get dist-upgrade without requiring a reboot (modulo kernel and bootloader updates, obviously, which require reflashing the board).”

> “Note that our distribution does not include everything from Debian; for example, Mendel does not include desktop environments and applications, because our system is designed for embedded systems. Also, the new SecureBoot and AppArmor changes do not apply to Mendel.”

*Aanhalingen uit [[1]](bronnen.md)*

### Broncode

De broncode voor Mendel Linux is beschikbaar op Google Git (enkel toegankelijk in Linux).
De repository is groot, maar het biedt wel een goed inzicht in wat er allemaal aanwezig is in Mendel Linux. `7810 directories, 91531 files`

*Broncode [[2]](.bronnen.md)*

### Mendel release notes

Hieronder is een opsomming van de belangrijkste update voor dit project.

De volledige release notes kunnen worden teruggevonden in de [Coral documentatie](https://coral.googlesource.com/docs/+/refs/heads/master/Releases.md) [[3]](bronnen.md).

#### 1.0 (alpha) - First Mendel release!

#### 2.0 (beaker)

#### 3.0 (chef)

#### 4.0 (day) - First release based on Debian 10 (buster):

- Support for Python 3.7

- Support for OpenCV and OpenCL

- Support for device tree overlays

#### 5.0 (eagle):

- Upgraded U-Boot bootloader to v2019.04

- Upgraded Vivante GPU drivers

#### 5.2 (eagle)

new Mendel 5.2 image for the Coral Dev Board and SoM (file version `20210518200035`) that includes a critical patch for the NXP SoC. (Does not apply to the Dev Board Mini.)

- Various kernel updates, including:
  - defconfig: Include USB FTDI driver
  - defconfig: Add micro k8s / k3s deps
  - apex: Add power control
  - m4: Add M4 device tree overlay
  - i2s: Enable I2S output on 40-pin header
  - rt5645: Add a sysfs-based override for hp-detect

#### 5.3 (eagle)

New Mendel 5.3 image is released for the Coral Dev Board and SoM (file version `20211117215217`).

- Updated preinstalled packages to grouper ML release:
  - python3-pycoral
  - libedgetpu1-std
  - python3-edgetpuvision

### Mendel Minimal

Dit is een package vanaf 2.0 (beaker) en geeft enkele dependencies aan.
Hierin bevind zich ook het script en woordenlijsten die de automatische naam toekenning doet van een board.

> Source: mendel-minimal
> Maintainer: AIY Projects <support-aiyprojects@google.com>
> Build-Depends: debhelper
> Section: misc
> Priority: optional
>
> Package: mendel-minimal
> Section: misc
> Priority: optional
> Architecture: all
>
> Depends: e2fsprogs(>=1.43), adduser, sudo, openssh-server, locales, avahi-daemon,  bluez, network-manager, passwd, python2.7, runonce, ${misc:Depends}
>
> Description: Defines the files and packages for a minimal Mendel install. This package contains the initial "run once" scripts that perform initial startup work such as resizing the root filesystem to match the emmc size, adding in known users, enabling services that users will likely want by default, and other housekeeping behaviors (such as forcing a regeneration of ssh host keys).
> This package actually contains the runonce scripts that do the above. Like the runonce package, removing this one should have little effect on a running system that has executed those scripts already.

*Aanhaling uit [[4]](bronnen.md)*

### Release Manifest om een Mendel Image te builden

Om zelf een Mendel image te maken moeten verschillende repositories van Google Coral gecombineerd worden. Hiervoor is een manifest nodig die [hier in de coral/releases](https://coral.googlesource.com/releases/+/refs/heads/master) [[5]](bronnen.md) gevonden kan worden.

Volgend manifest is voor de *enterprise* Mendel 5.3 (eagle) release. Dit geeft al een idee van wat er juist allemaal in zit. *Enterprise* is hier de codenaam van de Coral Dev Board.

> In general, each XML file in the manifest refers to each board configuration that Mendel supports, and each is named by the board's codename. The most common is the Coral EdgeTPU Dev Board, or “enterprise”, which is denoted by the `enterprise.xml` file.

*Aanhaling en onderstaande code uit [[6]](bronnen.md)*

```xml
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
  <remote fetch="https://coral.googlesource.com" name="coral" review="https://coral-review.googlesource.com"/>
  <remote fetch="sso://aiyprojects" name="coral-internal" review="sso://aiyprojects"/>

  <default remote="coral" revision="master"/>

  <project dest-branch="master" groups="notdefault,internal" name="a71ch-crypto-support" remote="coral-internal" revision="e543e5b712a5acca04bc3bd3c82c7629a0b2f8fe" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="a71ch-crypto-support-package" path="packages/a71-crypto-support" remote="coral-internal" revision="5c3a7afe2c6c66761f15d712e8969c5433592713" upstream="master"/>
  <project dest-branch="master" name="base-files" path="packages/base-files" revision="d0705056c8c8e453eae500fac743d85f0a99c939" upstream="master"/>
  <project dest-branch="master" name="bluez-imx" revision="2f651659c853ad1a4185f1aca68713c6f564c803" upstream="master"/>
  <project dest-branch="master" name="bluez-imx-debian" path="packages/bluez-imx" revision="af093c85244cf24661a079eace62191d0e60dd11" upstream="master"/>
  <project dest-branch="master" name="board-enterprise" path="board" revision="a27857c7424dc9b0ecb63a66bbbb440e355a2fbd" upstream="master"/>
  <project dest-branch="master" name="bpt" path="tools/bpt" revision="e2c77aebf72d56beb50329e9ce873ea834e02164" upstream="master"/>
  <project dest-branch="master" name="build" revision="ad406b1acd75d20b1f82d8f2e4f3e785962c86dc" upstream="master">
    <linkfile dest="Makefile" src="Makefile"/>
  </project>
  <project dest-branch="master" name="busybox" path="tools/busybox" revision="86ef6484a499639bf9429613b23c125a7428251c" upstream="master"/>
  <project dest-branch="master" name="docs" revision="46d84fc5d668aaca39596805d1b286dd8d109121" upstream="master">
    <linkfile dest="GettingStarted.md" src="GettingStarted.md"/>
  </project>
  <project dest-branch="master" name="edgetpudemo" path="packages/edgetpudemo" revision="8b49774cdcb2c876fc367fed563859959b69c814" upstream="master"/>
  <project dest-branch="master" name="edgetpuvision" path="packages/edgetpuvision" revision="d17d7fdfb92b6a0103f288522ad1b4d1fcca99c3" upstream="master"/>
  <project dest-branch="master" name="enterprise-testing-tools-debian" path="packages/enterprise-testing-tools" revision="7f6a8b2a473367a1ad053dcd391e504bb90a5d5f" upstream="master"/>
  <project dest-branch="master" name="img2simg" path="tools/img2simg" revision="ac0166eee9d4976d4756f137686f501982112fb1" upstream="master"/>
  <project dest-branch="master" name="imx-atf" revision="91af6468f66ad9b4219ad6333756015b54bdd109" upstream="master"/>
  <project dest-branch="master" name="imx-atf-debian" path="packages/imx-atf" revision="a76e979349a47bd45c50c7a063d203a5e850a802" upstream="master"/>
  <project dest-branch="master" name="imx-board-audio" path="packages/imx-board-audio" revision="2e387266398455f9ebb0a894682943c65e0d77a1" upstream="master"/>
  <project dest-branch="master" name="imx-board-tools" path="packages/imx-board-tools" revision="c1428376698b75fa8e0215bff9df38335ab95902" upstream="master"/>
  <project dest-branch="master" name="imx-board-wlan" path="packages/imx-board-wlan" revision="543f5441fd4051f63fcbce25daa5116de0106e9c" upstream="master"/>
  <project dest-branch="master" name="imx-board-wlan-src" path="imx-board-wlan" revision="276713bbe1ec417fabbe91baeedad75bcb3bee31" upstream="master"/>
  <project dest-branch="master" name="imx-firmware" revision="8510b4a900368694dd2781b82b49b556779fd9ec" upstream="master"/>
  <project dest-branch="master" name="imx-firmware-debian" path="packages/imx-firmware" revision="0e4a954f3535ada971fcfb8ababff52f01fd9e49" upstream="master"/>
  <project dest-branch="source" groups="notdefault,internal" name="imx-gpu-viv" remote="coral-internal" revision="2686ebf0c1072e6f755dd0cf2a7e109edefcee51" upstream="source"/>
  <project dest-branch="master" groups="notdefault,internal" name="imx-gpu-viv-debian" path="packages/imx-gpu-viv" remote="coral-internal" revision="339ad743dcd82dfe6e4fe3e5d31bf0f678dccb27" upstream="master"/>
  <project dest-branch="master" name="imx-gpu-viv-ko" revision="8aa0d455633b441846b161574622278782bb71c0" upstream="master"/>
  <project dest-branch="master" name="imx-gpu-viv-ko-debian" path="packages/imx-gpu-viv-ko" revision="0c480e396afc099b3fdf4f260dc146433853923a" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-bad" revision="2f2023174fcf0bfe02b4c5551d11db558cc13a6f" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-bad-debian" path="packages/imx-gst-plugins-bad" revision="1f19537325445cde6ec6d4d98b6d94b2c9a1a9c8" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-base" revision="8c643ac79be63aba114fd7682297858354c198b9" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-base-debian" path="packages/imx-gst-plugins-base" revision="51fd2ce6cc89ff4c813ee67a77305fdcc274bf86" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-good" revision="1cc1fc057e062deec93929fb7e1cec9be09586e1" upstream="master"/>
  <project dest-branch="master" name="imx-gst-plugins-good-debian" path="packages/imx-gst-plugins-good" revision="2f6eb6ef113b7000f20c0da1879603846c9e8f0f" upstream="master"/>
  <project dest-branch="master" name="imx-gst1.0-plugin" revision="1f03561701b45cf09647dae4be67308926cae788" upstream="master"/>
  <project dest-branch="master" name="imx-gst1.0-plugin-debian" path="packages/imx-gst1.0-plugin" revision="b029dadb3ced909ab8f1899caeef4f3a8fc48250" upstream="master"/>
  <project dest-branch="master" name="imx-gstreamer" revision="87a83f9012ffa6f12ac113faee9db3bf9d267b4e" upstream="master"/>
  <project dest-branch="master" name="imx-gstreamer-debian" path="packages/imx-gstreamer" revision="5b4eac9b9afd60a6bac7e6ae4afe70bd865ae722" upstream="master"/>
  <project dest-branch="master" name="imx-mkimage" path="tools/imx-mkimage" revision="cb60dc0fef8cf11cec733ee7bf69661658be1f50" upstream="master"/>
  <project dest-branch="master" name="imx-mkimage-debian" path="packages/imx-mkimage" revision="7de7deb54030022705692db9f5f8d0602fb5c632" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="imx-vpu-hantro" remote="coral-internal" revision="53cf72c95a96826121426f7105764a8d183b5d73" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="imx-vpu-hantro-debian" path="packages/imx-vpu-hantro" remote="coral-internal" revision="45a5585c8574440b4206a8ce3bb5d7626a117460" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="imx-vpuwrap" remote="coral-internal" revision="6190aad5334249f3fce0957970c36faba8ff6232" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="imx-vpuwrap-debian" path="packages/imx-vpuwrap" remote="coral-internal" revision="085e026083009e4c6079af223ddf3553510eafae" upstream="master"/>
  <project dest-branch="master" groups="notdefault,internal" name="kokoro-enterprise" path="kokoro" remote="coral-internal" revision="80185975785bc4638d921754bb04c27d80210008" upstream="master"/>
  <project dest-branch="master" name="libdrm-imx" revision="597f5b0e8edaf2b59d2115826688a5c65ce2ec77" upstream="master"/>
  <project dest-branch="master" name="libdrm-imx-debian" path="packages/libdrm-imx" revision="1ffa7f1cfc8ed50ea8544725b2ac65c850c50869" upstream="master"/>
  <project dest-branch="master" name="linux-imx" revision="c13f50afd3920f718f9becc9f47c31fccba00bf7" upstream="master"/>
  <project dest-branch="master" name="linux-imx-debian" path="packages/linux-imx" revision="281fbe6d1336851d26cefd8c5aa3b946869e0cdc" upstream="master"/>
  <project dest-branch="master" name="manifest" revision="eea9cb420babca51a6462fd2408133e33a64dfb2" upstream="master"/>
  <project dest-branch="master" name="mdt" path="tools/mdt" revision="10fe0ff3d8aa50e983cb6609c7f4dd701319ce67" upstream="master"/>
  <project dest-branch="master" name="mdt-services" path="packages/mdt-services" revision="1e630b3e410451a95caffdf69dadba814acefd8c" upstream="master"/>
  <project dest-branch="master" name="mendel-distro-info-data" path="packages/mendel-distro-info-data" revision="b7059b5b5558564e9bc3ae150b87393eee2bec3b" upstream="master"/>
  <project dest-branch="master" name="mendel-keyring" path="packages/mendel-keyring" revision="63dc30a7ce9813c3db6e83935eff1dd0689f95f1" upstream="master"/>
  <project dest-branch="master" name="mendel-minimal" path="packages/mendel-minimal" revision="ed398693e8427c758b7006d45544daf5cea4a420" upstream="master"/>
  <project dest-branch="master" name="mendel-testing-tools" revision="6294c06d27d56af8947f1e0581ac0a0783ddb911" upstream="master"/>
  <project dest-branch="master" name="meta-enterprise" path="packages/meta-enterprise" revision="96ae5f764278c8ea9f024290f227ab3ef72c061d" upstream="master"/>
  <project dest-branch="master" name="meta-mendel" path="packages/meta-mendel" revision="b09a8ef832606ef2c1541413416f10d7cc1952ea" upstream="master"/>
  <project dest-branch="master" name="prebuilts" revision="a5dd58509bf51ac24990a6d092da3902da939942" upstream="master"/>
  <project dest-branch="master" name="releases" revision="95f05355f180b370b88912571e9a26a5917c461f" upstream="master"/>
  <project dest-branch="master" name="runonce" path="packages/runonce" revision="cacfb1a678b5e229e914bcf31f2e67dd092cb5a3" upstream="master"/>
  <project dest-branch="master" name="uboot-imx" revision="0353addb5507bb259c9232c8b23e3d604eebd878" upstream="master"/>
  <project dest-branch="master" name="uboot-imx-debian" path="packages/uboot-imx" revision="d537a556cd4f2b0a98a408b2f6f206e08972e21c" upstream="master"/>
  <project dest-branch="master" name="usb-gadget" path="packages/usb-gadget" revision="6a6ee8d6e2d8fa12c2959c45c5b18b79cf86e99f" upstream="master"/>
  <project dest-branch="master" name="vitalsd" path="packages/vitalsd" revision="bc182c41c782879cdfd756e541dcde6656140228" upstream="master"/>
  <project dest-branch="master" name="wayland-protocols-imx" revision="50ea8fbdd1b768ccaa33dfee43b0f12245ef09b8" upstream="master"/>
  <project dest-branch="master" name="wayland-protocols-imx-debian" path="packages/wayland-protocols-imx" revision="77620b41e6ddea9569ac2be9d35447761974db46" upstream="master"/>
  <project dest-branch="master" name="weston-imx" revision="6172a2a984fd0b423938ac08a64748439ea1b36e" upstream="master"/>
  <project dest-branch="master" name="weston-imx-debian" path="packages/weston-imx" revision="d84b92af5213fe65bc79e107b7a8cda7fe6aeb29" upstream="master"/>
</manifest>
```

---
