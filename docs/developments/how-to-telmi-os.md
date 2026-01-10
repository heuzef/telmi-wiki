 # *Telmi Story Teller* 
   *Cette page tente de regrouper des informations sur ce logiciel pour éventuellement contribuer à son développement, favoriser son portage sur d'autres plateformes..*

## Description 
  **Telmi OS**, ou **Telmi Story Teller**, est disponible ici : https://github.com/DantSu/Telmi-story-teller
  
  Telmi-Story-Teller est avant tout un lecteur audio, qui permet de parcourir une bibliothèque d'histoires, et d'entrer dans les packs d'histoires.
  
  Il a trois modes de fonctionnement: 
 * conteuse classique, le mode de base pour les histoires, et histoires interactives, jeux, quiz...
 * nuit, pour des histoires sans interruptions.
 * musique, un lecteur mp3 pour enfant

  Telmi-OS a été conçu pour remplacer l'OS des consoles de retro-gaming Miyoo mini et Miyoo mini plus, fonctionnant sous Linux ( OnionOS  https://github.com/OnionUI/Onion) 
  TelmiOS est écrit en C
  
 https://github.com/DantSu/Telmi-story-teller/tree/main/src/storyTeller
  
  Il comporte donc un lecteur, la navigation et une gestion des formats d'histoires interactives ( inventaires, etc..).

 * **Le lecteur:**
 
   StoryTeller utilise "musicplayer" le lecteur embarque dans onionOS.
   Base sur WIP voir src/storyTeller/music_player.h
 * **La selection des histoires:**
 
   Voir src/storyTeller/app_selector.h
   
    WIP
 * **Le format Studio et Telmi:** 
 
   La partie du code peut etre la plus importante, la lecture du fichier Json de l'histoire et la gestion du deroule de celle-ci..
   Voir src/storyTeller/stories_reader.h
   
    WIP
## Architecture Miyoo/ Onion OS

La Miyoo mini est basée sur l'architecture **ARMv7** ( 32bits)

WIP

Sous Onion OS, l'affichage est géré par la bibliothèque SDL (v2) 

WIP

## Développement

### Lancer une session SSH

WIP

### Telecharger les outils
Une toolchain est disponible ici https://github.com/shauninman/union-miyoomini-toolchain

Une procédure est également disponible dans la documentation OnionOs https://onionui.github.io/docs/dev/setup

## Compilation pour la Miyoo

WIP

## Compilation pour une VM
WiP
