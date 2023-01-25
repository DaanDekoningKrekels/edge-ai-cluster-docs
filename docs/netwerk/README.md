## Netwerk setup

<!-- > Hoe we het netwerk hadden ingesteld en wat de verschillende instellingen waren.

> Welke exacte router we hebben toevoegen Router HAP-HC lite tower new  -->

### Router

De kern van het netwerk is een [MikroTik hAP ac lite TC router](https://mikrotik.com/product/RB952Ui-5ac2nD-TC). Deze ziet er simpel uit maar er kan hier operationeel veel in worden aangepast. Dit geeft de kans om een netwerk op te zetten waar de cluster geïsoleerd kan zijn van de buitenwereld, maar tegelijkertijd ook verbinding heeft met het internet.   


#### IP adres toekennen

Via router software is er een vast IP ingesteld dat verbonden is met het MAC-adres.

`192.168.88.11` tot `192.168.88.15`

#### Netwerk instellingen

De MikroTik router is *dual-band*, daardoor kan er gebruik gemaakt worden van één band om een netwerk op te zetten voor de cluster en de andere band om te verbinden met wifi van de school voor internet. Het wifinetwerk is vernoemd naar "TPU_Friends" en de andere WiFi-band zijn is verbonden met Blechley. Blechley is gekozen omdat er dan geen nood is om iemand zijn school-login op te slagen op de router. De Coral boards zijn allemaal verbonden via ethernet aan de router en het is mogelijk om in te loggen op de Dev Boards via MDT langs de WiFi.

Via het `mdt` commando is het mogelijk om lokaal over het netwerk inloggen op de nodes. Dat werkt net zoals SSH met volgend commando: 

```bash
mdt shell 192.168.88.xx
```

In de interface van de netwerkinstellingen kunnen er verschillende zaken worden aangepast. Een belangrijk punt is het SSID. Dit is de naam die het netwerk zal krijgen. In dit geval is dit "TPU-Friends".

![image](https://user-images.githubusercontent.com/93762886/214342443-b6422198-0583-4959-ba76-d9ae7e7878f8.png)

Bij de DHCP server kan men zien welke apparaten er zijn verbonden met het netwerk van de router. Hier ziet men onder andere het MAC adres en het IP adres van het apparaat. Door de naam te weten te komen van het apparaat kon men het IP adres aanpassen. Zo krijgen al de nodes hun eigen IP adres dat statisch is gemaakt zodat zij telkens weer hetzelfde IP adres kregen toegewezen. 

![image](https://user-images.githubusercontent.com/93762886/214343028-b260ea21-f09b-4d4d-9c25-608c30bacace.png)

![image](https://user-images.githubusercontent.com/93762886/214343583-b2bcbb58-d19f-4235-b1fc-da430d2becda.png)

Om de ssh te kunnen enablen moet men er voor zorgen dat deze enabled is. Omdat men nog niet met de wifi is verbonden kan men geen nieuwe packages installeren. Er is hiervoor gekozen om de 5GHz te kiezen om met het internet te verbinden. Op deze manier kan de gebruiker nog steeds met de router verbinden via de 2.4GHz. 

![image](https://user-images.githubusercontent.com/93762886/214344223-40ef61a6-563f-414e-8326-e8628026265f.png)

![image](https://user-images.githubusercontent.com/93762886/214344347-5773882d-7c53-4e47-8195-450d14c173d7.png)

![image](https://user-images.githubusercontent.com/93762886/214344391-175b1fa4-339b-49d2-933b-a5de79f728ef.png)

![image](https://user-images.githubusercontent.com/93762886/214344423-bc0ccf30-d842-42fc-9fdd-46a39560b171.png)

Door de stappen uit te voeren werd er ontdekt dat de ethernet poort 1 als verbinding met de wifi werd ingesteld. Dit is de reden waarom er hier in het begin niet mee kon worden verbonden. 

![image](https://user-images.githubusercontent.com/93762886/214344565-85baa94c-43c1-4176-b8be-1661583aacb6.png)


## SSH verbinding vanaf master

Bij het opstellen van de infrastructuur werd er gemerkt dat de eerste ethernet poort van de router niet werkt. 

![image](https://user-images.githubusercontent.com/93762886/214343965-a59efc2f-e85d-4fad-a3ce-bd438aa47021.png)

Wanneer er bij de bridge werd gekeken kon men zien dat ether1 er niet bij stond ondanks dat deze was verbonden. Door ether1 hier dan aan toe te voegen kon men verbinding maken. 

![image](https://user-images.githubusercontent.com/93762886/214344057-f4b4674f-d14b-443d-a4c6-a9f6beb4b47a.png)


De master `.11` beschikt over een private sleutel waarvan de publieke sleutel aanwezig is op iedere andere node. Op deze manier kan de master zonder enige problemen wachtwoordloos inloggen op eender welke node.

Ik was hier echter in het begin best vergeetachtig in waardoor er telkens 'Permission denied' errors verschenen. Dit was opgelost nadat ik van de master, `cat .ssh/id_rsa.pub` heb gecopy-pastet naar `.ssh/authorized_keys` van alle nodes.

