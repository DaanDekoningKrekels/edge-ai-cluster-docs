# Conclusie


De Coral Dev Board zijn speciaal ontworpen voor het snel maken van prototypes en proof-of-concepts met een SOM TPU. Daarom vallen de Dev Boards binnen de "Prototyping products" categorie in de catalogus van Coral. De SOM op de Dev Boards is ook los verkrijgbaar, deze valt binnen de "Production products", samen met verschillende TPU's die onder andere verkocht worden om via M.2 of PCIe te gebruiken.

Deze andere modules bevatten enkel de Edge TPU's en niet de extra componenten die mee op de SOM vervat zitten. Er is softwarematig ook reeds ondersteuning voor het gebruiken van meerdere Edge TPU's die verbonden zijn met hetzelfde systeem. Er bestaan PCIe kaarten die op een moederbord kunnen geplaatst worden, zoals een grafische kaart, waar meerdere Edge TPU's op passen.

De [Delock PCI Express x16 Karte zu 4 x intern NVMe M.2 Key M]() kaart biedt de mogelijkheid om 4 M.2 key B+M kaarten aan te sluiten op een computer. Dit zou compatibel zijn met de [Edge TPU M.2 Accelerator B+M key](https://coral.ai/products/m2-accelerator-bm/).

De [ASUS AI Accelerator PCIe Card](https://iot.asus.com/products/AI-accelerator/AI-Accelerator-PCIe-Card/) is speciaal ontwikkeld om meerdere Edge TPU's op één systeem aan te sluiten. Bij dit product kunnen er 8 [M.2 Accelerator with Dual Edge TPU](https://coral.ai/products/m2-accelerator-dual-edgetpu/) modules worden aangesloten. Dit biedt de mogelijkheid tot een equivalente rekenkracht van 16 Dev Boards in één compacte computerbehuizing.

![ASUS AI acceleator](assets/pcie-tpus.png 'ASUS AI Accelerator blokdiagram')

Om dit verschil in perspectief te zetten is er volgende financiële schatting. Gebaseerd op een equivalente rekenkracht. Er is gekozen voor een 

## Delock 4x PCIe

| **Product**                                                  | **Prijs** |
| ------------------------------------------------------------ | --------- |
| 1 x CPU: AMD Ryzen 7 5700G, 3,8 GHz (4,6 GHz Turbo Boost) socket AM4 processor (Productcode: 1758475) | € 219,90  |
| 1 x Moederbord: MSI B550 GAMING GEN3, socket AM4 moederbord (Productcode: 1868790) | € 124,90  |
| 1 x CPU-koeler: be quiet! Dark Rock 4 cpu-koeler (Productcode: 1441525) | € 79,90   |
| 1 x Geheugen: G.Skill 32 GB DDR4-3200 kit werkgeheugen (Productcode: 1584069) | € 77,90   |
| 1 x SSD: WD Blue SN570 250 GB SSD (Productcode: 1797439)       | € 34,99   |
| 1 x Behuizing: Sharkoon PURE STEEL, Tower-behuizing (Productcode: 1470853) | € 49,99   |
| 1 x Voeding: Sharkoon WPM Gold ZERO 750W voeding (Productcode: 1467183) | € 87,90   |
| 1 x [Delock PCI Express x16 Karte zu 4 x intern NVMe M.2 Key M - Bifurcation](https://www.allekabels.be/delock/6335/3822788/delock-pci-express-x16-karte-zu-4-x-intern-nvme-m.2-key-m-bifurcation.html) |  € 119,99         |
| 4 x [Edge TPU: M.2 Accelerator B+M key](https://coral.ai/products/m2-accelerator-bm/) |  € 100         |
| **Totaal** | € 895.47 |

Bovenstaande componenten zijn gekozen met de prijzen van 25/01/2023 en de productcodes kunnen ter referentie gebruikt worden bij Alternate.be.

+ Dev Board = € 134,24 x 5 = € 671,20
+ [Mikrotik hAP ac lite](https://www.dustin.be/nl/wlan-access-points/mikrotik/hap-ac-lite-tower-art-rb952ui-5ac2nd-tc-num-6166125/) = € 62
+ [Anker PowerPort Speed ](https://www.coolblue.be/nl/product/779803/anker-powerport-speed-oplader-5-usb-poorten-18w-quick-charge-3-0-zwart.html) = € 40
+ **Totaal** = € 737,20

Zoals te zien in de vergelijking is de gekozen configuratie voor de normale computer niet iets duurder. Echter is deze configuratie ook vele malen performanter en is er al standaard ondersteuning voor het laten samenwerken van de TPU's in de Python en C++ TensorFlow software. Deze configuratie kan opgeschaald worden tot maximaal 2 Delock PCI Express kaarten en 8 TPU's. 


## ASUS AI Accelerator PCIe Card

| **Product**                                                  | **Prijs** |
| ------------------------------------------------------------ | --------- |
| 1 x CPU: AMD Ryzen 7 5700G, 3,8 GHz (4,6 GHz Turbo Boost) socket AM4 processor (Productcode: 1758475) | € 219,90  |
| 1 x Moederbord: MSI B550 GAMING GEN3, socket AM4 moederbord (Productcode: 1868790) | € 124,90  |
| 1 x CPU-koeler: be quiet! Dark Rock 4 cpu-koeler (Productcode: 1441525) | € 79,90   |
| 1 x Geheugen: G.Skill 32 GB DDR4-3200 kit werkgeheugen (Productcode: 1584069) | € 77,90   |
| 1 x SSD: WD Blue SN570 250 GB SSD (Productcode: 1797439)       | € 34,99   |
| 1 x Behuizing: Sharkoon PURE STEEL, Tower-behuizing (Productcode: 1470853) | € 49,99   |
| 1 x Voeding: Sharkoon WPM Gold ZERO 750W voeding (Productcode: 1467183) | € 87,90   |
| 1 x [ASUS AI Accelerator PCIe Card inclusief TPU's](https://www.amazon.com/Accelerator-Based-Google-Inference-CRL-G18U-P3DF/dp/B08TZRX2WP) |  € 1300         |
| **Totaal** | € 1975.48 |

+ Dev Board = € 134,24 x 17 = € 2282,08
+ [Mikrotik hAP ac lite](https://www.dustin.be/nl/wlan-access-points/mikrotik/hap-ac-lite-tower-art-rb952ui-5ac2nd-tc-num-6166125/) = € 62 x 2 = € 124
+ [Anker PowerPort Speed ](https://www.coolblue.be/nl/product/779803/anker-powerport-speed-oplader-5-usb-poorten-18w-quick-charge-3-0-zwart.html) = € 40 x 2 = 80
+ **Totaal** = € 2486,08

Bij deze situatie is het financiële schaalvoordeel duidelijk zichtbaar. Omdat de modules op het ASUS-bord 2 TPU-kernen bevatten is dit een totaal van 16 TPU's. Ook op vlak van softwareondersteuning geldt hetzelfde als bij de vorige configuratie.
