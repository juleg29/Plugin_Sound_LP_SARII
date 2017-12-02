# Séance Finale

Vous avez maintenant les bases pour creer le plugin binaural.

## Etape 1: Création du Projet

* Création d'un nouveau plugin audio. 
* Dans l'introjucer, il faut ajouter le module DSP pour pouvoir utiliser la convolution (menu de gauche > bouton add).
* Dans la partie réglage, il faut utiliser la version 14 de C++ pour pouvoir utiliser le module DSP (C++ language standard).
* Ouvrez ensuite la solution avec visual studio (ne plus revenir à l'introjucer à partir de maintenant)

### Etape 2: Utilisation des object convolution

* Dans votre fichier Plugin_Processor.h, il faut ajouter deux objets appartenant à la classe Convolution dans la partie public.

``` 
Convolution conv_droite;
``` 

* Dans la méthode prepare_to_play de votre plugin (fichier Plugin_Processor.cpp), il faut initialiser votre objet convolution

``` 
ProcessSpec spec { sampleRate, static_cast<uint32> (samplesPerBlock), 1 };
conv_droite.prepare(spec);
``` 


### Etape 3: Création de la GUI

Notre GUI sera composé de plusieurs élements:

* Un Slider en mode Rotary permettant de choisir un nombre entre 1 et 25 (azimut)
* Un Slider en mode Rotary permettant de choisir un nombre entre 1 et 25 (elevation)
* Un TextButton pour activer la selection d'un repertoire
* Un label permettant d'afficher le répertoire selectionné
* Une Combobox permettant de selectionner un flux audio en entrée (canal gauche, droite ou un mix des deux)

Il faudra ajouter les listener relatifs au slider, Textbutton et Combobox.

> Note: A chaque fois que vous ajouter un élement dans l'UI, il faut tout d'abord indiquer l'element dans le PlugEditor.h, 
puis configurer votre élement et le rendre visible (addAndMakeVisible) lors de la construction de votre objet AudioProcessorEditor et enfin spécifier son positionnement graphique dans la méthode resized de votre AudioProcessorEditor (methode setBounds)

### Etape 4: Lecture des HRIR

* Lorsque l'utilisateur appuie sur le TextButton, le plug doit lui proposer de selectionner un répertoire. La selection d'un répertoire peut se faire
via un objet FileChooser. 

```
        FileChooser chooser ("Select a HRTF Folder...",File::nonexistent,"*.wav");
        if (chooser.browseForDirectory())
        {
        
        }
        
```
* Le répertoire selectionné (object File dans Juce) doit être sauvegardé dans votre objet AudioProcessorEditor.
* Lorsque l'un des Slider bouge, vous devez charger les HRIR. 
  * Dans un premier temps, il faut lire la valeur des Slider (.getValue()) et stocker le resultat dans deux variables entières 
  (1 pour l'azimut et 1 pour l'élevation).
  * Dans un second temps, il faut creer deux chaines de caractères permettant de specifier le nom des fichier HRIR (sans le répertoire).
  
  ```
   std::string hrir_droite="HRIRdroite_az"+std::to_string(az)+"_el"+std::to_string(el)+".wav";
  ```
  
 * Enfin, il faut lancer le chargement des fichiers. Au lieu de charger les fichiers dans un Buffer, il est directement possible de le charger
dans votre objet convolution.
 
   ``` 
    processor.conv_droite.loadImpulseResponse(folder.getChildFile (String(hrir_droite)),false,true, 1000);
   ```
   
### Etape 5: Gestion du traitement audio

La gestion du traitement audio est réalisé dans la méthode processBlock du fichier PLuginProcessor.cpp. Pour réaliser la convolution, il faudra

* Créer un object AudioBlock à partir du buffer: AudioBlock<float> block (buffer);
* Charger un canal de votre AudioBlock. Par exemple:  ```auto canal_droite = block.getSingleChannelBlock (0)```
* Traiter votre canal avec l'objet convolution:

```
conv_droite.process(ProcessContextReplacing<float> (canal_droite) );
```
