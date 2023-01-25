# Coral Dev Board

## Wat is een Coral Dev Board?

Het Coral Dev Board is een *Single Board Computer*, of SBC, die een Edge TPU coprocessor bevat. Het is ideaal voor het maken van prototypes voor nieuwe projecten waarbij snelle on-device inferencing voor machine learning modellen nodig is.

[![](./assets/devboard-inhand.jpg 'Fig. 1: Coral Dev Board in de hand. [1]')](./bronnen.md#coral-dev-board)

[![](./assets/devboard-dimensions.jpg 'Fig. 2: Coral Dev Board dimensies. [3]')](./bronnen.md#coral-dev-board)

## Architectuur

Een Coral Dev Board bestaat uit twee onderdelen:
1. Edge TPU System-on-Module (SoM)
2. Baseboard met connectoren dat het een *Single Board Computer* maakt.

Deze zijn verbonden via drie DF40C-100DP-0.4V(51) 100-pin connectoren.

[![](./assets/som-connector-schematic.png 'Fig 3: SoM connector schema. [4]')](bronnen.md#coral-dev-board)

### Edge TPU System-on-Module (SoM)

[![](./assets/som-blockdiagram.png 'Fig 4: SoM blockdiagram. [3]')](bronnen.md#coral-dev-board)

[![](./assets/som-dimensions-basic.png 'Fig 5: SoM dimensies. [3]')](bronnen.md#coral-dev-board)

### Baseboard

[![](./assets/diagram-baseboard_bw.png 'Fig 6: Baseboard blockdiagram. [3]')](bronnen.md#coral-dev-board)

[![](./assets/devboard-dimensions-illi.png 'Fig 7: Baseboard dimensies. [3]')](bronnen.md#coral-dev-board)

## Tensor Processing Unit (TPU)

Een Tensor Processing Unit, of TPU, is een ASIC ontwikkeld door Google. Het wordt gebruikt voor machine learning applicaties zoals neurale netwerken. De chip is ontworpen om Google's eigen Tensorflow software op te laten draaien.

In tegensteling tot een CPU die zijn berekeningen één voor één gaat uitvoeren, gaat een TPU, net zoals een GPU, gespecialiseerde berekeningen parallelliseren. Dit wilt zeggen dat meerdere berekeningen tegelijkertijd kunnen plaatsvinden. Ze zijn ontworpen om een hoog volume aan lageprecisie-berekeningen te verwerken met meer invoer/uitvoer bewerkingen per verbruikte eenheid energie. TPU's zijn zeer geschikt voor convolutionele neurale netwerken, terwijl dat GPU's voordeel hebben bij sommige volledig verbonden neurale netwerken.

Het voorvoegsel *Edge* in *Edge TPU* geeft aan dat een AI model lokaal op de hardware kan draaien zonder off-loading naar de cloud.
