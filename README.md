[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-718a45dd9cf7e7f842a935f5ebbe5719a5e09af4491e668f4dbf3b35d5cca122.svg)](https://classroom.github.com/online_ide?assignment_repo_id=11101312&assignment_repo_type=AssignmentRepo)
# Classificació de gèneres musicals

L'objectiu d'aquest projecte és classificar música en 163 gèneres diferents utilitzant atributs extrets o analitzant les pistes dels arxius MP3. Per aconseguir-ho, es farà servir una combinació de xarxes neuronals convolucionals (CNN) i xarxes neuronals recurrents (RNN).

Els models de CNN són excel·lents per processar dades espaials com imatges i, en aquest cas, es poden utilitzar per extreure característiques de les pistes de música. Les capes de convolució de la CNN poden aprendre patrons locals en les dades d'àudio i, posteriorment, s'aplicaran capes totalment connectades per classificar les característiques extretes.
D'altra banda, les RNN són útils per processar seqüències de dades, com ara les representacions en temps de les pistes d'àudio. Les RNN són capaces de capturar dependències a llarg termini en les dades seqüencials, cosa que les fa adequades per a tasques de classificació d'àudio.

El projecte en el qual s'ha treballat té com a tasca principal la classificació d'àudio.

Pel que fa a les dades, es disposa de metadades que ocupen aproximadament 342MB i inclouen informació com el títol de la cançó, l'artista, l'àlbum, etc. A més, hi ha 8.000 cançons de 8 gèneres diferents que ocupen al voltant de 7GB. Aquestes cançons seran fonamentals per entrenar i avaluar el model de classificació.

<p align="center">
<img src="https://github.com/DCC-UAB/xnap-project-matcad_grup_11/blob/main/IMG_20230307_230625460.jpg", widht="300", height="300">
</p>

## Estructura del codi
1. models.py : Es defineixen varies arquitectures de xarxes neuronals usant 'torch.nn'. Aquesta consta de tres classes.
2. models_utils.py: Col·lecció de funcions que modifiquen paràmetres d'inicialització dels models i retornen informació del model i també de mètriques.
3. utils.py : És una col·lecció de funcions i classes que realitzen diverses tasques relacionades amb el processament de dades per un model de classificació de gèneres de música usant espectogrames d'àudio.
4. download_data.sh : Crea un directori anomenat "data" si no existeix, descarrega i extreu dos fitxers zip ("fma_metadata.zip" i "fma_small.zip") d'una URL proporcionada i realitza altres operacions per organitzar els fitxers descarregats dins del directori "data".
5. environment.yml : Arxiu de configuració en YAML que descriu un entorn conda amb el nom "xnap-example".
6. main.py : Realitza el procés d'entrenament i prova d'un model de xarxa neuronal convolucional per a la classificació de gèneres musicals utilitzant dades d'espectrogrames. També utilitza la plataforma WandB per al seguiment i la visualització. Si no estan ja creats, també utilitza funcions de utils.py per crear els espectrogrames.
7. train.py : Defineix un procés d'entrenament per a un model de xarxa neuronal, calcula les pèrdues per a cada lot d'entrenament i les registra en el registre de WandB per al seguiment i la visualització.
8. test.py : Defineix el procés de testeig per a un model de xarxa neuronal que es realitzarà a cada època, igual que en el fitxer train, es calculen les mètriques pertinents i es registren al WandB.
9. pre_trained_models : Carrega un model ja entrenat i realitza una predicció sobre les dades passades. Està pensat per ser utilitzat per dades no vistes (ni en el train ni en el test, d'altres cançons que com per exemple del "fma_medium.zip"). Per ara dona la predicció d'un set de test del dataset original, caldria descarregar un nou dataset i treballar amb ell. 

Abans d'executar el codi cal crear un entorn local amb conda i activar-lo. El fitxer [environment.yml] conté totes les dependències necessàries per a poder executar el codi adequadament. Per activar-lo cal executar ``conda env create --file environment.yml``. També cal tenir instal·lat 7zip a l'ordinador ``sudo apt install p7zip-full p7zip-rar``.

```
conda activate xnap-example

chmod +x download_data.sh
```
Per executar un model:
```
python main.py
```
## Resultats
| Model | Initial LR | Data Augmentation | Weight Decay |Epochs | Time (s) | Nº Parameters | Train Loss | Test Loss | Accuracy |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CNN1D | 0.001 | No | No | 30 | 900 | 236,680 | 1.50 | 1.85 | 33% |
| CNN1D | 0.0001 | Yes | Yes | 50 | 1200 | 236,680 | 1.3 | 1.55 | 47% |
| CNN2D | 0.001 | No | No | 30 | 5180 | 37,912,576 | 0.1 | 1.5 | 49% |
| CRNN | 0.001 | No | No | 30 | 6220 | 438,808 | 0.1 | 1.1 | 56% |
| CRNN | 0.01 | No | No | 30 | 6220 | 438,808 | 2.08 | 2.08 | 12% |
| CRNN | 0.0001 | No | Yes | 20 | 3000 | 438,808 | 0.9 | 1.3 | 47% |


## Matrius de confusió

Confusion CNN1D (Data Augmentation)
<p align="center">
<img src="https://cdn.discordapp.com/attachments/1107734165383823424/1112432536082202644/WB_Chart_28_5_2023_19_29_34.png", widht="300", height="300">
</p>

Confusion CRNN
<p align="center">
<img src="https://cdn.discordapp.com/attachments/1107734165383823424/1112424600576467055/WB_Chart_28_5_2023_18_58_00.png", widht="300", height="300">
</p>

## Conclusions

La implementació de diferents models i l'utilització de diferents paràmetres ha permès observar com l'aprenentatge de les dades (els espectrogrames) es diferencia. Per una banda, s'ha pogut distingir que, tot i que la relació entre l'eix de les x i de les y en els espectogrames no és igual a la d'una imatge convencional, ja que les x representen el temps i les y la freqüència, s'obtenen millors resultats quan es tracten les dues dimensions per iguals (fent convolucions 2D) que no pas quan es tracten per separat (convolucions 1D). També s'ha observat que la introducció de capes recurrents (RNN) millora considerablement l'aprenentatge, sent el model CRNN el que obté millors resultats. Aquesta millora és raonable, ja que es tracte d'una sèrie temporal una canço, i la LSTM introduïda permet al model captar aquesta temporalitat.
Cal destacar que l'ús de Data Augmentation i la introducció de paràmetres regularitzadors com el Weight Decay milloren el model, com es pot observar amb el model CNN1D, com també ho fa la modificació del Learning Rate. Això porta a pensar que practicant Data Augmentation i realitzant Hyperparameter Search s'obtindria un resultat molt millor amb la CRNN, ja que, d'entre altres coses, es reduiria l'overfitting present.

Pel que fa a les mètriques obtingudes amb el millor model, clarament s'observa que el model és capaç d'apendre. Depenent el model classifica millor un gènere o altre (i això podria portar a plantejar fer un ensemble de models) amb excepció del Pop.  Si es té present que el pop és un gènere pensat per a les masses, i agafa dels altres gèneres musicals, el problema en la seva classificació és prou raonable.   

## Contribuïdors
- Manuel Arnau Fernández -> 1597487@uab.cat
- Pau Fuentes Hernández -> pau.fuentesh@autonoma.cat
- Andrea González Aguilera -> andrea.gonzaleza@autonoma.cat
- Xavier Seminario Monllaó -> 1603853@uab.cat

Xarxes Neuronals i Aprenentatge Profund
Grau de Matemàtica Computacional i Analítica de Dades, 
UAB, 2023
