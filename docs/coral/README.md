# Coral Dev Board

## Wat is een Coral Dev Board?

Het Coral Dev Board is een *Single Board Computer*, of SBC, die een Edge TPU coprocessor bevat. Het is ideaal voor het maken van prototypes voor nieuwe projecten waarbij snelle on-device inferencing voor machine learning modellen nodig is.

[![](./assets/coral-dev-board.png 'Fig. 1: Coral Dev Board. [2]')](./bronnen.md#coral-dev-board)

Een Coral Dev Board bestaat uit twee onderdelen:
- Edge TPU System-on-Module (SoM)
- Baseboard met connectoren, wat het tot een *Single Board Computer* maakt.

---

## Edge TPU System-on-Module (SoM)

De Edge TPU System-on-Module is een volledig geïntegreerd systeem.
Het bevat:
- NXP i.MX 8M SoC (MIMX8MQ6DVA)
  - Cortex-A53/M4F
  - Vivante GC7000 Lite GPU en VPU (decode + HDR10)
- Google Edge TPU (ASIC) ML accelerator coprocessor (PCIe2)
- Cryptographic coprocessor
- Wi-Fi 2x2 MIMO (802.11b/g/n/ac 2.4/5 GHz) (PCIe1)
- Bluetooth 4.2
- 8/16 GB eMMC
- 1/4 GB LPDDR4

[![](./assets/som-inhand.jpg 'Fig 2: SoM in de hand foto. [4]')](bronnen.md#coral-dev-board)

[![](./assets/som-dimensions-front-back.png 'Fig 3: SoM dimensies en detail foto. [4]')](bronnen.md#coral-dev-board)

[![](./assets/som-powerdraw.png 'Fig 4: SoM vermogenverbruik diagram per onderdeel. [4]')](bronnen.md#coral-dev-board)

[![](./assets/som-dimensions-basic.png 'Fig 5: SoM dimensies diagram. [3]')](bronnen.md#coral-dev-board)

[![](./assets/som-blockdiagram.png 'Fig 6: SoM blockdiagram. [3]')](bronnen.md#coral-dev-board)

### NXP i.MX 8M SoC (MIMX8MQ6DVA)

Als kern en basis is NXP's i.MX 8M System-on-Chip (SoC) gekozen. Hiermee zijn alle andere onderdelen verbonden.
Deze SoC bevat zowel een Quad-core Arm Cortex-A53 microcontroller, als een Cortex-M4F, en een Vivante GC7000 Lite GPU en VPU voor decode en HDR10.

MIMX8MQ6DVA staat voor [[6]](bronnen.md#coral-dev-board):
- M = Mass Production
- IMX8M = onderdeel serie naam
- Q = Quad core
- 6 = GPU en VPU (decode + HDR10)
- D = voor Consumenten, temp range 0 tot +95°C
- VA = ROHS 17 x 17 mm, 0.65 mm pitch, FCBGA bare die

Voor meer informatie zie de officiële website en documentatie [[6]](bronnen.md#coral-dev-board).

### Google Edge TPU ML accelerator coprocessor

#### Tensor Processing Unit (TPU)

Een Tensor Processing Unit, of TPU, is een ASIC ontwikkeld door Google. Het wordt gebruikt voor machine learning applicaties zoals neurale netwerken. De chip is ontworpen om Google's eigen TensorFlow software op te laten draaien.

In tegensteling tot een CPU die zijn berekeningen één voor één gaat uitvoeren, gaat een TPU, net zoals een GPU, gespecialiseerde berekeningen parallelliseren. Dit wilt zeggen dat meerdere berekeningen tegelijkertijd kunnen plaatsvinden. Ze zijn ontworpen om een hoog volume aan lageprecisie-berekeningen te verwerken met meer invoer/uitvoer bewerkingen per verbruikte eenheid energie. TPU's zijn zeer geschikt voor convolutionele neurale netwerken, terwijl dat GPU's voordeel hebben bij sommige volledig verbonden neurale netwerken.

#### Google Edge TPU ML accelerator

Het voorvoegsel *Edge* in *Edge TPU* geeft aan dat een AI model, hier meer precies een TensorFlow Lite model, lokaal op de hardware kan draaien zonder off-loading naar de cloud. Dit reduceert de *latency*, verbeterd de data privacy, en vereist geen constante internet verbinding.

Op de Coral Dev Board zit deze geïntegreerd op de SoM samen met de andere onderdelen. Om deze beter op zich te kunnen bekijken kan er gekeken worden naar de Coral Mini PCIe Accelerator [[7]](bronnen.md#coral-dev-board) en Coral Accelerator Module [[8]](bronnen.md#coral-dev-board), waarvan de architectuur nagenoeg identiek is.

[![](./assets/mini-pcie-accelerator-dimensions-front-back.png 'Fig 7: Coral Mini PCIe Accelerator dimensies en detail foto. [7]')](bronnen.md#coral-dev-board)

[![](./assets/mini-pcie-accelerator-side-dimensions-diagram.png 'Fig 8: Coral Mini PCIe Accelerator dimensies diagram. [7]')](bronnen.md#coral-dev-board)

[![](./assets/coral-accelerator-module-dimensions-front-back.png 'Fig 9: Coral Accelerator Module detail foto. [8]')](bronnen.md#coral-dev-board)

##### Kenmerken

- 4 TOPS totale peak performance (int8), gelijk aan 4 biljoen (trillion) operaties per seconden.
- Energieverbruik van 2 TOPS per watt.
- Geïntegreerde power management (PMIC* ISL9130B)
- Ontworpen voor PCIe Gen2 x1 interface

_*PMIC = Power Manager Integrated Circuit_

[![](./assets/coral-accelerator-module-blockdiagram.png 'Fig 10: Coral Accelerator Module functioneel blockdiagram. [8]')](bronnen.md#coral-dev-board)

### Verbindingen

De afzonderlijke onderdelen, zoals te zien op *Fig 6: SoM blockdiagram*, zijn als volgt verbonden met de SoC:
- PCIe1 ↔ Wi-Fi 2x2 MIMO en Bluetooth 4.2
- PCIe2 ↔ Google Edge TPU (ASIC) ML accelerator coprocessor
- POWER en CONTROL ↔ PMIC* BD71837MWV
- SDIO ↔ 8/16 GB eMMC
- DRAM ↔ 1/4 GB LPDDR4

_*PMIC = Power Manager Integrated Circuit_

De verbinding tussen SoM en het baseboard gebeurt via drie DF40C-100DP-0.4V(51) 100-pin connectoren.

[![](./assets/baseboard-connectors-and-component-restrictions.png 'Fig 11: SoM connector op het baseboard. [4]')](bronnen.md#coral-dev-board)

[![](./assets/som-connector-schematic.png 'Fig 12: SoM connector schema. [4]')](bronnen.md#coral-dev-board)


---

## Baseboard

Het baseboard van het Coral Dev Board is hetgeen het geheel tot een *Single Board Computer* (SBC) maakt.

[![](./assets/devboard-inhand.jpg 'Fig. 13: Coral Dev Board in de hand foto. [1]')](./bronnen.md#coral-dev-board)

[![](./assets/devboard-dimensions-front-back.png 'Fig. 14: Coral Dev Board dimensies en detail foto. [3]')](./bronnen.md#coral-dev-board)

[![](./assets/devboard-dimensions-illi.png 'Fig 15: Coral Dev Board dimensies diagram. [3]')](bronnen.md#coral-dev-board)

[![](./assets/diagram-baseboard_bw.png 'Fig 16: Baseboard blockdiagram. [3]')](bronnen.md#coral-dev-board)

### Connectiviteit

Het Coral Dev Board is ontwikkeld als *prototype board* voor ontwikkelaars die de SoM of Edge TPU accelerators willen integreren in eigen hardware. Hiervoor brengt het Baseboard een hele hoop rand-, of *peripheral*, connectiviteit naar buiten, als ook stroomvoorzieningsaansluitingen, zoals te zien in *Fig 17: Baseboard connectiviteits diagram*.

[![](./assets/devboard-port-callouts-illi-named.png 'Fig 17: Baseboard connectiviteits diagram. [3]')](bronnen.md#coral-dev-board)

- USB connections
  - USB Type-C power port (5 V DC)
  - USB 3.0 Type-C OTG port
  - USB 3.0 Type-A host port
  - USB 2.0 Micro-B serial console port
- Audio connections
  - 3.5 mm audio jack (CTIA compliant)
  - Digital PDM microphone (x2)
  - 2.54 mm 4-pin terminal for stereo speakers
- Video connections
  - HDMI 2.0a (full size)
  - 39-pin FFC connector for MIPI DSI display (4-lane)
  - 24-pin FFC connector for MIPI CSI-2 camera (4-lane)
- MicroSD card slot
- Gigabit Ethernet RJ45 port
- 40-pin GPIO expansion header

<br>

[![](./assets/devboard-gpio-header.jpg 'Fig 18: Baseboard GPIO header foto met functies. [3]')](bronnen.md#coral-dev-board)

Het volledige [baseboard schema](./assets/coral-dev-board-baseboard-schematic.pdf) is ook beschikbaar in de *google-coral/electricals* repository [[5]](bronnen.md#coral-dev-board), link via [[2]](bronnen.md#coral-dev-board).
