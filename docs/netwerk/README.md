## Netwerk setup

>Hoe we het netwerk hadden ingesteld en wat de verschillende instellingen waren.

> 1. https://help.mikrotik.com/docs/display/UM/hAP+ac+lite
> 2. https://wiki.mikrotik.com/wiki/Manual:Winbox
> 3. https://sanisimov.com/2019/02/connect-mikrotik-router-to-a-wifi-access-point-and-serve-internet-via-ethernet-and-wifi/


### Router

De kern van het netwerk is een MikroTik router. Deze ziet er simpel uit maar er kan hier operationeel veel in worden aangepast. Dit geeft de kans om een netwerk op te zetten waar de cluster geïsoleerd kan zijn van de buitenwereld, maar tegelijkertijd ook verbinding heeft met het internet.   


#### IP adres toekennen

Via router software hebben we een vast IP ingesteld dat verbonden is met het MAC-adres.

`192.168.88.11` tot `192.168.88.15`

#### Netwerk instellingen

De MikroTik router is *dual-band*, daardoor kunnen we gebruik maken van één band om een netwerk voor ons op te zetten en de andere band om te verbinden met wifi van de school voor internet. Het wifinetwerk is vernoemd naar "TPU_Friends" en de andere WiFi-band zijn is verbonden met Blechley. Blechley is gekozen omdat er dan geen nood is om iemand zijn school-login op te slagen op de router. De Coral boards zijn allemaal verbonden via ethernet aan de router en het is mogelijk om in te loggen op de Dev Boards via MDT langs de WiFi.

Via het `mdt` commando is het mogelijk om lokaal over het netwerk inloggen op de nodes. Dat werkt net zoals SSH met volgend commando: `mdt shell 192.168.88.xx`


## SSH verbinding vanaf master

>Dat de master kan verbinden via SSH met eender welke node, maar niet omgekeerd. Hoe dat is ingesteld.


De master `.11` beschikt over een private sleutel waarvan de publieke sleutel aanwezig is op iedere andere node. Op deze manier kan de master zonder enige problemen wachtwoordloos inloggen op eender welke node.

Ik was hier echter in het begin best vergeetachtig in waardoor er telkens 'Permission denied' errors verschenen. Dit was opgelost nadat ik van de master, `cat .ssh/id_rsa.pub` heb ge copy-paste naar `.ssh/authorized_keys` van alle nodes.

