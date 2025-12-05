# Installation manuelle de TelmiOS sur Linux

Pour s'assurer du bon fonctionnement du logiciel, il faudra formater cette carte au format "FAT32".

Lancer un terminal puis afficher la liste des disques et leur partitions :

```
fdisk -l
```

Identifiez la carte SD et sa ou ses partitions (quelque chose comme `/dev/sdX`, mais attention **soyez certain de bien repérer votre carte SD avant d'aller plus loin !**).

Démontez la ou les partitions avant de faire les prochaines actions de modification :

```
unmount /dev/sdX
```

On peut partitionner le disque en utilisant fdisk, bien suivre les indications et ne confirmer **que si vous êtes sur(e)s de vous**

```
fdisk /dev/sdX
# Puis choix n ; p ; 1 ; t ; b ; w
```

Maintenant il faut formater la partition créé :

```
mkfs.fat -F32 -n "Telmi" /dev/sdX
```

> Le script de formatage intégré à TelmiSync, pour Linux, réalise justement le formatage en FAT32 avec la commande ``mkfs.fat -F32 -n "$volumeName" "$device"``.

Bravo, vous avez une carte SD toute prête, il ne reste plus qu'à copier les répertoires et fichiers de TelmiOS dessus !

La dernière version est disponible [ICI](https://github.com/DantSu/Telmi-story-teller/releases/latest), récupérez le fichier ZIP complet, et copier son contenu sur la carte SD.

```
wget https://github.com/DantSu/Telmi-story-teller/releases/download/X.X.X/TelmiOS_vX.X.X.zip
unzip TelmiOS_vX.X.X.zip -d /dev/sdX/
ls -a /dev/sdX/
```

> Assurez-vous bien que les repertoires et fichiers cachés soient bien copiés (`.tmp_update, etc ...`)