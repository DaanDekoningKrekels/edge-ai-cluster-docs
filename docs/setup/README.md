## Dev Boards flashen

>Uitleg en tutorial hoe de Dev Boards te programmeren zijn en welke instellingen we zoal hadden aangepast.

De documentatie rond het installeren van Mendel Linux is best uitgebreid en is [hier](https://coral.ai/docs/dev-board/get-started/) te vinden. Voor de installatie is een micro-SD-kaart nodig en een computer. Op de SD-kaart staat het Linux-systeem dat bij het opstarten met de schakelaars in de juiste positie het systeem installeert naar het interne flash geheugen. De SD-kaart kan na dit proces weggehaald worden.

Om met de Dev Boards te verbinden kunnen we gebruik maken van MDT. MDT staat voor mendel-development-tool en lijkt qua werking wat op SSH. Als je je computer aansluit via USB-C met een Dev Board dan wordt er een virtueel netwerk aangemaakt waarlangs de MDT-communicatie loopt.


## MDT instellen

>Hoe we MDT hebben ingesteld met al die sleutels enzo.

De documentatie om MDT te installeren vindt u [hier](https://coral.ai/docs/dev-board/get-started/#install-mdt).

Om ervoor te zorgen dat de gebruikers met de verschillende borden kunnen verbinden via MDT zonder elke keer een wachtwoord te moeten invullen wordt er een private key gedeeld over de verschillende gebruikers zodat elke bordje dezelfde public key heeft. 

Om er voor te zorgen dat bovenstaande stappen niet elke week steeds opnieuw moeten worden uitgevoerd wordt de private key file gekopieerd om bij elke gebruiker in zijn /home/Users/<naam>/.config/mdt/mdt.key file te plaatsen. Op deze manier wordt er op elk bordje dezelfde public key aangemaakt en maakt het in het vervolg niet uit wie er op welk bordje inlogt.
