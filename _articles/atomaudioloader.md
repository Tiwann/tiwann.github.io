---
layout: post
title: Atom Audio Loader 
desc: Un plugin C++ pour Unreal Engine qui permet de charger de l'audio en temps réel depuis un fichier ou un buffer.
image: assets/atomaudio/logo.png
---

[SoundWave Reference]:<https://docs.unrealengine.com/5.3/en-US/API/Runtime/Engine/Sound/USoundWave/>
[AudioComponent Reference]:<https://docs.unrealengine.com/5.3/en-US/API/Runtime/Engine/Components/UAudioComponent/>
[Atom Audio Loader Marketplace]:<https://www.unrealengine.com/marketplace/en-US/product/atom-audio-loader>

<img src="../../assets/atomaudio/logo.png"/>
Ce plugin est écrit en C++ et fournit des API simple pour charger de l'audio dans un [`USoundWave`][SoundWave Reference], depuis un fichier ou depuis un buffer. Ce dernier pourra ensuite etre lu par un [`UAudioComponent`][AudioComponent Reference]. Le plugin est disponible sur l'[Unreal Marketplace][Atom Audio Loader Marketplace]

# Formats compatibles
Le plugin supporte ces différents formats audio:<br>
* WAV (.wav)
* MPEG-3 (.mp3)
* OGG Vorbis (.ogg)
* Flac (.flac)

Les données audio, lorsqu'elles sont chargée, sont converties en samples PCM brutes au format 16-bit signé entrelacé.

# Configurer le plugin pour l'utilisation
Pour utiliser le plugin dans votre projet, télécharger le plugin de l'[Unreal Marketplace][Atom Audio Loader Marketplace] et installez le dans votre projet. Veuillez faire attention à faire correspondre les versions d'Unreal avec celles du plugin.<br>
Vous devriez avoir un dossier nommé `UE5-AtomAudio` dans votre répertoire `Plugins`. <br><br>
Ouvrez votre fichier description de projet unreal `MonProjet.uproject` et ajoutez le plugin dans la section `Plugins` s'il n'est pas présent:
```json
"Plugins": [
  {
    "Name": "AtomAudio",
    "Enabled": true,
  }
]
```
<br>

Puis ouvrez votre fichier de build qui correspond au module ou vous voulez utiliser le plugin, par exemple `MonProjet.Build.cs`. <br>
Ajoutez le nom du plugin dans les dépendances de modules comme ceci:
```cs
// MonProjet.Build.cs
PublicDependenciesModuleNames.Add("AtomAudio");
```

Vous pouvez maintenant recompiler votre projet et utiliser [`Atom Audio Loader`][Atom Audio Loader Marketplace]!<br>

# Documentation

Le plugin fourni une `Blueprint Function Library` nommé `UAtomAudioFunctionLibrary` qui permet d'appeler les fonctions de l'API. Ces fonctions sont donc disponibles pour C++ et dans les Blueprint.

## Charger depuis un fichiers

```c++
// ...
#include "AtomAudioFunctionLibrary.h"
static UAtomSoundWave* LoadSoundFromFile(const FString& Filepath, EAtomAudioFileFormat Format, ESoundGroup SoundGroup);
```
`const FString&` Filepath: Chemin du fichier audio a charger. <br>
`EAtomAudioFileFormat` Format: Format du fichier à charger (`EAtomAudioFileFormat::Auto` est recommandé). <br>
Retourne: Un pointeur vers un `UAtomSoundWave` avec l'audio chargé si succès. Vaut `nullptr` si échec. <br>
<img src="../../assets/atomaudio/Marketplace01.png"/>

## Charger depuis un buffer

```c++
// ...
#include "AtomAudioFunctionLibrary.h"
static UAtomSoundWave* LoadSoundFromBuffer(const TArray<uint8>& RawFile, EAtomAudioFileFormat Format, ESoundGroup SoundGroup);
```
`const TArray<uint8>&` RawFile: Reference vers le buffer. <br>
`EAtomAudioFileFormat` Format: Format des données du buffer (`EAtomAudioFileFormat::Auto` est recommandé). <br>
Retourne: Un pointeur vers un `UAtomSoundWave` avec l'audio chargé si succès. Vaut `nullptr` si échec. <br>
<img src="../../assets/atomaudio/Marketplace02.png"/>

## Récupérer les samples PCM d'un `USoundWave`

```c++
// ...
#include "AtomAudioFunctionLibrary.h"
static void GetAudioPCMSamples(USoundWave* InAudio, TArray<float>& OutSamplesBuffer);
```
`USoundWave*` InAudio: Référence à un USoundWave. <br>
`TArray<float>&` OutSamplesBuffer: Référence au buffer à remplir avec les données PCM. <br>
