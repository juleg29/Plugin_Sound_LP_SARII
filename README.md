# Plugin VST de spatialisation binaurale

* Code, texte et doc en anglais -> audience plus elevée *

## Objectif

Création d'un plugin VST, AU, DXi pour la spatialisation sonore au casque par [synthèse binaurale
[synthèse binaurale](https://en.wikipedia.org/wiki/Head-related_transfer_function)

## Methodologie

* Developpement via le framework [JUCE 5.1](https://www.juce.com)
* Utilisation de banques d'[HRTF](https://www.sofaconventions.org/mediawiki/index.php/Files) pour la spatialisation
* Calcul de la Convolution réalisée via les fonctions DSP tools de Juce 5.1 (classe [convolution](https://www.juce.com/doc/classConvolution) )

## Contributeurs

* Julien Legrand (LP SARII, IUT Brest)

