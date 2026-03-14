# Astuces pour Telmi-Sync

## Sauvegarder le répertoire des données

Si vous souhaitez récupérer et sauvegarder votre configuration Telmi-Sync avec ses bibliothèques sur votre système, il vous faut copier le repertoire de configuration ``.telmi``.

Ce dernier est localisé sur votre système dans le répertoire utilisateur :

* Linux : ``~/.telmi``
* MacOS : ``~/.telmi`` (Pour afficher les fichiers cachés sous mac, utiliser le raccourci clavier Majuscule + Command + Point : `⇧⌘.`)
* Windows : ``C:\Users\<username>\.telmi``


## Comment déplacer le dossier contenant les histoires vers un autre dossier ou disque dur ?

### Windows:

1. Couper et coller le dossier C:\Users\[TON_UTILISATEUR]\.telmi\stories vers le disque ou répertoire que vous souhaitez.
2. Ouvrez un terminal de commande en ADMINISTRATEUR puis exécutez la commande suivante : 
3. `MKLINK /D "C:\Users\[TON_UTILISATEUR]\.telmi\stories" "D:\PATH\VERS\LE\NOUVEL\EMPLACEMENT\DE\STORIES"`

### Linux ou Mac:
1. Créer le répertoire cible, c'est-à-dire l'endroit où l'on veut stocker les données de TelmiSync, par exemple dans `/VOLUMES/foo/telmisync`. Y copier éventuellement l'intégralité du contenu du dossier d'origine `.telmi`, pour ne pas perdre les données existantes (p.ex. histoires déjà téléchargées ou paramètres de TelmiSync).
2. Supprimer le dossier d'origine, par exemple `/Users/iko/.telmi`
3. Dans un terminal, créer un lien symbolique avec cette commande : `ln -s /VOLUMES/foo/telmisync /Users/iko/.telmi`
