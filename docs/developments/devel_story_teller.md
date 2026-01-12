
 # *Telmi Story Teller* 
   *Cette page tente de regrouper des informations sur ce logiciel pour éventuellement contribuer à son développement, favoriser son portage sur d'autres plateformes..*

## Description 
  **Telmi OS**, ou **Telmi Story Teller** a été conçu pour remplacer l'OS des consoles de retro-gaming Miyoo mini et Miyoo mini plus, fonctionnant sous Linux ( [OnionOS](https://github.com/OnionUI/Onion)  ) 
  TelmiOS est écrit en C par DantSu sous licence GPLv3, son code est disponible [ici](https://github.com/DantSu/Telmi-story-teller/tree/main/src/storyTeller).
  
  Il se compose de 3 parties principales:

 * **Le lecteur:**
 
   StoryTeller utilise Libmpg123.so.0 une libraire compatible SDL2 pour lire les mp3
   voir src/storyTeller/[music_player.h](https://github.com/DantSu/Telmi-story-teller/blob/main/src/storyTeller/music_player.h)
 * **La sélection des histoires:**
 
   Voir src/storyTeller/[app_selector.h](https://github.com/DantSu/Telmi-story-teller/blob/main/src/storyTeller/app_selector.h)


 * **Le format Studio et Telmi:** 
 La gestion des formats d'histoires interactives ( inventaires, etc..).
 
  La partie du code peut être la plus importante, la lecture du fichier Json de l'histoire et la gestion du déroulé de celle-ci..
  
   Voir src/storyTeller/[stories_reader.h](https://github.com/DantSu/Telmi-story-teller/blob/main/src/storyTeller/stories_reader.h)
   
   Voir aussi [cette documentation ](telmi_story_format.md) (générée par IA)

## Architecture Miyoo/ Onion OS

La Miyoo mini est basée sur l'architecture **ARMv7** ( 32bits)

L'affichage est géré par la bibliothèque SDL (v2) 

WIP

## Développement
Pour toute question concernant le développement de Telmi et de Story Teller, vous êtes bienvenus sur le canal dédié "développement" du [serveur Discord](https://discord.gg/ZTA5FyERbg)! 
### Lancer une session SSH

Installation d'un serveur dropbear pour copier et envoyer des fichiers sur la Miyoo en utilisant la connexion wifi ( permet de gagner du temps en évitant de sortir et rentrer la carte Sd a chaque version).

WIP


### Compilation pour la Miyoo

Voir [Makefile](https://github.com/DantSu/Telmi-story-teller/blob/main/Makefile)

 - Pour faire un build de test


 Tout devrai être automatique:

    make with-toolchain CMD=build

 - Pour créer une nouvelle release avec le .zip et le -update.zip :

*(Penser a supprimer les répertoires : build, cache, release)*

    make with-toolchain CMD=release
Les fichiers zip seront enregistrés dans /release

### Toolchain

Normalement, **vous n'aurez pas à vous en soucier**, le Makefile télécharge automatiquement les outils et lance docker.

La toolchain conseillée pour la compilation est la "miyoo mini toolchain": 
Son nom interne est "arm-linux-gnueabihf" donc compatible ArmHF
 

    TOOLCHAIN := aemiii91/miyoomini-toolchain:latest
    

Une toolchain est aussi disponible ici https://github.com/shauninman/union-miyoomini-toolchain

Une procédure est également disponible dans la documentation OnionOs https://onionui.github.io/docs/dev/setup
