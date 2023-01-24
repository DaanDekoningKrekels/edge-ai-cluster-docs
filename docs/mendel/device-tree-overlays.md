# Device Tree Overlays

## Device Tree lezen

De Device Tree overlay wordt opgeslagen als een .dtb (device tree blob) code file. Dit is gecompileerde code. Om deze te kunnen lezen moet de .dtb file dus eerst gedecompileerd worden.

### Installatie Device Tree compiler

```bash
sudo apt-get install device-tree-compiler
```

### Decompileren

De .dtb files bevinden zich in de `"/boot"` directory.

```bash
mendel@undefined-yarn:~/share$ /boot ls
boot.scr                 fsl-imx8mq-phanbell.dtb      Image         System.map-4.14.98-imx
config-4.14.98-imx       fsl-imx8mq-phanbell-m4.dtbo  lost+found    u-boot.imx
fsl-imx8mm-columbia.dtb  fsl-imx8mq-yorktown.dtb      overlays.txt  vmlinuz-4.14.98-imx
```

Er bevinden zich drie .dtb files in `/root`. In één van deze moet de TPU beschreven staan.

De-compileren gebeurd met volgend commando: `sudo dtc -I dtb -O dts /boot/<input-filename>.dtb -o /tmp/<output-filename>.dts` Zo komt de ge-de-compileerde code in een menselijk leesbaar .dts file in de `/temp` directory terecht [[26]](bronnen.md).

Dit voor de drie files:

```bash
sudo dtc -I dtb -O dts /boot/fsl-imx8mm-columbia.dtb -o /tmp/fsl-imx8mm-columbia.dts
```

```bash
sudo dtc -I dtb -O dts /boot/fsl-imx8mq-phanbell.dtb -o /tmp/fsl-imx8mq-phanbell.dts
```

```bash
sudo dtc -I dtb -O dts /boot/fsl-imx8mq-yorktown.dtb -o /tmp/fsl-imx8mq-yorktown.dts
```

Resultaat:

```bash
mendel@undefined-yarn:/tmp$ ls
pulse-PKdhtXMmr18n
systemd-private-b047b4ad188845f79678d97a41208ab6-systemd-timesyncd.service-iLofIQ
fsl-imx8mm-columbia.dts
fsl-imx8mq-phanbell.dts
fsl-imx8mq-yorktown.dts
```

**De Device Tree Overlay bestaat hier dus uit drie lagen:**

- [`fsl-imx8mm-columbia.dts`](./assets/fsl-imx8mm-columbia.dts) beschrijft het *FSL i.MX8MM Columbia board* in 2375 lijnen code. Dit is de basis en onderste laag.
- [`fsl-imx8mq-phanbell.dts`](./assets/fsl-imx8mq-phanbell.dts) beschrijft de *Freescale i.MX8MQ Phanbell* in 2522 lijnen code. Dit is een uitbreiding en de middelste laag.
- [`fsl-imx8mq-yorktown.dts`](./assets/fsl-imx8mq-yorktown.dts) beschrijft ook de *Freescale i.MX8MQ Phanbell*, maar in een meer uitgebreidere 2827 lijnen code. Aan de uitbreidingen, en het gebruik van de codenaam Yorktown die naar de Edge TPU verwijst, kunnen we afleiden dat het hier gaat over de bovenste laag die, onder andere, de toevoegingen voor de Edge TPU beschrijft.

### De code

De code in deze *Device Tree Overlays* is zeer uitgebreid. Er wordt hier dus enkel code bekeken met referenties naar *Edge TPU* of *PCI*.

#### fsl-imx8mm-columbia.dts

Columbia bevat geen referenties naar een TPU.

#### fsl-imx8mq-phanbell.dts

In Phanbell staat de edgetpu-audio-card:

```bash
sound-rt5645 {
                compatible = "google,edgetpu-audio-card";
                model = "edgetpu-audio-card";
                audio-cpu = < 0x63 >;
                audio-codec = < 0x64 >;
                google,model = "edgetpu-audio-card";
                status = "okay";
        };
```

#### fsl-imx8mq-yorktown.dts

De edgetpu-audio-card staat ook in Yorktown, maar met een andere `audio-cpu`.

```bash
sound-rt5645 {
                compatible = "google,edgetpu-audio-card";
                model = "edgetpu-audio-card";
                audio-cpu = < 0x6a >;
                audio-codec = < 0x6b >;
                google,model = "edgetpu-audio-card";
                status = "okay";
        };
```



Hierop volgend staat een TPU genaamd `yorktown-tpu` bestaande uit `akira` onderdelen.

```bash
yorktown-tpu {
                compatible = "google,yorktown-tpu";

                akira0 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x6c 0x01 0x00 0x6c 0x00 0x01 >;
                };

                akira1 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x6d 0x01 0x00 0x6d 0x00 0x01 >;
                };

                akira2 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x6e 0x01 0x00 0x6e 0x00 0x01 >;
                };

                akira3 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x6f 0x01 0x00 0x6f 0x00 0x01 >;
                };

                akira4 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x70 0x01 0x00 0x70 0x00 0x01 >;
                };

                akira5 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x71 0x01 0x00 0x71 0x00 0x01 >;
                };

                akira6 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x72 0x01 0x00 0x72 0x00 0x01 >;
                };

                akira7 {
                        compatible = "google,akira";
                        status = "okay";
                        tpu-gpios = < 0x73 0x01 0x00 0x73 0x00 0x01 >;
                };
        };

        akira01_reset {
                compatible = "gpio-reset";
                pinctrl-0 = < 0x74 >;
                pinctrl-names = "default";
                reset-gpios = < 0x53 0x0a 0x01 >;
                reset-delay-us = < 0x96 >;
                #reset-cells = < 0x00 >;
                linux,phandle = < 0x5c >;
                phandle = < 0x5c >;
        };

        akira23_reset {
                compatible = "gpio-reset";
                pinctrl-0 = < 0x75 >;
                pinctrl-names = "default";
                reset-gpios = < 0x26 0x05 0x01 >;
                reset-delay-us = < 0x96 >;
                #reset-cells = < 0x00 >;
                linux,phandle = < 0x5d >;
                phandle = < 0x5d >;
        };

        akira45_reset {
                compatible = "gpio-reset";
                pinctrl-0 = < 0x76 >;
                pinctrl-names = "default";
                reset-gpios = < 0x4a 0x14 0x01 >;
                reset-delay-us = < 0x96 >;
                #reset-cells = < 0x00 >;
                linux,phandle = < 0x5e >;
                phandle = < 0x5e >;
        };

        akira67_reset {
                compatible = "gpio-reset";
                pinctrl-0 = < 0x77 >;
                pinctrl-names = "default";
                reset-gpios = < 0x39 0x09 0x01 >;
                reset-delay-us = < 0x96 >;
                #reset-cells = < 0x00 >;
                linux,phandle = < 0x5f >;
                phandle = < 0x5f >;
        };
```



Op het einde onder `__symbols__` staat:

```bash
__symbols__ {
				...
				iomuxc = "/iomuxc@30330000";
                ...
                pinctrl_pcie0 = "/iomuxc@30330000/imx8mq-phanbell/pcie0grp";
                pinctrl_pcie1 = "/iomuxc@30330000/imx8mq-phanbell/pcie1grp";
                pinctrl_sai2 = "/iomuxc@30330000/imx8mq-phanbell/sai2grp";
                pinctrl_gpio = "/iomuxc@30330000/imx8mq-phanbell/gpio_ctrlgrp";
                pinctrl_pwm1 = "/iomuxc@30330000/imx8mq-phanbell/pwm1";
                pinctrl_pwm2 = "/iomuxc@30330000/imx8mq-phanbell/pwm2";
                pinctrl_pwm3 = "/iomuxc@30330000/imx8mq-phanbell/pwm3";
                pinctrl_ecspi1 = "/iomuxc@30330000/imx8mq-phanbell/ecspi1grp";
                pinctrl_ecspi1_cs = "/iomuxc@30330000/imx8mq-phanbell/ecspi1_cs_grp";
                pinctrl_usb5744 = "/iomuxc@30330000/imx8mq-phanbell/usb5744";
                pinctrl_i2c_mux = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_mux_grp";
                pinctrl_akira01 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira01_grp";
                pinctrl_akira23 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira23_grp";
                pinctrl_akira45 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira45_grp";
                pinctrl_akira67 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira67_grp";
                pinctrl_csi1 = "/iomuxc@30330000/imx8mq-phanbell/csi1grp";
                ...
				akira0 = "/i2c@30a40000/i2c-mux@70/i2c@0/gpio@43";
                akira1 = "/i2c@30a40000/i2c-mux@70/i2c@0/gpio@44";
                akira2 = "/i2c@30a40000/i2c-mux@70/i2c@1/gpio@43";
                akira3 = "/i2c@30a40000/i2c-mux@70/i2c@1/gpio@44";
                akira4 = "/i2c@30a40000/i2c-mux@70/i2c@2/gpio@43";
                akira5 = "/i2c@30a40000/i2c-mux@70/i2c@2/gpio@44";
                akira6 = "/i2c@30a40000/i2c-mux@70/i2c@3/gpio@43";
                akira7 = "/i2c@30a40000/i2c-mux@70/i2c@3/gpio@44";
                ...
                pcie0 = "/pcie@0x33800000";
                pcie1 = "/pcie@0x33c00000";
                ...
                reg_apex = "/regulators/apex_pmic";
                ...
                akira01_reset = "/akira01_reset";
                akira23_reset = "/akira23_reset";
                akira45_reset = "/akira45_reset";
                akira67_reset = "/akira67_reset";
        };
```

Hieruit kan echter niets worden afgeleid hoe de TPU met de CPU communiceert.

### Coral Google Git referenties

Deze code kan ook teruggevonden worden in de Coral repository op Google Git [[27]](bronnen.md).

```bash
/*
 * Copyright 2018 NXP
 *
 * This program is free software; you can redistribute it and/or
 * modify it under the terms of the GNU General Public License
 * as published by the Free Software Foundation; either version 2
 * of the License, or (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 */
#include "fsl-imx8mq-som.dtsi"
/ {
	model = "Freescale i.MX8MQ Phanbell";
	compatible = "fsl,imx8mq-phanbell", "fsl,imx8mq";
	sound-rt5645 {
		compatible = "google,edgetpu-audio-card";
		model = "edgetpu-audio-card";
		audio-cpu = <&sai2>;
		audio-codec = <&rt5645>;
		google,model = "edgetpu-audio-card";
        status = "okay";
	};
	fan: gpio_fan {
		gpios = <&gpio3 13 GPIO_ACTIVE_LOW>;
		status = "okay";
	};
	yorktown-tpu {
		compatible = "google,yorktown-tpu";
		akira0 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira0 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira0 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira1 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira1 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira1 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira2 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira2 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira2 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira3 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira3 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira3 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira4 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira4 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira4 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira5 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira5 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira5 0 GPIO_ACTIVE_LOW>;   // reset
		};

		akira6 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira6 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira6 0 GPIO_ACTIVE_LOW>;   // reset
		};
		akira7 {
			compatible = "google,akira";
			status = "okay";
			tpu-gpios = <&akira7 1 GPIO_ACTIVE_HIGH>, // pmic
			            <&akira7 0 GPIO_ACTIVE_LOW>;   // reset
		};
	};

	akira01_reset: akira01_reset {
		compatible = "gpio-reset";
		pinctrl-0 = <&pinctrl_akira01>;
		pinctrl-names = "default";
		reset-gpios = <&gpio1 10 GPIO_ACTIVE_LOW>;
		reset-delay-us = <150>;
		#reset-cells = <0>;
	};
	akira23_reset: akira23_reset {
		compatible = "gpio-reset";
		pinctrl-0 = <&pinctrl_akira23>;
		pinctrl-names = "default";
		reset-gpios = <&gpio5 5 GPIO_ACTIVE_LOW>;
		reset-delay-us = <150>;
		#reset-cells = <0>;
	};
	akira45_reset: akira45_reset {
		compatible = "gpio-reset";
		pinctrl-0 = <&pinctrl_akira45>;
		pinctrl-names = "default";
		reset-gpios = <&gpio2 20 GPIO_ACTIVE_LOW>;
		reset-delay-us = <150>;
		#reset-cells = <0>;
	};
	akira67_reset: akira67_reset {
		compatible = "gpio-reset";
		pinctrl-0 = <&pinctrl_akira67>;
		pinctrl-names = "default";
		reset-gpios = <&gpio3 9 GPIO_ACTIVE_LOW>;
		reset-delay-us = <150>;
		#reset-cells = <0>;
	};
};
```

Op de Coral Google Git kan ook wat extra info over deze Yorktown en AKira TPU gevonden worden [[28]](bronnen.md), maar niet veel:

```google git
Akira power management driver and Yorktown TPU "bus" driver

- Simple platform_driver to power sequence Akira
- Bus driver that kicks off Akira initializations, and forces rescanning
of the PCI bus in the future

...

@@ -526,6 +526,21 @@
            Enable this configuration option to enable the host side test driver
            for PCI Endpoint.

+config TPU_AKIRA
+	tristate "Akira M.2 support"
+	default n
+	depends on I2C
+	depends on GPIOLIB
+	help
+	  Support for the Akira M.2 Card
+
+config YORKTOWN_TPU
+	tristate "Yorktown TPU device"
+	depends on TPU_AKIRA
+	default n
+	help
+	  Driver for managing multiple TPUs on Yorktown.
+
```

In de *Accelerating Reduction and Scan Using Tensor Core Units* [[29]](bronnen.md) paper wordt vermeld dat er 8 cores zitten in de Google's TPU v3. Omdat er ook 8 Akira onderdelen zijn binnen de Yorktown TPU kunnen we afleiden dat een Akira een Core is, die dan verder nog bestaat uit 2 MXU's.

> 2 TENSOR CORES UNITS (TCUS) A marquee feature of NVIDIA’s GPUs (Volta’s Tesla V100 and Turning’s TU102 architectures) and Google’s TPUs are their TCUs— a programmable matrix multiply and accumulate hardware units, called Tensor Cores by NVIDIA and matrix-multiply-units (MXUs) by Google. While there are other competing TCU implementations, both NVIDIA Tensor Cores and Google’s TPU are by far the most popular. At a high level, their functionality and architectural design are similar. They both subdivide the device into cores, with each having multiple processing block (or subcores) and TCUs. Figure 1 illustrates a subcore in an NVIDIA SM, with the V100 containing 80 SMs and each having 4 subcores. In turn, each subcore contains two Tensor Cores — for a total of 640 Tensor Cores and achieve a 12× throughput improvement over previous generation Tesla P100 [54]. Google’s TPUv3 device, on the other hand, has 8 cores — 4 chips each with 2 cores — with each core having 2 MXUs.

---

## System on Module (SoM)

Verder onderzoek met de [datasheet van de Coral Dev Board](./assets/google-coral-dev-board-03062019-G950-01455-01-datasheet.pdf) [[31]](bronnen.md) duid aan dat de Edge TPU zich samen met de i.MX8M SoC op een System on Module (SoM) bevindt. Hiervan is nog een aparte [SoM datasheet](./assets/coral-som-datasheet.pdf) ([online](https://coral.ai/static/files/Coral-SoM-datasheet.pdf)) [[32]](bronnen.md) die ons meer info kan geven.

> The SOM is based on NXP's iMX8M system-on-chip (SOC) and contains all the essential hardware systems, including the Edge TPU and Wi-Fi/Bluetooth radios. It is attached to the Dev Board baseboard with three 100-pin board-to-board
> connectors.

*Uit Google Coral Dev Board datasheet, p21 [[31]](bronnen.md)*

In volgend *Fi 1* zien we dat de Edge TPU communiceert met de SoC via PCIe2.

[![](./assets/som-blockdiagram.png 'Fig 1: uit Coral SoM datahseet, p7, 1.1 Block Diagrams [32]')](bronnen.md)

> 5.5 PCIe
>
> The SoC i ncludes PCIE1 and PCIE2 l ines that are routed to the baseboard connectors, but you should not need to connect
> these and y ou should not remap these with your own device tree because both are used on the SoM for Wi-Fi ( PCIE1) and
> the Edge TPU ( PCIE2).

*Uit Coral SoM datahseet p16, 5 Peripheral interfaces [[32]](bronnen.md)*

Terug naar de Device Tree Overlay om te weten te komen hoe de CPU op de SoC communiceert met de Edge TPU via PCIe2. Dit door het zoeken naar referenties naar de **imx8mq**, en meer precies de **imx8mq-phanbell**.

Onderstaande referenties komen zowel in `phanbell.dts` als `yorktown.dts`  terug, maar zoals eerder aangetoond is **yorktown** te bovenste laag. Dit is hier ook zichtbaar met een meer uitgebreidere beschrijving van de **imx8mq-phanbell**.

Bovenaan de naamgeving van de SoC, zijnde Freescale i.MX8MQ Phanbell.

````bash
compatible = "fsl,imx8mq-phanbell\0fsl,imx8mq";
        interrupt-parent = < 0x01 >;
        #address-cells = < 0x02 >;
        #size-cells = < 0x02 >;
        model = "Freescale i.MX8MQ Phanbell";
````

De IO MUX `iomuxc@30330000` waar de Edge TPU in voorkomt onder `imx8mq-phanbell`.

````bash
iomuxc@30330000 {
                compatible = "fsl,imx8mq-iomuxc";
                reg = < 0x00 0x30330000 0x00 0x10000 >;
                pinctrl-names = "default";
                pinctrl-0 = < 0x2c >;
                pinctrl-1 = < 0x2d >;
                linux,phandle = < 0x8b >;
                phandle = < 0x8b >;

                imx8mq-evk {

                        hoggrp {
                                fsl,pins = < 0x134 0x39c 0x00 0x05 0x00 0x19 0x138 0x3a0 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x2c >;
                                phandle = < 0x2c >;
                        };

                        fec1grp {
                                fsl,pins = < 0x68 0x2d0 0x00 0x00 0x00 0x03 0x6c 0x2d4 0x4c0 0x00 0x01 0x23 0x70 0x2d8 0x00 0x00 0x00 0x1f 0x74 0x2dc 0x00 0x00 0x00 0x1f 0x78 0x2e0 0x00 0x00 0x00 0x1f 0x7c 0x2e4 0x00 0x00 0x00 0x1f 0x9c 0x304 0x00 0x00 0
x00 0x91 0x98 0x300 0x00 0x00 0x00 0x91 0x94 0x2fc 0x00 0x00 0x00 0x91 0x90 0x2f8 0x00 0x00 0x00 0x91 0x84 0x2ec 0x00 0x00 0x00 0x1f 0x8c 0x2f4 0x00 0x00 0x00 0x91 0x88 0x2f0 0x00 0x00 0x00 0x91 0x80 0x2e8 0x00 0x00 0x00 0x1f 0x4c 0x2b4 0x00 0x00 0x00 0x
19 >;
                                linux,phandle = < 0x4e >;
                                phandle = < 0x4e >;
                        };

                        i2c1grp {
                                fsl,pins = < 0x214 0x47c 0x00 0x00 0x00 0x4000007f 0x218 0x480 0x00 0x00 0x00 0x4000007f >;
                                linux,phandle = < 0x51 >;
                                phandle = < 0x51 >;
                        };

                        i2c2grp {
                                fsl,pins = < 0x21c 0x484 0x00 0x00 0x00 0x4000007f 0x220 0x488 0x00 0x00 0x00 0x4000007f >;
                                linux,phandle = < 0x54 >;
                                phandle = < 0x54 >;
                        };

                        i2c3grp {
                                fsl,pins = < 0x224 0x48c 0x00 0x00 0x00 0x4000007f 0x228 0x490 0x00 0x00 0x00 0x4000007f >;
                                linux,phandle = < 0x57 >;
                                phandle = < 0x57 >;
                        };

                        dvfsgrp {
                                fsl,pins = < 0x5c 0x2c4 0x00 0x00 0x00 0x16 >;
                                linux,phandle = < 0x8c >;
                                phandle = < 0x8c >;
                        };

                        typecgrp {
                                fsl,pins = < 0xf4 0x35c 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x58 >;
                                phandle = < 0x58 >;
                        };

                        uart1grp {
                                fsl,pins = < 0x234 0x49c 0x4f4 0x00 0x00 0x49 0x238 0x4a0 0x00 0x00 0x00 0x49 >;
                                linux,phandle = < 0x3a >;
                                phandle = < 0x3a >;
                        };

                        uart2grp {
                                fsl,pins = < 0x23c 0x4a4 0x4fc 0x00 0x00 0x49 0x240 0x4a8 0x00 0x00 0x00 0x49 0x24c 0x4b4 0x00 0x01 0x00 0x49 0x250 0x4b8 0x4f8 0x01 0x01 0x49 0x10c 0x374 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x3c >;
                                phandle = < 0x3c >;
                        };

                        uart3grp {
                                fsl,pins = < 0x244 0x4ac 0x504 0x00 0x02 0x49 0x248 0x4b0 0x00 0x00 0x00 0x49 >;
                                linux,phandle = < 0x3b >;
                                phandle = < 0x3b >;
                        };

                        usdhc1grp {
                                fsl,pins = < 0xa0 0x308 0x00 0x00 0x00 0x83 0xa4 0x30c 0x00 0x00 0x00 0xc3 0xa8 0x310 0x00 0x00 0x00 0xc3 0xac 0x314 0x00 0x00 0x00 0xc3 0xb0 0x318 0x00 0x00 0x00 0xc3 0xb4 0x31c 0x00 0x00 0x00 0xc3 0xb8 0x320 0x00 0x00 0x
00 0xc3 0xbc 0x324 0x00 0x00 0x00 0xc3 0xc0 0x328 0x00 0x00 0x00 0xc3 0xc4 0x32c 0x00 0x00 0x00 0xc3 0xcc 0x334 0x00 0x00 0x00 0x83 0xc8 0x330 0x00 0x00 0x00 0xc1 >;
                                linux,phandle = < 0x43 >;
                                phandle = < 0x43 >;
                        };

                        usdhc1grp100mhz {
                                fsl,pins = < 0xa0 0x308 0x00 0x00 0x00 0x85 0xa4 0x30c 0x00 0x00 0x00 0xc5 0xa8 0x310 0x00 0x00 0x00 0xc5 0xac 0x314 0x00 0x00 0x00 0xc5 0xb0 0x318 0x00 0x00 0x00 0xc5 0xb4 0x31c 0x00 0x00 0x00 0xc5 0xb8 0x320 0x00 0x00 0x
00 0xc5 0xbc 0x324 0x00 0x00 0x00 0xc5 0xc0 0x328 0x00 0x00 0x00 0xc5 0xc4 0x32c 0x00 0x00 0x00 0xc5 0xcc 0x334 0x00 0x00 0x00 0x85 0xc8 0x330 0x00 0x00 0x00 0xc1 >;
                                linux,phandle = < 0x44 >;
                                phandle = < 0x44 >;
                        };

                        usdhc1grp200mhz {
                                fsl,pins = < 0xa0 0x308 0x00 0x00 0x00 0x87 0xa4 0x30c 0x00 0x00 0x00 0xc7 0xa8 0x310 0x00 0x00 0x00 0xc7 0xac 0x314 0x00 0x00 0x00 0xc7 0xb0 0x318 0x00 0x00 0x00 0xc7 0xb4 0x31c 0x00 0x00 0x00 0xc7 0xb8 0x320 0x00 0x00 0x
00 0xc7 0xbc 0x324 0x00 0x00 0x00 0xc7 0xc0 0x328 0x00 0x00 0x00 0xc7 0xc4 0x32c 0x00 0x00 0x00 0xc7 0xcc 0x334 0x00 0x00 0x00 0x87 0xc8 0x330 0x00 0x00 0x00 0xc1 >;
                                linux,phandle = < 0x45 >;
                                phandle = < 0x45 >;
                        };

                        usdhc2grpgpio {
                                fsl,pins = < 0xd0 0x338 0x00 0x05 0x00 0x41 0xec 0x354 0x00 0x05 0x00 0x41 >;
                                linux,phandle = < 0x47 >;
                                phandle = < 0x47 >;
                        };

                        usdhc2grp {
                                fsl,pins = < 0xd4 0x33c 0x00 0x00 0x00 0x83 0xd8 0x340 0x00 0x00 0x00 0xc3 0xdc 0x344 0x00 0x00 0x00 0xc3 0xe0 0x348 0x00 0x00 0x00 0xc3 0xe4 0x34c 0x00 0x00 0x00 0xc3 0xe8 0x350 0x00 0x00 0x00 0xc3 0x38 0x2a0 0x00 0x01 0x
00 0xc1 >;
                                linux,phandle = < 0x46 >;
                                phandle = < 0x46 >;
                        };

                        usdhc2grp100mhz {
                                fsl,pins = < 0xd4 0x33c 0x00 0x00 0x00 0x85 0xd8 0x340 0x00 0x00 0x00 0xc5 0xdc 0x344 0x00 0x00 0x00 0xc5 0xe0 0x348 0x00 0x00 0x00 0xc5 0xe4 0x34c 0x00 0x00 0x00 0xc5 0xe8 0x350 0x00 0x00 0x00 0xc5 0x38 0x2a0 0x00 0x01 0x
00 0xc1 >;
                                linux,phandle = < 0x48 >;
                                phandle = < 0x48 >;
                        };

                        usdhc2grp200mhz {
                                fsl,pins = < 0xd4 0x33c 0x00 0x00 0x00 0x87 0xd8 0x340 0x00 0x00 0x00 0xc7 0xdc 0x344 0x00 0x00 0x00 0xc7 0xe0 0x348 0x00 0x00 0x00 0xc7 0xe4 0x34c 0x00 0x00 0x00 0xc7 0xe8 0x350 0x00 0x00 0x00 0xc7 0x38 0x2a0 0x00 0x01 0x
00 0xc1 >;
                                linux,phandle = < 0x49 >;
                                phandle = < 0x49 >;
                        };

                        wdoggrp {
                                fsl,pins = < 0x30 0x298 0x00 0x01 0x00 0xc6 >;
                                linux,phandle = < 0x61 >;
                                phandle = < 0x61 >;
                        };

                        pmicirq {
                                fsl,pins = < 0x34 0x29c 0x00 0x00 0x00 0x41 >;
                                linux,phandle = < 0x52 >;
                                phandle = < 0x52 >;
                        };

                        mipi_dsi_en {
                                fsl,pins = < 0x1f4 0x45c 0x00 0x05 0x00 0x16 >;
                                linux,phandle = < 0x25 >;
                                phandle = < 0x25 >;
                        };
                };

                imx8mq-phanbell {

                        pcie0grp {
                                fsl,pins = < 0x22c 0x494 0x524 0x12 0x00 0x76 0x120 0x388 0x00 0x05 0x00 0x16 0x11c 0x384 0x00 0x05 0x00 0x16 0x28 0x290 0x00 0x05 0x00 0x05 >;
                                linux,phandle = < 0x64 >;
                                phandle = < 0x64 >;
                        };

                        pcie1grp {
                                fsl,pins = < 0x230 0x498 0x528 0x12 0x00 0x76 0x13c 0x3a4 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x67 >;
                                phandle = < 0x67 >;
                        };

                        sai2grp {
                                fsl,pins = < 0x1bc 0x424 0x00 0x00 0x00 0xd6 0x1c0 0x428 0x00 0x00 0x00 0xd6 0x1c8 0x430 0x00 0x00 0x00 0xd6 0x1c4 0x42c 0x00 0x00 0x00 0xd6 0x1b8 0x420 0x00 0x00 0x00 0xd6 0x1ec 0x454 0x00 0x05 0x00 0xd6 >;
                                linux,phandle = < 0x4d >;
                                phandle = < 0x4d >;
                        };

                        gpio_ctrlgrp {
                                fsl,pins = < 0x40 0x2a8 0x00 0x00 0x00 0x19 0x44 0x2ac 0x00 0x00 0x00 0x19 0x48 0x2b0 0x00 0x00 0x00 0x19 0x14c 0x3b4 0x00 0x05 0x00 0x19 0x154 0x3bc 0x00 0x05 0x00 0x19 0x164 0x3cc 0x00 0x05 0x00 0x19 0x184 0x3ec 0x00 0x0
5 0x00 0x19 0x188 0x3f0 0x00 0x05 0x00 0x19 0x18c 0x3f4 0x00 0x05 0x00 0x19 0x204 0x46c 0x00 0x05 0x00 0x19 0x20c 0x474 0x00 0x05 0x00 0x19 0x210 0x478 0x00 0x05 0x00 0x19 0x16c 0x3d4 0x00 0x05 0x00 0x19 0x170 0x3d8 0x00 0x05 0x00 0x19 0x16c 0x3d4 0x00 0
x05 0x00 0x19 0x1a4 0x40c 0x00 0x05 0x00 0x19 0x17c 0x3e4 0x00 0x05 0x00 0x19 0x150 0x3b8 0x00 0x05 0x00 0x19 0x1cc 0x434 0x00 0x05 0x00 0x19 0x1e8 0x450 0x00 0x05 0x00 0x19 0x15c 0x3c4 0x00 0x05 0x00 0x19 0x128 0x390 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x2d >;
                                phandle = < 0x2d >;
                        };

                        pwm1 {
                                fsl,pins = < 0x2c 0x294 0x00 0x01 0x00 0x7f >;
                                linux,phandle = < 0x0d >;
                                phandle = < 0x0d >;
                        };

                        pwm2 {
                                fsl,pins = < 0x5c 0x2c4 0x00 0x05 0x00 0x7f >;
                                linux,phandle = < 0x0e >;
                                phandle = < 0x0e >;
                        };

                        pwm3 {
                                fsl,pins = < 0x60 0x2c8 0x00 0x05 0x00 0x7f >;
                                linux,phandle = < 0x0f >;
                                phandle = < 0x0f >;
                        };

                        ecspi1grp {
                                fsl,pins = < 0x1f4 0x45c 0x00 0x00 0x00 0x82 0x1f8 0x460 0x00 0x00 0x00 0x82 0x1fc 0x464 0x00 0x00 0x00 0x82 >;
                                linux,phandle = < 0x37 >;
                                phandle = < 0x37 >;
                        };

                        ecspi1_cs_grp {
                                fsl,pins = < 0x200 0x468 0x00 0x05 0x00 0x82 0xfc 0x364 0x00 0x05 0x00 0x82 >;
                                linux,phandle = < 0x38 >;
                                phandle = < 0x38 >;
                        };

                        usb5744 {
                                fsl,pins = < 0x160 0x3c8 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x59 >;
                                phandle = < 0x59 >;
                        };

                        pinctrl_mux_grp {
                                fsl,pins = < 0x108 0x370 0x00 0x05 0x00 0x18 >;
                                linux,phandle = < 0x5b >;
                                phandle = < 0x5b >;
                        };

                        pinctrl_akira01_grp {
                                fsl,pins = < 0x50 0x2b8 0x00 0x00 0x00 0x19 >;
                                linux,phandle = < 0x74 >;
                                phandle = < 0x74 >;
                        };

                        pinctrl_akira23_grp {
                                fsl,pins = < 0x1f0 0x458 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x75 >;
                                phandle = < 0x75 >;
                        };

                        pinctrl_akira45_grp {
                                fsl,pins = < 0xf0 0x358 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x76 >;
                                phandle = < 0x76 >;
                        };

                        pinctrl_akira67_grp {
                                fsl,pins = < 0x118 0x380 0x00 0x05 0x00 0x19 >;
                                linux,phandle = < 0x77 >;
                                phandle = < 0x77 >;
                        };

                        csi1grp {
                                fsl,pins = < 0x114 0x37c 0x00 0x05 0x00 0x19 0x58 0x2c0 0x00 0x00 0x00 0x19 >;
                                linux,phandle = < 0x55 >;
                                phandle = < 0x55 >;
                        };
                };
        };
````



Deze komen ook terug in de `__symbols__` onderaan.

````bash
__symbols__ {
                CPU_SLEEP = "/cpus/idle-states/cpu-sleep";
                CLUSTER_SLEEP = "/cpus/idle-states/cluster-sleep";
                A53_0 = "/cpus/cpu@0";
                A53_1 = "/cpus/cpu@1";
                A53_2 = "/cpus/cpu@2";
                A53_3 = "/cpus/cpu@3";
                A53_L2 = "/cpus/l2-cache0";
                resmem = "/reserved-memory";
                rpmsg_reserved = "/reserved-memory/rpmsg@0xb8000000";
                gic = "/interrupt-controller@38800000";
                ckil = "/clocks/clock@0";
                osc_25m = "/clocks/clock@1";
                osc_27m = "/clocks/clock@2";
                clk_ext1 = "/clocks/clock@3";
                clk_ext2 = "/clocks/clock@4";
                clk_ext3 = "/clocks/clock@5";
                clk_ext4 = "/clocks/clock@6";
                mipi_pd = "/gpc_power_domain@0";
                pcie0_pd = "/gpc_power_domain@1";
                usb_otg1_pd = "/gpc_power_domain@2";
                usb_otg2_pd = "/gpc_power_domain@3";
                gpu_pd = "/gpc_power_domain@4";
                vpu_pd = "/gpc_power_domain@5";
                mipi_csi1_pd = "/gpc_power_domain@8";
                mipi_csi2_pd = "/gpc_power_domain@9";
                pcie1_pd = "/gpc_power_domain@10";
                pwm1 = "/pwm@30660000";
                pwm2 = "/pwm@30670000";
                pwm3 = "/pwm@30680000";
                pwm4 = "/pwm@30690000";
                gpio1 = "/gpio@30200000";
                gpio2 = "/gpio@30210000";
                gpio3 = "/gpio@30220000";
                gpio4 = "/gpio@30230000";
                gpio5 = "/gpio@30240000";
                tmu = "/tmu@30260000";
                throttle_devfreq = "/tmu@30260000/throttle-cfgs/devfreq";
                thermal_zones = "/thermal-zones";
                cpu_thermal = "/thermal-zones/cpu-thermal";
                cpu_alert0 = "/thermal-zones/cpu-thermal/trips/trip0";
                cpu_alert1 = "/thermal-zones/cpu-thermal/trips/trip1";
                cpu_alert2 = "/thermal-zones/cpu-thermal/trips/trip2";
                cpu_crit0 = "/thermal-zones/cpu-thermal/trips/trip3";
                fan_toggle0 = "/thermal-zones/cpu-thermal/trips/trip4";
                gpt1 = "/gpt@302d0000";
                irqsteer_dcss = "/irqsteer@32e2d000";
                csi1_bridge = "/csi1_bridge@30a90000";
                csi1_ep = "/csi1_bridge@30a90000/port/endpoint";
                csi2_bridge = "/csi2_bridge@30b80000";
                mipi_csi_1 = "/mipi_csi1@30a70000";
                mipi1_sensor_ep = "/mipi_csi1@30a70000/port/endpoint1";
                csi1_mipi_ep = "/mipi_csi1@30a70000/port/endpoint2";
                mipi_csi_2 = "/mipi_csi2@30b60000";
                dcss = "/dcss@0x32e00000";
                dcss_disp0 = "/dcss@0x32e00000/port@0";
                dcss_disp0_hdmi = "/dcss@0x32e00000/port@0/hdmi-endpoint";
                dcss_disp0_mipi_dsi = "/dcss@0x32e00000/port@0/mipi_dsi";
                hdmi = "/hdmi@32c00000";
                hdmi_disp = "/hdmi@32c00000/port@0/endpoint";
                lcdif = "/lcdif@30320000";
                mipi_dsi_phy = "/dsi_phy@30A00300";
                mipi_dsi_bridge = "/mipi_dsi_bridge@30A00000";
                mipi_dsi_bridge_in = "/mipi_dsi_bridge@30A00000/port@0/endpoint";
                panel1_in = "/mipi_dsi_bridge@30A00000/panel@0/port/endpoint";
                mipi_dsi_bridge_out = "/mipi_dsi_bridge@30A00000/port@1/endpoint";
                mipi_dsi = "/mipi_dsi@30A00000";
                mipi_dsi_out = "/mipi_dsi@30A00000/port@0/endpoint";
                mipi_dsi_in = "/mipi_dsi@30A00000/port@1/endpoint";
                iomuxc = "/iomuxc@30330000";
                pinctrl_hog = "/iomuxc@30330000/imx8mq-evk/hoggrp";
                pinctrl_fec1 = "/iomuxc@30330000/imx8mq-evk/fec1grp";
                pinctrl_i2c1 = "/iomuxc@30330000/imx8mq-evk/i2c1grp";
                pinctrl_i2c2 = "/iomuxc@30330000/imx8mq-evk/i2c2grp";
                pinctrl_i2c3 = "/iomuxc@30330000/imx8mq-evk/i2c3grp";
                pinctrl_dvfs = "/iomuxc@30330000/imx8mq-evk/dvfsgrp";
                pinctrl_typec = "/iomuxc@30330000/imx8mq-evk/typecgrp";
                pinctrl_uart1 = "/iomuxc@30330000/imx8mq-evk/uart1grp";
                pinctrl_uart2 = "/iomuxc@30330000/imx8mq-evk/uart2grp";
                pinctrl_uart3 = "/iomuxc@30330000/imx8mq-evk/uart3grp";
                pinctrl_usdhc1 = "/iomuxc@30330000/imx8mq-evk/usdhc1grp";
                pinctrl_usdhc1_100mhz = "/iomuxc@30330000/imx8mq-evk/usdhc1grp100mhz";
                pinctrl_usdhc1_200mhz = "/iomuxc@30330000/imx8mq-evk/usdhc1grp200mhz";
                pinctrl_usdhc2_gpio = "/iomuxc@30330000/imx8mq-evk/usdhc2grpgpio";
                pinctrl_usdhc2 = "/iomuxc@30330000/imx8mq-evk/usdhc2grp";
                pinctrl_usdhc2_100mhz = "/iomuxc@30330000/imx8mq-evk/usdhc2grp100mhz";
                pinctrl_usdhc2_200mhz = "/iomuxc@30330000/imx8mq-evk/usdhc2grp200mhz";
                pinctrl_wdog = "/iomuxc@30330000/imx8mq-evk/wdoggrp";
                pinctrl_pmic = "/iomuxc@30330000/imx8mq-evk/pmicirq";
                pinctrl_mipi_dsi_en = "/iomuxc@30330000/imx8mq-evk/mipi_dsi_en";
                pinctrl_pcie0 = "/iomuxc@30330000/imx8mq-phanbell/pcie0grp";
                pinctrl_pcie1 = "/iomuxc@30330000/imx8mq-phanbell/pcie1grp";
                pinctrl_sai2 = "/iomuxc@30330000/imx8mq-phanbell/sai2grp";
                pinctrl_gpio = "/iomuxc@30330000/imx8mq-phanbell/gpio_ctrlgrp";
                pinctrl_pwm1 = "/iomuxc@30330000/imx8mq-phanbell/pwm1";
                pinctrl_pwm2 = "/iomuxc@30330000/imx8mq-phanbell/pwm2";
                pinctrl_pwm3 = "/iomuxc@30330000/imx8mq-phanbell/pwm3";
                pinctrl_ecspi1 = "/iomuxc@30330000/imx8mq-phanbell/ecspi1grp";
                pinctrl_ecspi1_cs = "/iomuxc@30330000/imx8mq-phanbell/ecspi1_cs_grp";
                pinctrl_usb5744 = "/iomuxc@30330000/imx8mq-phanbell/usb5744";
                pinctrl_i2c_mux = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_mux_grp";
                pinctrl_akira01 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira01_grp";
                pinctrl_akira23 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira23_grp";
                pinctrl_akira45 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira45_grp";
                pinctrl_akira67 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira67_grp";
                pinctrl_csi1 = "/iomuxc@30330000/imx8mq-phanbell/csi1grp";
                gpr = "/iomuxc-gpr@30340000";
                ocotp = "/ocotp-ctrl@30350000";
                anatop = "/anatop@30360000";
                snvs = "/snvs@30370000";
                snvs_rtc = "/snvs@30370000/snvs-rtc-lp";
                snvs_pwrkey = "/snvs@30370000/snvs-powerkey";
                clk = "/ccm@30380000";
                src = "/src@30390000";
                gpc = "/gpc@303a0000";
                system_counter = "/system-counter@3036a0000";
                spdif1 = "/spdif@30810000";
                ecspi1 = "/ecspi@30820000";
                ecspi2 = "/ecspi@30830000";
                ecspi3 = "/ecspi@30840000";
                uart1 = "/serial@30860000";
                uart3 = "/serial@30880000";
                uart2 = "/serial@30890000";
                spdif2 = "/spdif@308a0000";
                uart4 = "/serial@30a60000";
                mu = "/mu@30aa0000";
                usb3_phy0 = "/phy@381f0040";
                usb3_0 = "/usb@38100000";
                usb_dwc3_0 = "/usb@38100000/dwc3";
                usb3_phy1 = "/phy@382f0040";
                usb3_1 = "/usb@38200000";
                usb_dwc3_1 = "/usb@38200000/dwc3";
                usdhc1 = "/usdhc@30b40000";
                usdhc2 = "/usdhc@30b50000";
                sai1 = "/sai@30010000";
                sai6 = "/sai@30030000";
                sai5 = "/sai@30040000";
                sai4 = "/sai@30050000";
                sai2 = "/sai@308b0000";
                sai3 = "/sai@308c0000";
                sdma1 = "/sdma@30bd0000";
                sdma2 = "/sdma@302c0000";
                fec1 = "/ethernet@30be0000";
                ethphy0 = "/ethernet@30be0000/mdio/ethernet-phy@0";
                gpu = "/gpu@38000000";
                imx_ion = "/imx_ion";
                i2c1 = "/i2c@30a20000";
                pmic = "/i2c@30a20000/bd71837@4b";
                buck1_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@0";
                buck2_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@1";
                buck3_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@2";
                buck4_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@3";
                buck5_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@4";
                buck6_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@5";
                buck7_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@6";
                buck8_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@7";
                ldo1_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@8";
                ldo2_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@9";
                ldo3_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@10";
                ldo4_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@11";
                ldo5_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@12";
                ldo6_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@13";
                ldo7_reg = "/i2c@30a20000/bd71837@4b/regulators/regulator@14";
                i2c2 = "/i2c@30a30000";
                ov5645_mipi = "/i2c@30a30000/ov5645_mipi@3c";
                ov5645_mipi1_ep = "/i2c@30a30000/ov5645_mipi@3c/port/endpoint";
                led_driver = "/i2c@30a30000/ktd2027@30";
                i2c3 = "/i2c@30a40000";
                rt5645 = "/i2c@30a40000/rt5645@1a";
                typec_ptn5150 = "/i2c@30a40000/ptn5150@3d";
                usb3_hub = "/i2c@30a40000/usb5744@2d";
                power_monitor_m2_a = "/i2c@30a40000/power_monitor_m2_a@40";
                power_monitor_m2_b = "/i2c@30a40000/power_monitor_m2_b@41";
                power_monitor_m2_c = "/i2c@30a40000/power_monitor_m2_c@4e";
                power_monitor_m2_d = "/i2c@30a40000/power_monitor_m2_d@45";
                power_monitor_som = "/i2c@30a40000/power_monitor_som@48";
                power_monitor_3P3 = "/i2c@30a40000/power_monitor_3P3@4c";
                power_monitor_1P8 = "/i2c@30a40000/power_monitor_1P8@4d";
                power_monitor_5P0 = "/i2c@30a40000/power_monitor_5P04f";
                led_driver_1 = "/i2c@30a40000/ktd2027@30";
                led_driver_2 = "/i2c@30a40000/ktd2027@31";
                akira0 = "/i2c@30a40000/i2c-mux@70/i2c@0/gpio@43";
                akira1 = "/i2c@30a40000/i2c-mux@70/i2c@0/gpio@44";
                akira2 = "/i2c@30a40000/i2c-mux@70/i2c@1/gpio@43";
                akira3 = "/i2c@30a40000/i2c-mux@70/i2c@1/gpio@44";
                akira4 = "/i2c@30a40000/i2c-mux@70/i2c@2/gpio@43";
                akira5 = "/i2c@30a40000/i2c-mux@70/i2c@2/gpio@44";
                akira6 = "/i2c@30a40000/i2c-mux@70/i2c@3/gpio@43";
                akira7 = "/i2c@30a40000/i2c-mux@70/i2c@3/gpio@44";
                i2c4 = "/i2c@30a50000";
                vpu = "/vpu@38300000";
                wdog1 = "/wdog@30280000";
                wdog2 = "/wdog@30290000";
                wdog3 = "/wdog@302a0000";
                dma_cap = "/dma_cap";
                qspi = "/qspi@30bb0000";
                pcie0 = "/pcie@0x33800000";
                pcie1 = "/pcie@0x33c00000";
                ddr_pmu0 = "/ddr_pmu@3d800000";
                imx_rpmsg = "/imx_rpmsg";
                rpmsg = "/imx_rpmsg/rpmsg";
                crypto = "/caam@30900000";
                sec_jr0 = "/caam@30900000/jr0@1000";
                sec_jr1 = "/caam@30900000/jr1@2000";
                sec_jr2 = "/caam@30900000/jr2@3000";
                caam_sm = "/caam-sm@00100000";
                caam_snvs = "/caam-snvs@30370000";
                irq_sec_vio = "/caam_secvio";
                dma_apbh = "/dma-apbh@33000000";
                gpmi = "/gpmi-nand@33002000";
                reg_usdhc2_vmmc = "/regulators/usdhc2_vmmc";
                reg_apex = "/regulators/apex_pmic";
                modem_reset = "/modem-reset";
                fan = "/gpio_fan";
                akira01_reset = "/akira01_reset";
                akira23_reset = "/akira23_reset";
                akira45_reset = "/akira45_reset";
                akira67_reset = "/akira67_reset";
        };
````

Op de Coral Google repository kunnen ook de bron `.dtsi` bestanden teruggevonden worden.

In onderstaande code blokken wordt nu gekeken waar in bovenste code blok naar verwezen wordt.

In */arch/arm64/boot/dts/freescale/fsl-imx8mq.dtsi* [[33]](bronnen.md) vinden we, met referentie:

> pinctrl_i2c_mux = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_mux_grp";
> pinctrl_akira01 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira01_grp";
> pinctrl_akira23 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira23_grp";
> pinctrl_akira45 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira45_grp";
> pinctrl_akira67 = "/iomuxc@30330000/imx8mq-phanbell/pinctrl_akira67_grp";

````bash
iomuxc: iomuxc@30330000 {
		compatible = "fsl,imx8mq-iomuxc";
		reg = <0x0 0x30330000 0x0 0x10000>;
	};
````

> pcie1 = "/pcie@0x33c00000";

````bash
pcie1: pcie@0x33c00000 {
		compatible = "fsl,imx8mq-pcie", "snps,dw-pcie";
		reg = <0x0 0x33c00000 0x0 0x400000>, <0x0 0x27f00000 0x0 0x80000>;
		reg-names = "dbi", "config";
		#address-cells = <3>;
		#size-cells = <2>;
		device_type = "pci";
		ranges =  <0x81000000 0 0x00000000 0x0 0x27f80000 0 0x00010000 /* downstream I/O 64KB */
			   0x82000000 0 0x20000000 0x0 0x20000000 0 0x07f00000>; /* non-prefetchable memory */
		num-lanes = <1>;
		interrupts = <GIC_SPI 74 IRQ_TYPE_LEVEL_HIGH>,
				<GIC_SPI 80 IRQ_TYPE_LEVEL_HIGH>; /* eDMA */
		interrupt-names = "msi";
		#interrupt-cells = <1>;
		interrupt-map-mask = <0 0 0 0x7>;
		interrupt-map = <0 0 0 1 &gic GIC_SPI 77 IRQ_TYPE_LEVEL_HIGH>,
				<0 0 0 2 &gic GIC_SPI 76 IRQ_TYPE_LEVEL_HIGH>,
				<0 0 0 3 &gic GIC_SPI 75 IRQ_TYPE_LEVEL_HIGH>,
				<0 0 0 4 &gic GIC_SPI 74 IRQ_TYPE_LEVEL_HIGH>;
		clocks = <&clk IMX8MQ_CLK_PCIE2_ROOT>,
			<&clk IMX8MQ_CLK_PCIE2_AUX_CG>,
			<&clk IMX8MQ_CLK_PCIE2_PHY_CG>;
		clock-names = "pcie", "pcie_bus", "pcie_phy";
		fsl,max-link-speed = <2>;
		ctrl-id = <1>;
		power-domains = <&pcie1_pd>;
		status = "disabled";
	};
````

> akira0 = "/i2c@30a40000/i2c-mux@70/i2c@0/gpio@43";

````bash
i2c3: i2c@30a40000 {
		#address-cells = <1>;
		#size-cells = <0>;
		compatible = "fsl,imx21-i2c";
		reg = <0x0 0x30a40000 0x0 0x10000>;
		interrupts = <GIC_SPI 37 IRQ_TYPE_LEVEL_HIGH>;
		clocks = <&clk IMX8MQ_CLK_I2C3_ROOT>;
		status = "disabled";
	};
````

In *arch/arm64/boot/dts/freescale/fsl-imx8mq-som.dtsi* [[34]](bronnen.md):

````bash
...

&i2c3 {
	clock-frequency = <100000>;
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_i2c3>;
	status = "okay";
};

...

&iomuxc {
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_hog>;

	...

	imx8mq-phanbell {
		pinctrl_pcie0: pcie0grp {
			fsl,pins = <
				MX8MQ_IOMUXC_I2C4_SCL_PCIE1_CLKREQ_B        0x76 /* open drain, pull up */
				MX8MQ_IOMUXC_NAND_DATA05_GPIO3_IO11     0x16 /* Disable - wl_regon */
				MX8MQ_IOMUXC_NAND_DATA04_GPIO3_IO10     0x16 /* Reset - wl_nreset */
				MX8MQ_IOMUXC_GPIO1_IO00_ANAMIX_REF_CLK_32K      0x05
			>;
		};
		pinctrl_pcie1: pcie1grp {
		 fsl,pins = <
			 MX8MQ_IOMUXC_I2C4_SDA_PCIE2_CLKREQ_B	0x76 /* open drain, pull up */
			 MX8MQ_IOMUXC_NAND_WP_B_GPIO3_IO18       0x19 /* APEX_SYS_RST_L */
		 >;
		};
	};
};
&pcie0 {
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_pcie0>;
	disable-gpio = <&gpio3 11 GPIO_ACTIVE_LOW>;
	reset-gpio = <&gpio3 10 GPIO_ACTIVE_LOW>;
	vph-supply = <&ldo7_reg>;
	fsl,max-link-speed = <1>;
	status = "okay";
};
&pcie1 {
	pinctrl-names = "default";
	pinctrl-0 = <&pinctrl_pcie1>;
	reset-gpio = <&gpio3 18 GPIO_ACTIVE_LOW>;
	ext_osc = <1>;
	vph-supply = <&ldo7_reg>;
	hard-wired = <1>;
	status = "okay";
};

...
````

---

## Device Tree Overlays toevoegen

In de Coral documentatie is een pagina [[35]](bronnen.md) voorzien over hoe eigen overlays toe te voegen. Hierdoor is de werking hiervan binnen Mendel duidelijker.

Uit de gegeven device tree source code kan ook bevestigd worden dat `imx8mq-phanbell` de huidige top laag is.

````bash
// Set CMA region to 512M
/dts-v1/;
/plugin/;

/ {
    compatible = "fsl,imx8mq-phanbell";

    fragment@0 {
        target-path = "/";
        __overlay__ {
            reserved-memory {
                linux,cma {
                    size = <0 0x20000000>;
                };
            };
        };
    };
};
````

De `/boot/overlays.txt` bevat in de onderzochte versie echter niets.

````bash
mendel@undefined-yarn:~$ cat /boot/overlays.txt
# List of device tree overlays to load. Format: overlay=<dtbo name, no extenstion> <dtbo2> ...
overlay=
````
