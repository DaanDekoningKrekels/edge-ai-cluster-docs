# Debian voor Edge TPU

In dit onderdeel wordt er bekeken wat er in een Debian moet worden aangepast om op een Coral Dev Board te kunnen draaien.

## Device Tree Overlays van Mendel

De Coral Dev Board bestaat uit een baseboard met daarop een op maat ontwikkelde SoM op basis van een NXP SoC.

De Device Tree Overlays van Mendel die de hardware van de Coral Dev Board beschrijven kunnen gebruikt worden zodat het OS over de juiste beschrijvingen beschikt.

## Packages

Uit de *Mendel release notes* [[3]](bronnen.md) kan volgende lijst samengesteld worden:
- python3-pycoral
- libedgetpu1-std
- python3-edgetpuvision

De *mendel-minimal* package [[4]](bronnen.md) geeft ook enkele dependencies aan:
- e2fsprogs(>=1.43)
- adduser
- sudo
- openssh-server
- locales
- avahi-daemon
- bluez
- network-manager
- passwd
- python2.7 (ondertussen vervangen door python3.6-3.9)
- runonce

## PCIe Edge TPU

Het Coral Dev Board werkt met een TPU op PCIe. Dit is afgeleid uit de *M.2 or Mini PCIe Accelerator* documentatie van Coral zelf [[36]](bronnen.md). Hierin staat verder ook beschreven hoe je zo'n module installeert, onder andere op een Linux systeem. Hetzelfde kan dus gedaan worden om Debian aan te passen zodat het met de TPU op de Coral Dev Board kan werken. Volgende komt recht uit de vernoemde documentatie, waar ook het installatie proces teruggevonden kan worden.

### Onderdelen

- Versie van de Debian 10 (buster) Linux distributie en ARMv8 systeem architectuur.
- Python 3.6 - 3.9
- Coral ("Apex") PCIe driver
- Edge TPU runtime
- PyCoral library
