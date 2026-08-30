# Créer son store Telmi

Cette documentation explique en détail comment mettre en production son store Telmi pour héberger des packs d'histoire.

Elle s'adresse donc à vous si :

* Vous avez la volonté de gérer et maintenir un store Telmi.
* Vous possédez un compte Github et vous savez comment l'utiliser.

Vous êtes partant ? Super ! N'hésitez pas à demander de l'aide à la communauté Telmi !

## Structure d'un store Telmi

Un store Telmi est basiquement un fichier JSON qui est appelé sur le logiciel Telmi-Sync, ce dernier contient toutes les informations des packs d'histoire. Il est resynchronisé à chaque lancement du logiciel. Votre rôle est donc de faire en sorte que ce fichier JSON soit régulièrement mis à jour et publiquement accessible. Pour cela, un seul service est exploité : Github. Il héberge les histoires et leurs informations, met à disposition le fichier JSON du store via GIST, et exécute quotidiennement, grâce à Github Actions, le script qui régénère ce fichier.

> Cette procédure reposait auparavant sur le service Deno, qui ne propose plus de fonction de synchronisation périodique (CRON).

## Notre store exemple

> ⚠️ Dans la suite de cette documentation, c'est le store `telmi-store-en` qui sera pris en exemple, remplacez donc **systématiquement** ce dernier par votre propre nom de Store ⚠️

## Création d'une organisation Github

Identifiez-vous sur Github puis débutez la création d'une organisation avec le nom de votre store.

![](assets/img/github_01.png)


![](assets/img/github_02.png)

Vous pouvez également ajouter quelques éminents membres de la communauté Telmi en soutient 🤪

![](assets/img/github_03.png)

Commencez à personaliser l'organisation : 

* Nom : `Telmi Store EN`
* Une description
* Le lien vers `https://telmi.fr`
* Une icône personnalisée, vous pouvez utiliser [ce template XCF](https://github.com/heuzef/telmi-wiki/raw/refs/heads/gh-pages/stores/assets/files/telmi-icon-flag.xcf) avec [GIMP](https://www.gimp.org) si besoin pour créer votre icône.

![](assets/img/github_04.png)

Votre organisation est prête, il est temps d'initialiser le premier dépôt `.github`, c'est un dépôt de configuration spécial.

![](assets/img/github_05.png)

Ajoutez ce dernier en accès public.

![](assets/img/github_06.png)

Ce store doit être initialisé avec quelques fichiers, dossier et surtout la bannière traduite dans votre langue. Ces archives contiennent également le mécanisme de synchronisation (`.github/workflows/sync-store.yml` et `.github/scripts/sync-store.mjs`), que nous configurerons plus loin. Pour faire ceci, reprenez la structure des dépôts existants, par exemple : 

- [https://github.com/telmi-store/.github/archive/refs/heads/main.zip](https://github.com/telmi-store/.github/archive/refs/heads/main.zip)
- [https://github.com/telmi-store-en/.github/archive/refs/heads/main.zip](https://github.com/telmi-store-en/.github/archive/refs/heads/main.zip)
- [https://github.com/telmi-store-cn/.github/archive/refs/heads/main.zip](https://github.com/telmi-store-cn/.github/archive/refs/heads/main.zip)

etc ...

Pour concevoir la bannière, vous pouvez utiliser [ce template XCF](https://github.com/heuzef/telmi-wiki/raw/refs/heads/gh-pages/stores/assets/files/banner-telmi.xcf) avec [GIMP](https://www.gimp.org) si besoin pour la concevoir.

![](assets/img/gimp_01.png)

![](assets/img/gimp_02.png)

Voici quelques instructions pour vous aidez à initialiser ce dépôt :

```
# Récupérer le dépôt vide sur en local
git clone git@github.com:telmi-store-en/.github.git

# Ajoutez-y vos fichiers
mv -v mes-fichiers/* .github/

# Poussez les modifications
cd .github
git add --all
git commit -m "Init repo"
git branch -M main
git remote add origin git@github.com:telmi-store-en/.github.git
git push -u origin main
```

Une fois votre dépôt ``.github`` prêt, nous pouvons passer à la mise en place du fichier GIST.

## Mise en place d'un GIST

Github GIST est la fonctionnalité qui rendra votre fichier JSON visible publiquement. Rendez-vous sur [https://gist.github.com](https://gist.github.com) pour commencer à le créer en cliquant sur **Create secret gist**. Nommez-le puis ajouter un simple point pour l'initialiser.

![](assets/img/gist_01.png)

Un fichier `gistfile1.txt` est créé, renommez-le avec un format standardisé comme par exemple `telmi-interactive-en.json`.

![](assets/img/gist_02.png)

![](assets/img/gist_03.png)

![](assets/img/gist_04.png)

C'est prêt ! Récupérez l'URL publique en cliquant sur **Share**.

![](assets/img/gist_05.png)

Votre lien URL ressemblera à quelque chose comme ceci :

`https://gist.github.com/heuzef/c2da96666a3a84397f19576d94d15a57`

Ce lien comprend l'ID de votre GIST (ici `c2da96666a3a84397f19576d94d15a57` dans cet exemple), notez-le précieusement.

Ainsi, le lien officiel de votre nouveau store sera donc structuré ainsi :

`https://gist.githubusercontent.com/heuzef/c2da96666a3a84397f19576d94d15a57/raw/telmi-interactive-en.json`

Adaptez ce lien avec votre ID GIST et nom du fichier, vous devriez ainsi pouvoir consulter votre fichier JSON (qui ne contient qu'un point à ce stade). C'est également ce lien, que vous pourrez ajouter sur Telmi-Sync.

C'est tout bon ? Super ! Nous allons pouvoir configurer Github Actions, qui s'occupera de mettre à jour quotidiennement ce fichier JSON. 

Mais avant cela, il faut lui donner l'autorisation de le faire, nous allons donc créer un Token d'accès dédié. Pour cela, accéder à la gestion de vos Tokens.

![](assets/img/gist_06.png)

![](assets/img/gist_07.png)

![](assets/img/gist_08.png)

Vous pouvez directement commencer la création de votre Token via ce lien : [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new)

Donnez-lui un nom parlant, par exemple `telmi-store-gists` et surtout spécifiez bien que ce dernier **n'a pas de date d'expiration**.

Finalement, pour les permissions (Select scopes), spécifiez uniquement l'accès à **gist**.

> Sachez qu'un token portant le scope `gist` donne accès en écriture à **tous les gists** de votre compte, y compris les gists secrets : il n'est pas possible de le restreindre à un seul gist. Si vous préférez, un *fine-grained token* limité à la permission de compte `Gists: read and write` fonctionne également, avec l'avantage de porter une date d'expiration explicite.

![](assets/img/gist_09.png)

À la création, le Token apparaîtra en clair, **notez-le précieusement**, car vous n'aurez plus accès à ce dernier après. 

Votre token ressemble à ceci :

`github_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

**Ne partagez jamais ce dernier à qui que ce soit.**

## Configuration de la synchronisation avec Github Actions

[Github Actions](https://docs.github.com/actions) est le service d'automatisation intégré à Github. Nous allons l'exploiter car il dispose d'une fonction [CRON](https://fr.wikipedia.org/wiki/Cron), ce qui nous sera fort utile pour exécuter quotidiennement un script qui va :

* Parser les différents dépôts de notre organisation sur Github
* En extraire toutes les informations des packs d'histoire
* Générer le fichier JSON de notre store
* Pousser ce dernier sur GIST pour forcer son actualisation

L'avantage par rapport à la précédente méthode est qu'il n'y a plus aucun service tiers à gérer : tout se passe dans le dépôt `.github` de votre organisation, et rien n'est à installer sur votre machine.

Deux fichiers, déjà présents dans les archives téléchargées plus haut, s'en chargent :

```
.github
├── scripts
│   └── sync-store.mjs      # génère le JSON et le pousse sur le GIST
└── workflows
    └── sync-store.yml      # déclenche le script tous les jours
```

### Enregistrer le Token

Le Token créé à l'étape précédente ne doit **jamais** être écrit dans un fichier du dépôt : il serait alors visible publiquement, et Github le révoquerait automatiquement. Il se range dans les secrets du dépôt.

Rendez-vous dans votre dépôt `.github`, puis **Settings** → **Secrets and variables** → **Actions** → **New repository secret**.

* Name : `GIST_TOKEN`
* Secret : votre Token

> Attention à ne pas coller d'espace ni de retour à la ligne avec le Token, c'est l'erreur la plus fréquente. Elle se manifeste plus tard par un échec `Gist update failed: 401 Unauthorized`.

Prenez bien soin de créer un secret **de dépôt** et non un secret d'organisation : un secret d'organisation rendu visible par tous les dépôts serait lisible depuis n'importe quel dépôt de pack d'histoire.

### Adapter le script à votre store

Ouvrez `.github/scripts/sync-store.mjs`, tout ce qui vous concerne tient dans le bloc de configuration en début de fichier :

```
const config = {
    org: 'telmi-store-en',
    gistId: 'c2da96666a3a84397f19576d94d15a57',
    gistFilename: 'telmi-interactive-en.json',
    history: true,
    banner: {
        image: 'https://raw.githubusercontent.com/telmi-store-en/.github/main/profile/banner-telmi.jpg',
        background: '#2e144b',
        link: 'https://discord.gg/ZTA5FyERbg'
    }
}
```

| Clé | À renseigner avec |
| --- | --- |
| `org` | Le nom de votre organisation Github |
| `gistId` | L'ID de votre GIST, noté précieusement à l'étape précédente |
| `gistFilename` | Le nom du fichier de votre GIST |
| `history` | Voir la section « L'historique du store » ci-dessous |
| `banner` | L'image, la couleur de fond et le lien de votre bannière |

L'horaire de la synchronisation se règle de son côté dans `.github/workflows/sync-store.yml` :

```
  schedule:
    - cron: '37 4 * * *'
```

Ce format est celui de CRON, et l'heure est exprimée en **UTC**. Évitez de programmer la tâche pile à l'heure ronde (`0 4 * * *`) : c'est le moment où Github Actions est le plus chargé, et une tâche planifiée peut alors être retardée, voire purement et simplement abandonnée.

Il ne reste qu'à pousser vos modifications :

```
git add --all
git commit -m "Configure the store synchronisation"
git push
```

> Le workflow doit impérativement se trouver sur la branche par défaut de votre dépôt (`main`), car Github n'exécute les tâches planifiées que depuis celle-ci.

### Vérifier que tout fonctionne

Inutile d'attendre le lendemain, la synchronisation se déclenche à la demande. Dans votre dépôt `.github`, ouvrez l'onglet **Actions**, sélectionnez le workflow **Sync store**, puis **Run workflow**.

La case **dry run** permet un essai à blanc : le fichier JSON est généré et le résumé d'exécution vous montre ce qu'il contient, mais rien n'est publié. Pratique pour vérifier votre configuration sans risque.

Une fois l'exécution terminée, cliquez dessus pour consulter son résumé : vous y trouverez la liste des packs publiés, ainsi que les éventuels avertissements concernant les dépôts ignorés.

Rechargez enfin l'URL de votre GIST, votre fichier JSON doit désormais contenir vos packs d'histoire.

### L'historique du store

À chaque exécution, le workflow conserve dans votre dépôt une copie datée du store :

```
store
├── CHANGELOG.md                 # les évolutions notables, en clair
└── telmi-interactive-en.json    # copie exacte de ce qui est publié sur le GIST
```

Le `CHANGELOG.md` ne retient que les changements qui comptent (ajout, retrait, nouvelle version d'un pack) et ignore la simple progression des compteurs de téléchargement. Vous disposez ainsi d'un historique lisible de votre store, et d'une copie de secours si le GIST venait à disparaître.

Ce mécanisme remplit un second rôle, moins évident mais essentiel : **Github désactive automatiquement les tâches planifiées d'un dépôt public resté 60 jours sans activité**. Comme le workflow commite tous les jours, y compris lorsque rien n'a changé, ce compteur ne s'épuise jamais et votre store continue de se synchroniser.

C'est pourquoi le réglage `history: true` est celui recommandé. Le passer à `false` supprime l'historique, mais vous prive du même coup de cette protection : il faudra alors penser à réactiver le workflow depuis l'onglet **Actions** chaque fois que Github le désactivera.

## Création d'un premier pack d'histoire

> ⚠️ La création des packs d'histoire doit être très rigoureusement gérée : un pack mal configuré est ignoré par la synchronisation et n'apparaîtra tout simplement pas sur votre store ⚠️

Rassurez-vous cependant, un pack mal formé ne met plus l'ensemble du store en péril : il est écarté, signalé en avertissement dans le résumé d'exécution, et les autres packs sont publiés normalement. Pensez donc à consulter ce résumé si l'un de vos packs venait à manquer à l'appel.

Pour que la synchronisation puisse fonctionner, il vous faut initialiser un premier pack d'histoire, afin de voir apparaître ce dernier sur telmi-sync.

Donc c'est parti mon Telmi, retournons sur notre organisation Github, pour créer un nouveau dépôt. Prenez exemple des dépôts d'histoire existants, par exemple : [https://github.com/orgs/telmi-store/repositories](https://github.com/orgs/telmi-store/repositories)

Chaque dépôt est structuré très précisément, donc soyez très rigoureux, mais globalement, vous y retrouverez la même logique de structure avec quelques fichiers à modifier.

![](assets/img/github_07.png)

Puis, une fois votre dépôt prêt, vous pourrez créer une "Realease" dans le menu du même nom, qui contiendra votre pack d'histoire au format **ZIP**.

* Ajouter un nom avec la nomenclature des noms de pack (âge, titre, langue)
* Créer un tag ``latest`` pour cette release
* Ajouter la description, attention au format du contenu, c'est ce dernier qui est analysé sur le store
* Ajouter enfin le pack d'histoire au format **ZIP**

![](assets/img/github_08.png)

Pour terminer, n'oubliez pas d'ajouter une description à votre dépôt, avec le nom du pack d'histoire utilisant la nomenclature des noms de packs (âge, titre, langue), autrement, la synchronisation ignorera ce dépôt.

![](assets/img/github_09.png)

C'est tout bon ! Déclenchez une synchronisation manuelle depuis l'onglet **Actions** pour voir apparaître immédiatement votre nouveau pack d'histoire sur Telmi-Sync, sans attendre l'exécution du lendemain. Puis répétez l'opération pour chaque pack d'histoire 💪

---
