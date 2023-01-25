# OpenMP

Bij parallelle computing worden de uitvoering van de processen tegelijkertijd uitgevoerd. Grote taken of problemen kunnen opgedeeld worden in kleinere delen zodat die taken tegelijkertijd uitgevoerd kunnen worden. In ons geval maken we een clusters van Coral Dev Boards waar parallel computing voorkomt. Om deze parallel computing te doen kan er gebruikt gemaakt worden openMP of openMPI. 


OpenMP is een application programming interface (API) die shared-memory multiprocessor kan aanspreken in C, C++ of in Fortran. Deze shared-memory multiprocessor bestaat uit meerdere procesoren die toegang hebben tot de hardware van apparaat. Deze openMP API is meer bedoeld om op één systeem de taken, op meerdere processoren te verdelen.

Met openMP wordt er verschillende parallelle toepassingen ontwikkeld voor platformen variërend van de standaard desktopcomputers tot supercomputer. Bij openMP wordt er ook gebruik gemaakt van multithreading. Multithreading is een methode van parallellism waarbij de primaire thread aantal andere sub-threads forkt en de taken worden dan onder deze threads verdeeld. De threads lopen dan gelijktijdig, waarbij de runtime omgeving threads toewijst aan zijn eigen processor. Het gedeelte van de code dat bedoeld is om parallel te lopen, zal eerst threads aanmaken met behulp van de compiler en dan zal de code uitgevoerd worden. Elke thread heeft een id en de primaire thread heeft een id van 0. Na de uitvoering van de geparallelleerde code worden alle threads terug samen één primaire thread. Hieronder wordt een afbeelding getoond om beter inzicht te krijgen.


![](./assets/Schema_multithreading.png 'Fig 1: Schema multithreading')

# OpenMPI

OpenMPI implementeert open source Message Passing Interface en wordt vooral ook gebruikt bij gedistribueerde parallelle computers. Het is een communicatieprotocol die gebruikt wordt parallel computing. In tegenstelling tot het hierboven beschreven shared-memory, maakt openMPI gebruik van verzameling van onafhankelijke memory cores die dan gesynchroniseerd worden via netwerk. Deze kunnen we meer terug vinden in clusters.

Dit betekent ook dat elke core (wordt ook soms node genoemd) zijn eigen geheugen heeft. Echter, de synchronisatie is ook nodig om de taken te verdelen en om resultaten te verzamelen. Al deze data wordt verzoden en verzamelt met behulp van message passing. 

OpenMPI biedt ook API requests zoals MPI_Send en MPI_Rec om te kunnen communiceren tussen de verschillende nodes. In tegenstelling tot openMP, moet hier de resultaten doorgestuurd worden naar de hoofdnode in het netwek. Hieronder wordt er een diagram getoond die het verschillen tussen de openMP en openMPI verduidelijkt.

![](./assets/Verschil_openMP_en_openMPI.png 'Fig 2: Verschil tussen openMP en openMPI')

Beide bibliotheken implementeren dus verschillende normen voor parallel computing maar met verschillende architecturen.

# MPICH

MPICH wordt ook beschouwd als een van de populairste implementaties van de MPI-standaard. Hier wordt ook de MPI en de MPI libraties gebruikt voor distributed-memory applicaties aan te sturen. MPICH heeft al aantal voorgaande versies namelijk MPICH1,MPICH2 en MPICH3. MPICH1 implementeerde MPI-1 standaard. MPICH2 bevatte aantal nieuwe mogelijkheden voor communicatie, dynamische processen, uitgebreide MPI-IO functionaliteiten enzovoort. MPICH3 is momenteel de laatste versie van de MPI standaard en heeft aantal nieuwe functies bijgekregen.

OpenMPI en MPICH werd geïnstalleerd bij alle bordjes maar enkel de MPICH werkte goed bij de Edge TPU’s. Hieronder worden de verschillende stappen beschreven die bij de installatie van de openMPI en MPICH. Deze moet op alle hosts geïnstalleerd worden.

Eerst werd er geprobeerd om OpenMPI te installeren hieronder worden de genomen stappen beschreven.

- Het is belangrijk dat het systeem up to date is voordat er iets geïnstalleerd wordt.
``` bash 
sudo apt-get update
```
![](./assets/updaten_van_systeem.png 'Fig 3: Updaten van het systeem')

- Upgraden van het systeem.
``` bash 
sudo apt-get upgrade
 ```
![](./assets/upgraden_van_systeem.png 'Fig 4: Upgraden van het systeem')

- De volgende commando zorgt ervoor dat de meta-pakketten die essentieel om een programma te compileren geïnstalleerd worden.
``` bash 
sudo apt install build-essential
```
![](./assets/installatie_build-essential.png 'Fig 5: Installeren van build-essential')

- De installatie van openMPI.
``` bash 
sudo apt-get install openmpi-bin openmpi-doc libopenmpi-dev
```
![](./assets/installatie_openMPI.png 'Fig 6: Installeren van openMPI')

- Om te kijken of openMPI goed geïnstalleerd werd, kan de volgende commando uitgevoerd worden.
``` bash 
mpiexec –version
```
![](./assets/MPI_versie_vragen.png 'Fig 7: Vragen van MPI versie')

- Een andere optie is ook mogelijk.
``` bash 
mpirun –version
```
![](./assets/mpirun_versie_vragen.png 'Fig 8: Vragen van MPI versie')

Nu is het tijd om de openMPI te testen door een voorbeeld programma te runnen. Om met de TPU’s te communiceren, wordt er gebruik gemaakt CMD. Omdat het niet mogelijk is om in CMD een editor te openen, wordt er een voorbeeld programma’s vanuit GitHub gebruikt. Deze kan gedaan worden met de volgende commando.
``` bash 
git clone https://github.com/open-mpi/ompi
```

- Om het programma te compileren wordt de volgende commando gebruikt.
``` bash 
mpicc -o hello hello_c.c
```
![](./assets/compileren_van_programma.png 'Fig 9: Compileren van het programma')

- Nu wordt de gecompileerde bestand uitgevoerd.
``` bash 
mpiexec -n 4 ./hello
```
![](./assets/uitvoering_van_code.png 'Fig 10: Uitvoeren van het programma')

Deze code is vooral geschreven in C, maar omdat de TPU’s gebruik maakt van Tensorflow die modelen die meestal in Python gescreven worden, is het best dat er hier ook gebruikt gemaakt wordt van Python.

- Installeren van Python3 environment.
``` bash 
sudo apt-get install python3
```
![](./assets/installatie_Python3.png 'Fig 11: Installeren van Python3')

- Installeren van mpi4. Deze zorgt ervoor dat MPI gebruikt kan worden met Python. Dit wil zeggen dat dit eigenlijk een library is, die in de Python scripts gebruik kan worden.
``` bash 
sudo apt install python3-mpi4py
```
![](./assets/installatie_mpi4.png 'Fig 12: Installeren van mpi4py')

- Om te kijken of mpi4py goed is geïnstalleerd kan de volgende commando uitgevoerd worden.
``` bash 
python3 -m pip show mpi4py
```
![](./assets/mpi4py_versie_vragen.png 'Fig 13: Versie van mpi4py vragen')

- Om voorbeeld codes van de py scripts te krijgen wordt er volgende commando gebruikt.
``` bash 
git clone https://github.com/jbornschein/mpi4py-examples
```
![](./assets/clone_van_mpi4py_examples.png 'Fig 14: Downloaden van voorbeeld programma')

Bij het uitvoeren van de programma’s kwamen er aantal errors opdagen. Na verder onderzoek en testing werd er duidelijk dat deze versie van openMPI niet compatibel is met de Coral Dev Board. Ook is het hier niet eenvoudig om met Python scripts te werken aangezien deze ook voor meer errors zorgt. Een voorbeeld hiervan is:

![](./assets/error_mpi4py.png 'Fig 15: Een van de error')

![](./assets/andere_soort_error.png 'Fig 16: Een andere voorbeeld error')

Er werd alles gedaan om deze soort errors weg te werken, maar helaas was het niet zo zeer gelukt. Na het bespreken met andere teamgenoten werd er beslist om MPICH3 te installeren en daarmee verder te gaan.

Zoals eerder vermeld is deze ook een andere versie van openMP en kan gebruikt worden om de taken te verdelen tussen verschillende nodes/TPU's. Daarnaast werkt deze versie ook veel beter met Python scripts. Hieronder staan de genomen stappen om de installatie van de MPICH te voltooien. Daarnaast wordt de MPICH vanuit de source code geïnstalleerd. Dit betekent dat er geen package manager gebruikt wordt en de sourcecode wordt dan op de TPU gecompileerd.

- Als eerst wordt de zip file van de MPICH3 geïnstalleerd met de volgende commando.

``` bash 
sudo wget https://www.mpich.org/static/downloads/3.2/mpich-3.2.tar.gz
```

![](./assets/installatie_MPICH.png 'Fig 17: Downloaden van MPICH')

- Deze zip file moet dan geünzipt worden.
``` bash 
sudo tar xfz mpich-3.2.tar.gz
```
![](./assets/unzippen_MPICH.png 'Fig 18: Unzippen van MPICH')

- Installeren van GNU Fortan. Deze is een compiler die gebruikt wordt om de MPICH3 te compilen. Bij de installatie moet er ook op “Y” gedrukt worden om de installatie te voltooien.
``` bash 
sudo apt-get install gfortran
```
![](./assets/installatie_gfortan.png 'Fig 19: Intalleren van gfortan')

- Nu wordt de MPICH3 geconfigureerd.
``` bash 
sudo /home/mendel/mpich-3.2/configure -prefix=/home/mpi/mpi-install
```
![](./assets/configureren_MPICH.png 'Fig 20: Configureren van MPICH')

- Nu is het tijd om de code te compileren. Dit duurde wel ongeveer 30
    minuten.
``` bash 
sudo make
```
![](./assets/make_commando.png 'Fig 21: Gebruik van make commando')

- Nadat de code gecompileerd is, wordt de MPICH nu geïnstalleerd.
``` bash 
sudo make install
```
![](./assets/make_MPICH.png 'Fig 22: Uitvoeren van install commando')

- Om nu de Python script te runnen, gebruik de volgende commando.
``` bash 
mpirun -np 2 python3 01-hello-world.py
```
![](./assets/runnen_programma_op_TPU.png 'Fig 23: Runnen van het programma')

- De voorgaande commando was bedoeld om op één TPU te runnen, maar om de taken tussen verschillende TPU’s te verdelen, kan de volgende commando uitgevoerd worden.

``` bash 
mpiexec -n <<aantal precessoren>> -H <<IP-adres van TPU’s>> python3 <<programmanaam>>
```
IP-adres van TPU’s zijn:

- 192.168.88.11

- 192.168.88.12

- 192.168.88.13

- 192.168.88.14

- 192.168.88.15

``` bash 
mpiexec -n 2 -H 192.168.88.11,192.168.88.12,192.168.88.13,192.168.88.14,192.168.88.15 python3 programmanaam
```
![](./assets/runnen_programma_met_andere_TPUS.png 'Fig 24: Runnen van het programma')

# Problemen

De MPICH kon de programma’s op de andere nodes starten, maar het programma werd niet verdeeld tussen de verschillende nodes en voerde gewoon de volledige programma uit. De programma’s die getest werden, hebben gebruik gemaakt van de CPU en niet van TPU. Dit betekent dat er nog verder een manier gezocht moet om met de programma’s de TPU van de Coral Dev board aan te spreken. Ook moest er nog getest worden dat de MPI toegankelijk was via de NFS. Dit moest nog veder afgewerkt worden, maar wegens tijdsgebrek was het niet meer mogelijk. 