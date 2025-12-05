# Installation manuelle de TelmiOS sur Windows

Pour s'assurer du bon fonctionnement du logiciel, il faudra formater cette carte au format "FAT32" grâce au logiciel RUFUS (car Windows n'autorise pas le formatage FAT32 pour les carte supérieure à 32Go).

* [Télécharger Rufus (rufus-x.xx.exe)](https://github.com/pbatard/rufus/releases/latest)

Démarrer Rufus, séléctionner le carte SD. Enfin lancer le formatage en FAT32 avec les paramètres suivants :

![](assets/img/rufus.png)


Bravo, vous avez une carte SD toute prête, il ne reste plus qu'à copier les répertoires et fichiers de TelmiOS dessus !

La dernière version est disponible [ICI](https://github.com/DantSu/Telmi-story-teller/releases/latest), récupérez le fichier ZIP complet, et copier son contenu sur la carte SD.

> L'autorun de Rufus doit être écrasé par celui de Telmi.

> Assurez-vous bien que les repertoires et fichiers cachés soient bien copiés (`.tmp_update, etc ...`)