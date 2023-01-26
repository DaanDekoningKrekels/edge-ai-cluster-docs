# Behuizing

De ontwikkeling van de behuizing heeft geresulteerd in de creatie van drie verschillende versies. Elk van deze versies zullen hieronder visueel worden gepresenteerd en besproken. De overwegingen achter de keuzes die tijdens het ontwerpproces zijn gemaakt, zullen worden geanalyseerd, evenals de respectievelijke sterkte- en zwaktepunten van ieder ontwerp.

## Iteratie 1

In de vroege fase van ontwikkeling, werd de eerste iteratie van de behuizing ontworpen. Echter, in deze periode waren de vereisten voor de behuizing nog niet specifiek vastgesteld. Dit resulteerde in een eerste iteratie die degelijk afwijkt van de verdere ontwerpen en niet voldoet aan de gestelde eisen.

Een afbeelding van deze eerste iteratie van de behuizing wordt hieronder gepresenteerd. Het opvallendste aspect van het ontwerp is de matige kwaliteit ervan, bestaande uit slechts vijf gelasercutte plaatjes waarop de TPU's zijn geplaatst.

In deze periode was het nog onbekend dat de ontwerper rekening diende te houden met de benodigde apparatuur die in de behuizing moest passen. Bovendien, is er geen voorziening voor koeling, buiten de ingebouwde koeling op de TPU's zelf.

Het is duidelijk dat deze behuizing alleen geschikt is voor het (semi) proper houden van de kabels. Er was een herontwerp nodig om aan de eisen te voldoen. 

<img src="behuizing/assets/behuizing%20iteratie%201.png" width="300">

_Behuizing iteratie 1_

## Iteratie 2

Deze iteratie heeft een fundamenteel herontwerp van de behuizing. Om inspiratie te verkrijgen, is er gezocht naar bestaande cluster behuizingen via internet. Door middel van zoekopdrachten op Google afbeeldingen, werd een specifieke cluster case voor Raspberry Pi's ontdekt. Dit ontwerp is vervolgens aangepast aan de vereisten van de TPU's om te functioneren als geschikte behuizing.

<img src="behuizing/assets/behuizing%20voorbeeld%20cluster%20case.jpg" width="300">

_Voorbeeld van cluster case met Raspberry Pi's 1_

<div style="display: flex;">
 <img src="behuizing/assets/behuizing%20iteratie%202_1.png" width="300">
 <img src="behuizing/assets/behuizing%20iteratie%202_2.png" width="300">
</div>

_Behuizing iteratie 2_

Hieronder wordt een afbeelding gepresenteerd van de tweede iteratie van de behuizing. Deze bestaat uit verschillende componenten, waaronder een frontpanel, sidepanel, verdiepingen, onderkant en backplate. Het frontpaneel is voorzien van twee (computer) ventilatoren met een breedte van 120mm, welke voor voldoende koeling zorgen voor de TPU's door frisse lucht te blazen van het frontpaneel naar de achterkant. Elke TPU heeft zijn eigen specifieke verdieping met openingen voor de kabels. Deze kabels zullen naar "de kelder" leiden, waar de router en voeding zich bevinden. Beide zijpanelen zijn ook voorzien van openingen, waardoor het mogelijk is om kabels via deze weg te leggen. Dit kan bijvoorbeeld handig zijn als een specifieke TPU moet worden verbonden met een computer. De TPU's zijn gemonteerd op houten platen die gemakkelijk uit de behuizing kunnen worden verwijderd via rails. De benodigde kabels voor de componenten en apparatuur worden door de twee openingen van de backplate gestoken en zo verbonden met alles in "de kelder". De behuizing is ook voorzien van twee handvatten bovenaan, waardoor de behuizing praktischer is om te verplaatsen wanneer nodig.

Er zijn enkele problemen ontstaan tijdens dit ontwerpproces. Specifiek, de handvatten van de platen waarop de TPU's zijn gemonteerd (de hoekige ovale waar de vingers in kunnen worden gestoken) waren niet dik genoeg. Hoewel dit fout in de render niet zichtbaar is, omdat het is aangepast in het uiteindelijke ontwerp van de tweede iteratie, heeft dit probleem ertoe geleid dat dit onderdeel zeer kwetsbaar was voor breuk, aangezien het niet dik genoeg was.

<div style="display: flex;">
 <img src="behuizing/assets/behuizing%20houder%20oud.png" width="300">
 <img src="behuizing/assets/behuizing%20houder%20nieuw.png" width="300">
</div>

_Oude versie en Nieuwe versie_

In een eerdere versie van de behuizing was de breedte van het handvat slechts 2.5mm, hetgeen onvoldoende was voor een stevige constructie. Om deze beperking aan te pakken, is er besloten om de breedte te verhogen naar 5mm, waardoor de totale breedte van het handvat 7.5mm bedraagt. Hierdoor is de stevigheid van het handvat verbeterd en komt het minder snel tot breuk.

Er zijn nog enkele andere aspecten waar verbetering mogelijk is, echter zullen deze in een latere iteratie van de behuizing worden aangepakt.

## Derde iteratie

De besluitvorming om een extra iteratie te maken voor de behuizing was een gevolg van de ontdekte fouten in het ontwerp in de latere fasen van de ontwikkeling. Dit had oorspronkelijk niet in de planning voorzien. Echter, gezien de noodzaak om deze problemen op te lossen, is er besloten om deze iteratie te ontwikkelen.

<div style="display: flex;">
 <img src="behuizing/assets/behuizing%20iteratie%203_1.png" width="300">
 <img src="behuizing/assets/behuizing%20iteratie%203_2.png" width="300">
</div>

_Behuizing iteratie 3_

De derde iteratie van het behuizingontwerp is afgebeeld in de bijgevoegde render. Hoewel er in deze iteratie geen significante veranderingen zijn aangebracht, zijn er zoals eerder vermeld enkele problemen aangepakt. Een van deze problemen was de onbeschikbaarheid van 120mm ventilatoren op school. Om dit op te lossen, is het frontpanel aangepast zodat deze geschikt is voor 80mm ventilatoren die wel beschikbaar waren. Deze aanpassing maakte het mogelijk om drie ventilatoren te integreren in plaats van twee, en elke ventilator is ook voorzien van een beschermfilter (zoals afgebeeld in het volgende hoofdstuk).

Daarnaast is er een laag verf aangebracht op de behuizing om deze professioneler te laten lijken. Er is wit verf gebruikt op de houten delen en een licht blauwe filament voor de 3D-geprinte componenten. Deze kleurencombinatie is gekozen omdat deze past bij de naam "Friends Of The TPU".

Een groot probleem dat pas laat werd ontdekt was dat de ruimte in de kelder onvoldoende was om zowel de voeding als de router op te bergen. Er was wel een oplossing bedacht om dit te verhelpen zonder de gehele behuizing groter te maken. Dit was door de voeding of router te monteren aan één van de zijpanelen. Hoewel dit niet de optimale oplossing was, was er op dat moment geen tijd meer om een andere oplossing te implementeren.

Een ander probleem dat zich voordeed was dat er geen manier was om de ventilatoren te voeden. De ventilatoren vereisten 12V maar de voeding levert slechts 5V (via USB). Er was wel een idee voor een oplossing: een kleine printplaat frezen met daarop een DC-barrel jack en een schroefconnector. Hierdoor zou de kabels van de ventilatoren vastgemaakt kunnen worden aan de schroefconnector en voeden via een externe voeding door de barrel jack. Helaas was de freesmachine voor de printplaat te maken kapot, dus kon deze niet gebruikt worden. Er was ook overwogen om dit op te lossen door gebruik te maken van perfboard, maar de school had hier geen op voorraad.

<div style="display: flex;">
 <img src="behuizing/assets/behuizing%20barrel%20jack.png.jpg" width="300">
 <img src="behuizing/assets/behuizing%20schroefconnector.jpg" width="300">
 <img src="behuizing/assets/behuizing%20perfboard.png" width="300">
</div>

_DC-barrel jack, Schroefconnector, Perfboard_

# Resultaat

Hier zijn een aantal foto's van het resultaat:

<div style="display: flex;">
 <img src="behuizing/assets/behuizing%20resultaat%201.jpg" width="300">
 <img src="behuizing/assets/behuizing%20resultaat%202.jpg" width="300">
 <img src="behuizing/assets/behuizing%20resultaat%203.jpg" width="500">
</div>

_Resultaat_

Hoewel er nog veel fouten aanwezig zijn in dit ontwerp en het uiteindelijk niet geschikt was als behuizing voor de cluster, beschouw ik dit toch als een behoorlijk resultaat voor een "concept of design".

Indien er meer tijd had bestaan, had er nog een vierde iteratie kunnen worden gemaakt waarin de eerder genoemde problemen zouden zijn opgelost, en zou het resultaat veel praktischer en bruikbaarder zijn als behuizing voor een (Beowulf) cluster.
