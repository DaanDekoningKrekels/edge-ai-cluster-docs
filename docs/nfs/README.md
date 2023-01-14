## NFS setup

>Hoe we NFS eerst wilde instellen. 
>Hoe we het uiteindelijk hebben ingesteld en alle problemen die we daarbij hadden.

Een Network File System is een manier om meerdere machines data gemakkelijk te delen met elkaar. Er is een centrale administratie voorzien. 

--> Afbeelding NFS

Er is een client-side file systeem en een server-side file systeem. De master node zal in ons geval de server zijn en de andere nodes zijn de clients. Een client kan dan een system call doen om bijvoorbeeld files op te roepen van de server. 

Hoe zet ik een NFS server op:
 - Eerst moeten we de NFS server package installeren met volgend commando: 
 ``` bash 
 sudo apt install nfs-kernel-server
 ```
 - Daarna zorgen we ervoor dat de NFS service runt en automatisch opstart bij het starten van de node.
 ``` bash 
 sudo systemctl enable --now nfs-server
 ```
 - Hebben we nog geen directory aangemaakt waaruit we de files willen delen dan maken we die even aan
 ``` bash 
 sudo mkdir -p /media/nfs
 ```
- We passen de /etc/exports file aan. Hierin kunenn we beslissen welke directories we willen delen en we hier toegang tot heeft. Indien gewenst kan men ook een limiet instellen op het aantal keer men kan delen. 
``` bash 
 sudo nano /etc/exports
 ```
- In deze file krijgt elke node zijn eigen lijn. Deze begint met de locatie van de node. Dit kan aan de hand van het IP adres. Hierachter plaatst men de regels die men aan de node wilt toevoegen. Een voorbeeld van hoe dit er dan kan uitzien:
``` bash 
 /media/nfs             192.168.1.1/24(rw,sync,no_subtree_check)
 ```
- Enkele regels die men kan instellen
--> Afbeelding

- We slagen de file op en executen dit om er voor te zorgen dat de aanpassingen zijn opgeslagen. 
--> Afbeelding 

Hoe connecteer ik met de NFS server vanaf mijn client
- De NFS packages worden geïnstalleerd
``` bash 
 sudo apt install nfs-common
 ```
- De NFS file wordt gemount
``` bash 
 sudo mount -t nfs4 192.168.1.110:/media/nfs /media/share
 ```

Errors 

--> Screenshots

> 1. https://nfs.sourceforge.net/nfs-howto/ar01s07.html#nfsd_wont_start
> 2. https://nfs-ganesha.github.io/
> 3. https://objectivefs.com/howto/how-to-set-up-nfs-ganesha
> 4. https://objectivefs.com/howto/how-to-set-up-nfs-ganesha
> 5. https://github.com/nfs-ganesha/nfs-ganesha/blob/next/src/config_samples/export.txt
> 6. https://cloudnull.io/2017/05/nfs-mount-via-systemd/

