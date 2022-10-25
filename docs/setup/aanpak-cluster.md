# Aanpak cluster

In hat algemeen zijn er twee mogleijke opties om de Coral Dev boards in te stellen voor een Beowulf cluster. Er bestaan linux distributies die speciaal gemaakt zijn om te gebruiken in een Beowulf cluster. We kunnen  deze linux distributies compileren om te draaien op de Coral Dev boards en zo een cluster opzetten. Een andere mogleijkheid is om na te gaan welke softwate zoal gebruikt wordt in Beowulf clusters en deze software los installeren op Mendel Linux.

## Cluster Linux Aanpassen

 Een Linux distributie aanpassen die gemaakt is voor Linux is best een grote karwij. Standaard draait een Coral Dev board op Mendel Linux. Google raadt het ook af om Mendel zelf te compileren en het draaien van een andere distributie zal waarschijnlijk ook voor onnodige problemen zorgen, Mendel is echter al speciaal gemaakt om goed te werken met de TPU.

> Note: we generally discourage this, as we put a great deal of effort  into making sure our releases run well on the boards we target. - [What is Mendel Linux?](https://coral.googlesource.com/docs/+/refs/heads/master/ReadMe.md)

Het is wel interessant om eens te bekijken wat de verschillen zij tussen Mendel Linux en de standaard Debian waar Mendel op is gebaseerd.

Mendel Linux is grotendeels compatibel met de normale Debian. Een aantal bodige aanpassingen zijn verricht zodat de Coral Dev Boards goed draaien.

- Mogelijkheid om op te starten vanaf eMMC opslag.
- Ondersteuning voor de TPU en andere hardware op het bordje.

## Software voor een Beowulf Cluster  

Een Beowulf cluster maakt gebruik van de Message Passing Interface (MPI), dat is de manier waarop de master met verschillende nodes kan communiceren via SSH. Een bekende implementatie van MPI is  Open MPI maar er zijn ook andere alternatieven.

Er moet ook een gedeelde netwerkmap zijn die opgeszet wordt via het Network File System (NFS) op de master.

### Vereenvoudigde software lijst

- Message Passing Interface implementatie
  - [Open MPI](https://www.open-mpi.org/)
  - [MPICH](https://www.mpich.org/)
  - [OpenMP](https://www.openmp.org/)
- SSH server
- Network File System

