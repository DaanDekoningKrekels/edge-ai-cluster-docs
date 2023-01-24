## TensorFlow model draaien

>Hoe we een TensorFlow model zoal kunnen draaien op één TPU en hoe we dat willen verwezenlijken in de cluster. 

Om een python model te kunnen draaien op een Coral Dev Board moet deze worden gecompileerd naar 8bit. Wanneer het Tensorflow model wordt geconverteerd dan wordt dit een Tensorflow Lite file. Dit moet men dan nog compileren om het Edge TPU model te kunnen verkrijgen waarna men het kan deployen op het Coral Dev Board. Onderstaande afbeelding geeft hier een visuele voorstelling van. 

![image](https://user-images.githubusercontent.com/93762886/214374448-b8a30c63-3c42-4e2d-875f-376d1d8488f4.png)

Door bovenstaande stappen uit te voeren kan men een model laten draaien op één TPU. Om dit te kunnen verspreiden over verschillende TPU's moet men nog andere stappen uitvoeren. 
 
