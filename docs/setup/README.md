## Dev Boards flashen

>Uitleg en tutorial hoe de Dev Boards te programmeren zijn en welke instellingen we zoal hadden aangepast.

## MDT instellen

>Hoe we MDT hebben ingesteld met al die sleutels enzo.


Om ervoor te zorgen dat de gebruikers met de verschillende borden kunnen verbinden via SSH zonder elke keer een paswoord te moeten invullen wordt er een private key gedeeld over de verschillende gebruikers zodat elke bordje dezelfde public key heeft. 

Om er voor te zorgen dat bovenstaande stappen niet elke week steeds opnieuw moeten worden uitgevoerd de private key file gekopieerd om bij elke gebruiker in zijn /home/Users/<naam>/.config/mdt/mdt.key file te plaatsen. Op deze manier wordt er op elk bordje dezelfde public key aangemaakt en maakt het in het vervolg niet uit wie er op welk bordje inlogt.
