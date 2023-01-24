## TensorFlow model draaien

>Hoe we een TensorFlow model zoal kunnen draaien op één TPU en hoe we dat willen verwezenlijken in de cluster. 

Om een python model te kunnen draaien op een Coral Dev Board moet deze worden gecompileerd naar 8bit. Wanneer het Tensorflow model wordt geconverteerd dan wordt dit een Tensorflow Lite file. Dit moet men dan nog compileren om het Edge TPU model te kunnen verkrijgen waarna men het kan deployen op het Coral Dev Board. Onderstaande afbeelding geeft hier een visuele voorstelling van. 

![image](https://user-images.githubusercontent.com/93762886/214374448-b8a30c63-3c42-4e2d-875f-376d1d8488f4.png)

Door bovenstaande stappen uit te voeren kan men een model laten draaien op één TPU. Om dit te kunnen verspreiden over verschillende TPU's moet men nog andere stappen uitvoeren. 
 
Eerst en vooral moet de master node weten welke andere TPU's er beschikbaar zijn. Dit kan men te weten komen door volgende code uit te voeren.  

![image](https://user-images.githubusercontent.com/93762886/214376031-180896f0-e437-47cf-a569-2439c7b59329.png)

Bovenstaande code toont al de andere TPU's indien deze gelinkt zijn aan de master via USB of PCI. Omdat dit in dit project niet het geval is moet er gezocht worden naar een andere manier om al de TPU's te kunnen vinden. 

Met onderstaande code kan er gezien worden waar de TPU zich bevindt. 

```bash
ls /dev | grep apex
apex_0
```

