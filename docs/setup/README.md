## Dev Boards flashen

>Uitleg en tutorial hoe de Dev Boards te programmeren zijn en welke instellingen we zoal hadden aangepast.

## MDT instellen

>Hoe we MDT hebben ingesteld met al die sleutels enzo.

MDT is een command line tool dat de gebruiker helpt om met het Dev Board te kunnen connecteren. Via deze command line kunnen er onder andere Debian packages worden geïnstalleerd en een shell terminal worden geopend op het bordje. 

Met volgend commando kan MDT op de host computer worden geïnstalleerd. 

```bash
python3 -m pip install --user mendel-development-tool
```

Om zeker te zijn dat MDT in de PATH environment variable staat wordt volgende commando uitgevoerd op Linux. 

```bash
echo 'export PATH="$PATH:$HOME/.local/bin"' >> ~/.bash_profile

source ~/.bash_profile
```

Om ervoor te zorgen dat de gebruikers met de verschillende borden kunnen verbinden via SSH zonder elke keer een paswoord te moeten invullen wordt er een private key gedeeld over de verschillende gebruikers zodat elke bordje dezelfde public key heeft. 

De gebruiker connecteert via USB-C kabel met het bordje. 
