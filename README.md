# Edge AI - TPU Beowulf Cluster

In deze repo kan je alle documentatie vinden van the Friends of the TPU.

De documentatie is geschreven in markdown en staat online in de vorm van een docsify website.

## Documentie

Live versie van de docs kan je bekijken op https://daandekoningkrekels.github.io/edge-ai-cluster-docs/#/

## setup

installeer docsify cli globally:

```
npm i docsify-cli -g
```

## development evironment

run het volgende command:

```
docsify serve docs
```
## Afbeeldingen toevoegen

Afbeeldingen zet je in een `./assets/` folder lokaal aan het .md bestand waar je in werkt. 

Een onderschrift toevoegen aan een afbeelding doe je zo:

```markdown
![Een beschrijvende alt tekst](./assets/mooie-prent.jpg 'Een onderschrift of caption')
```
Tussen 'quotes' achter het pad naar de afbeelding.