---
layout: post
title: libbfbmp 
desc: Une bibliothèque C qui propose une API pour lire et écrire des fichier .bfbmp (Binary Format Beatmap)
---

# Introduction
Le format de fichier .bfbmp (pour Binary Format Beatmap) est un format open source que je développe qui contient des informations de beatmaps pour des jeux de rhythme. Le format est pensé pour etre compact et interchangeable. Un fichier contient les métadonnées, une image, un son, et les données de niveaux. Un fichier peur contenir plusieurs niveaux de difficultés ou des versions alternatives. Comme il s'agit d'un format binaire les données sont illisibles par un humain et donc rend les modifications difficiles sans passer par l'api.<br/>
Plus d'informations sur la page [github][libbfbmplink]
-------------------------------------------------------
# Comment utiliser
Pour creer une beatmap et ecrire des données dedans:
```c++
#include <bfbmp/bfbmp.h>

int main(void)
{
    // Initialiser une structure beatmap
    bfbmp_beatmap_t beatmap = bfbmp_beatmap_create();
    bfbmp_beatmap_set_song_name(&beatmap, "The Song Name");
    bfbmp_beatmap_set_sub_name(&beatmap, "feat. some artist");
    bfbmp_beatmap_set_author_name(&beatmap, "The author");
    bfbmp_beatmap_set_mapper_name(&beatmap, "Map creator");
    beatmap.metadata.beats_per_minute = 160.0f;
    beatmap.metadata.beats_per_measure = 4;
    beatmap.metadata.start_offset = 0.0f;

    // Creer une structure level et ajouter des notes
    bfbmp_level_t level = bfbmp_level_create_with_name("Level Name");
    level.scroll_speed = 1.0f;

    bfbmp_note_t note;
    note.duration = 1.0f;
    note.position = 1.0f;
    note.type = bfbmp_note_normal;
    bfbmp_vector_note_push(&level.notes, note);

    // Ajouter le level a la beatmap
    bfbmp_vector_level_push(&beatmap.game_data, level);
    
    // Charger une image et un son
    bfbmp_beatmap_load_image(&beatmap, "Path/To/Image/File");
    bfbmp_beatmap_load_sound(&beatmap, "Path/To/Audio/File");

    // Et écrire le fichier
    const char* filepath = "Path/To/OutputFile.bfbmp";
    const uint8_t result = bfbmp_beatmap_encode_file(&beatmap, filepath, BFBMP_FALSE);
    if(result != BFBMP_SUCCESS) fprintf(stderr, "Failed to write beatmap to file!");

    // Toujours penser a liberer la mémoire
    bfbmp_beatmap_free(&beatmap);
}
```

Pour lire une beatmap:
```c++
#include <bfbmp/bfbmp.h>

int main(void)
{
    bfbmp_beatmap_t beatmap = bfbmp_beatmap_create();
    const char* filepath = "Path/To/File.bfbmp";
    bfbmp_beatmap_decode_file(&beatmap, filepath);
}
```

## Prise en charge des erreurs
Chaque fonction de l'api return un `uint8_t` interprété comme un booléen. Ça valeur peur être `BFBMP_SUCCESS` or `BFBMP_FAIL`. C'est donc à l'utilisateur de traiter les erreurs

```c++
#include <stdio.h>
#include <bfbmp/bfbmp.h>

int main(void)
{
    bfbmp_beatmap_t beatmap = bfbmp_beatmap_create();
    const char* filepath = "Path/To/File.bfbmp";
    uint8_t result = bfbmp_beatmap_decode_file(&beatmap, filepath);
    if(result != BFBMP_SUCCESS) 
    { 
        fprintf(stderr, "Failed to decode beatmap!");
        return -1;
    }
    return 0;
}
```


## C++ Wrapper
Un header C++ `bfbmp.hpp` est donné dans les `include`

```c++
#include <bfbmp/bfbmp.hpp>
#include <iostream>

int main()
{
    const std::string filepath = "Path/To/File.bfbmp";
    bfbmp::beatmap beatmap;
    if(!beatmap.decode(filepath))
    {
        std::cerr << "Failed to decode beatmap!\n;
        return -1;
    }
    return 0;
}
```

[libbfbmplink]:<https://github.com/Tiwann/libbfbmp.git>
[gitlink]:<https://git-scm.com/downloads>
[premakelink]:<https://premake.github.io/>