# OpenMP

Bij parallelle computing worden de uitvoering van de processen tegelijkertijd uitgevoerd. Grote taken of problemen kunnen opgedeeld worden in kleinere delen zodat die taken tegelijkertijd uitgevoerd kunnen worden. In ons geval maken we een clusters van Coral Dev Boards waar parallel computing voorkomt. Om deze parallel computing te doen kan er gebruikt gemaakt worden openMP of openMPI. 


OpenMP is een application programming interface (API) die shared-memory multiprocessor kan aanspreken in C, C++ of in Fortran. Deze shared-memory multiprocessor bestaat uit meerdere procesoren die toegang hebben tot de hardware van apparaat. Deze openMP API is meer bedoeld om op één systeem met meerdere processoren de talken te verdelen.

Met openMP wordt er verschillende parallelle toepassingen ontwikkeld voor platformen variërend van de standaard desktopcomputers tot supercomputer. Bij openMP wordt er ook gebruik gemaakt van multithreading. Multithreading is een methode van parallellism waarbij de primaire thread aantal andere sub-thread forkt en de taken worden dan onder deze threads verdeeld. De threads lopen dan gelijktijdig, waarbij de runtime omgeving threads toewijst aan zijn eigen processor.
Het gedeelte van de code dat bedoeld is om parallel te lopen, zal eerst threads aanmaken met behulp van de compiler en dan zal de code uitgevoerd worden. Elke thread heeft een id en de primaire thread heeft een id van 0. Na de uitvoering van de geparallelleerde code worden alle threads terug samen één primaire thread. Hieronder wordt een afbeelding getoond om beter inzicht te krijgen.


<img src="./assets/media/image1.png"/>

# OpenMPI

OpenMPI implementeert open source Message Passing Interface en wordt vooral gebruikt bij gedistribueerde parallelle computers. Het is een communicatieprotocol voor parallelle computer. In tegenstelling tot het hierboven beschreven shared-memory, maakt openMPI gebruik van verzameling van onafhankelijke memory cores die dan gesynchroniseerd worden via netwerk. Deze kunnen we meer terug vinden in clusters.

Dit betekent ook dat elke core (wordt ook soms node genoemd) zijn eigen geheugen heeft. Echter, de synchronisatie is ook nodig om de taken te verdelen en om resultaten te verzamelen. Al deze data wordt verzoden en verzamelt met behulp van message passing. 

OpenMPI biedt ook  API requests zoals MPI_Send en MPI_Rec om te kunnen communiceren tussen verschillende nodes. In tegenstelling tot openMP, moet hier de resultaten doorgestuurd worden naar de hooft node in het netwek. Hieronder wordt er een diagram getoond die het verschillen tussen de openMP en openMPI verduidelijkt.


<img src="./assets/media/image2.png"/>

Beide bibliotheken implementeren dus verschillende normen voor parallel computing maar met verschillende architecturen.

# MPICH

MPICH wordt ook beschouwd als een van de populairste implementaties van de MPI-standaard. Hier wordt ook de MPI en de MPI libraties gebruikt voor distributed-memory applicaties aan te sturen. MPICH heeft al aantal voorgaande versies namelijk MPICH1,MPICH2 en MPICH3. MPICH1 implementeerde MPI-1 standaard. MPICH2 bevatte aantal nieuwe mogelijkheden voor communicatie, dynamische processen, uitgebreide MPI-IO functionaliteiten enzovoort. MPICH3 is momenteel de laatste versie van de MPI standaard.

Alle drie versies van de MPI werd geïnstalleerd en enkel de MPICH werkte goed bij de Edge TPU’s. Hieronder worden de verschillende stappen beschreven die bij de installatie van de MPICH verder helpen.

Eerst werd er geprobeerd om OpenMPI te installeren hieronder worden de genomen stappen beschreven.

- Het is belangrijk dat het up to date is voordat er iets geïnstalleerd wordt.
``` bash 
sudo apt-get update
```
<img src="./assets/media/image3.png"/>

- Upgraden van het systeem.
``` bash 
sudo apt-get upgrade
 ```
<img src="./assets/media/image4.png" />

- De volgende commando zorgt ervoor dat de meta-pakketten die essentieel om een programma te compileren geïnstalleerd worden.
``` bash 
sudo apt install build-essential
```
<img src="./assets/media/image5.png" />

- De installatie van openMPI.
``` bash 
sudo apt-get install openmpi-bin openmpi-doc libopenmpi-dev
```
<img src="./assets/media/image6.png" />

- Om te kijken of openMPI goed geïnstalleerd werd, moeten de volgende commando’s uitgevoerd worden.
``` bash 
mpiexec –version
```
<img src="./assets/media/image7.png" />

- Andere optie is ook mogelijk.
``` bash 
mpirun –version
```
<img src="./assets/media/image8.png" />

Nu is het tijd om de openMPI te testen door een voorbeeld programma te runnen. Om met de TPU’s te communiceren, wordt er gebruik gemaakt CMD. Omdat het niet mogelijk is om in CMD een editor te openen, wordt er een voorbeeld programma’s vanuit GitHub gebruikt. Deze kan gedaan worden met de volgende commando:
``` bash 
git clone <https://github.com/open-mpi/ompi>
```

- Om te compileren wordt de volgende commando gebruikt.
``` bash 
mpicc -o hello hello_c.c
```
<img src="./assets/media/image9.png" />

- Nu wordt de gecompileerde bestand uitgevoerd.
``` bash 
mpiexec -n 4 ./hello
```
<img src="./assets/media/image10.png" />

Deze code is vooral geschreven in C, maar omdat de TPU’s gebruik maakt van Python is het best dat er hier ook gebruikt gemaakt wordt van Python.

- Installeren van Python3 environment.
``` bash 
sudo apt-get install python3
```
<img src="./assets/media/image11.png" />

- Installeren van mpi4. Deze zorgt ervoor dat MPI gebruikt kan worden met Python. Dit wil zeggen dat dit eigenlijk een library is, die in Python scripts gebruik kan worden.
``` bash 
sudo apt install python3-mpi4py
```
<img src="./assets/media/image12.png" />

- Om te kijken of mpi4py goed is geïnstalleerd kan de volgende commando uitgevoerd worden.
``` bash 
python3 -m pip show mpi4py
```
<img src="./assets/media/image13.png" />

- Om voorbeeld codes van de py scripts te krijgen wordt er volgende commando gebruikt.
``` bash 
git clone <https://github.com/jbornschein/mpi4py-examples>
```
<img src="./assets/media/image14.png" />

Bij het uitvoeren van de programma’s kwamen er aantal errors opdagen. Na verder onderzoek en testing werd er duidelijk dat deze versie van openMPI niet compatibel is met de Coral Dev Board. Ook is het hier niet eenvoudig om met Python scripts te werken aangezien deze ook voor meer errors zorgt. Een voorbeeld hiervan is:

<img src="./assets/media/image15.png" />

Er werd alles gedaan om deze soort errors weg te werken, maar helaas was het niet zo zeer gelukt. Na het bespreken met andere teamgenoten werd er beslist om MPICH3 te installeren en daarmee verder te gaan.

Zoals eerder vermeld is deze ook een andere versie van openMP en kan gebruikt worden om de taken te verdelen tussen verschillende nodes/tpu’s. Daarnaast werkt deze versie ook veel beter met Python scripts. Hieronder staan de genomen stappen om de installatie van de MPICH te voltooien. Daarnaast wordt de MPICH vanuit de source code geïnstalleerd. Dit betekent dat er geen package manager gebruikt wordt en de sourcecode wordt dan op de TPU gecompileerd.

- Als eerst wordt de zip file van de MPICH3 geïnstalleerd met volgende commando.
``` bash 
sudo wget <https://www.mpich.org/static/downloads/3.2/mpich-3.2.tar.gz>
```
<img src="./assets/media/image16.png" />

- Deze zip file moet dan geünzipt.
``` bash 
sudo tar xfz mpich-3.2.tar.gz
```
<img src="./assets/media/image17.png" />

- Installeren van GNU Fortan. Deze is een compiler die gebruikt wordt om de MPICH3 te compilen. Bij de installatie moet er ook op “Y” gedrukt worden om de installatie te voltooien.
``` bash 
sudo apt-get install gfortran
```
<img src="./assets/media/image18.png" />

- Nu wordt de MPICH3 geconfigureerd.
``` bash 
sudo /home/mendel/mpich-3.2/configure -prefix=/home/mpi/mpi-install
```
<img src="./assets/media/image19.png" />

- Nu is het tijd om de code te compileren. Dit duurde wel ongeveer 30
    minuten.
``` bash 
sudo make
```
<img src="./assets/media/image19.png" />

- Nadat de code gecompileerd is, wordt de MPICH nu geïnstalleerd.
``` bash 
sudo make install
```
<img src="./assets/media/image20.png" />

- Om te controleren of nu de Python script kan de script op volgende manier uitgevoerd worden.
``` bash 
mpirun -np 2 python3 01-hello-world.py
```
<img src="./assets/media/image21.png" />

- De voorgaande commando was bedoeld om op één TPU te runnen, maar om de taken tussen verschillende tpu’s te verdelen, kan de volgende commando uitgevoerd worden.

``` bash 
mpiexec -n \<\<aantal precessoren\>\> -H \<\<IP-adres van TPU’s\>\> python3 \<\<programmanaam\>\>
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
<img src="./assets/media/image22.png" />
