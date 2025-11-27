# Ouvrir un store international Telmi

Cette documentation explique en détail comment mettre en production un store Telmi pour héberger des packs d'histoire dans une langue étrangère précise.

Elle s'adresse donc à vous si :
* Vous êtes bilingue.
* Vous avez la volonté de gérer un store Telmi officiel dans une langue étrangère.
* Vous possédez un compte Github et vous savez comment l'utiliser.

Vous êtes partant ? Super ! N'hésitez pas à demander de l'aide à la communauté Telmi !

## Structure d'un store Telmi

Un store Telmi est basiquement un fichier JSON qui est appelé sur le logiciel Telmi-Sync, ce dernier contient toutes les informations des packs d'histoire. Il est resynchronisé à chaque lancement du logiciel. Votre rôle est donc de faire en sorte que ce fichier JSON soit régulièrement mis à jour et publiquement accessible. Pour cela, deux services sont donc exploités : Github et Deno. Github héberge les histoires, les informations et met à disposition le fichier JSON du store. Deno de son côté, sert uniquement à déclencher la mise à jour quotidienne du fichier JSON.

## La langue

Pour commencer, identifiez le code ISO 639-1 du pays concerné par votre langue : https://fr.wikipedia.org/wiki/Liste_des_codes_ISO_639-1
Par exemple "FR", pour la France.

> ⚠️ Dans la suite de cette documentation, c'est le code "EN" qui sera pris en exemple, remplacez donc **systématiquement** ce dernier par votre propre code pays ⚠️

## Création d'une organisation Github

Identifiez-vous sur Github puis débutez la création d'une organisation avec le nom : `telmi-store-en`

![](assets/github_01.png)


![](assets/github_02.png)

Vous pouvez également ajouter quelques éminents membres de la communauté Telmi en soutient 🤪

![](assets/github_03.png)

Commencez à personaliser l'organisation : 

* Nom : ``Telmi Store EN``
* Une description traduite dans votre langue
* Le lien vers https://telmi.fr
* Une icône personnalisée avec le drapeau de votre langue, vous pouvez utiliser [ce template XCF](assets/telmi-icon-flag.xcf) avec [GIMP](https://www.gimp.org) si besoin pour créer votre icône.

![](assets/github_04.png)

Votre organisation est prête, il est temps d'initialiser le premier dépôt ``.github``, c'est un dépôt de configuration spécial.

![](assets/github_05.png)

Ajoutez ce dernier en accès public.

![](assets/github_06.png)

Ce store doit être initialisé avec quelques fichiers, dossier et surtout la bannière traduite dans votre langue. Pour faire ceci, reprenez la structure des dépôts existants, par exemple : 

- https://github.com/telmi-store/.github/archive/refs/heads/main.zip
- https://github.com/telmi-store-en/.github/archive/refs/heads/main.zip
- https://github.com/telmi-store-cn/.github/archive/refs/heads/main.zip

etc ...

Pour concevoir la bannière, vous pouvez utiliser [ce template XCF](assets/banner-telmi.xcf) avec [GIMP](https://www.gimp.org) si besoin pour la concevoir.

![](assets/gimp_01.png)

![](assets/gimp_02.png)

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

Github GIST est la fonctionnalité qui rendra votre fichier JSON visible publiquement. Rendez-vous sur https://gist.github.com pour commencer à le créer en cliquant sur **Create secret gist**. Nommez-le `telmi-store-en`. Ajouter un simple point pour l'initialiser.

![](assets/gist_01.png)

Un fichier `gistfile1.txt` est créé, renommez-le `telmi-interactive-en.json`

![](assets/gist_02.png)

![](assets/gist_03.png)

![](assets/gist_04.png)

C'est prêt ! Récupérez l'URL publique en cliquant sur **Share**.

![](assets/gist_05.png)

Votre lien URL ressemblera à quelque chose comme ceci :

`https://gist.github.com/heuzef/c2da96666a3a84397f19576d94d15a57`

Ce lien comprend l'ID de votre GIST (ici `c2da96666a3a84397f19576d94d15a57` dans cet exemple), notez-le précieusement.

Ainsi, le lien officiel de votre nouveau store sera donc structuré ainsi :

`https://gist.githubusercontent.com/heuzef/c2da96666a3a84397f19576d94d15a57/raw/telmi-interactive-en.json`

Adaptez ce lien avec votre ID GIST et code pays, vous devriez ainsi pouvoir consulter votre fichier JSON (qui ne contient qu'un point à ce stade). C'est également ce lien, que vous pourrez ajouter sur Telmi-Sync.

C'est tout bon ? Super ! Nous allons pouvoir commencer la configuration de Deno, qui s'occupera de mettre à jour quotidiennement ce fichier JSON. 

Mais avant cela, il faut lui donner l'autorisation de le faire, nous allons donc créer un Token d'accès dédié. Pour cela, accéder à la gestion de vos Tokens.

![](assets/gist_06.png)

![](assets/gist_07.png)

![](assets/gist_08.png)

Vous pouvez directement commencer la création de votre Token via ce lien : [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new)

Donnez-lui un nom parlant, par exemple `telmi-store-gists` et surtout spécifiez bien que ce dernier **n'a pas de date d'expiration**.

Finalement, pour les permissions (Select scopes), spécifiez uniquement l'accès à **gist**.

![](assets/gist_09.png)

À la création, le Token apparaîtra en clair, **notez-le précieusement**, car vous n'aurez plus accès à ce dernier après. 

Votre token ressemble à ceci :

`github_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

**Ne partagez jamais ce dernier à qui que ce soit.**

## Configuration du serveur de synchronisation Deno

Deno est un service open-source d'exécution de code Javascript. Nous allons exploiter ce dernier car il dispose d'une fonction [CRON](https://fr.wikipedia.org/wiki/Cron), ce qui nous sera fort utile pour exécuter quotidiennement un script TS qui va :

* Parser les différents dépôts de notre organisation sur Github
* En extraire toutes les informations des packs d'histoire
* Générer le fichier JSON de notre store
* Pousser ce dernier sur GIST pour forcer son actualisation

Vous pouvez donc commencer par vous authentifier (avec votre compte Github) sur https://console.deno.com/login

![](assets/deno_01.png)

Installez ensuite Deno sur votre machine : 

``curl -fsSL https://deno.land/install.sh | sh``

Puis finalement, installez également l'outil de gestion (CLI) qui nous servira à déployer notre projet :

```
deno install -gArf jsr:@deno/deployctl
export PATH="/home/$USER/.deno/bin:$PATH" # Definir la variable PATH pour pouvoir executer deployctl
```

Nous allons à présent initialiser le projet, voici à quoi doit ressembler votre dossier ce travail :

```
telmi-store-en
└── src
    └── main.ts
```

```
mkdir telmi-store-en
mkdir telmi-store-en/src
touch telmi-store-en/src/main.ts
```

Créer votre fichier main.ts :

```
const
strFormat = (str) => str.replace(/[^\u0020-\ucfbf\u000A]+/g, ' ').trim(),
imageExists = (url) => {
    return new Promise((resolve, reject) => {
        fetch(url, { method: 'HEAD' })
        .then((res) => {
            if (res.status !== 200) {
                return resolve(false)
            }
            resolve(true)
        })
        .catch(() => {
            resolve(false)
        })
    })
}


Deno.serve(() => new Response('API d\'histoires', {
    status: 200,
    headers: { 'content-type': 'text/html; charset=utf-8' }
}))

const githubToTelmi = async (gist, filename, repoURI, banner, background, link) => {
    let data = []

    const repos = await (await fetch('https://api.github.com/orgs/' + repoURI + '/repos', { headers: { accept: 'application/json' } })).json()
    for (const repo of repos) {
        repo.thumbnail = 'https://raw.githubusercontent.com/' + repoURI + '/' + repo.name + '/main/thumbnail.jpg'
        if (await imageExists(repo.thumbnail)) {
            repo.releases = await (await fetch(repo.url + '/releases', { headers: { accept: 'application/json' } })).json()
        } else {
            repo.releases = []
        }
    }

    data = repos.reduce(
        (acc, repo) => {
            if (!repo.releases.length || !repo.releases[0].assets.length) {
                return acc
            }

            const title = [...repo.description.matchAll(/^\[([0-9]+)\+](.*)\(([A-Z]+)\)$/g)]

            if (!title.length) {
                return acc
            }

            const url = repo.releases[0].assets.reduce(
                (acc, v) => {
                    if (v.browser_download_url.substring(v.browser_download_url.length - 4) === '.zip') {
                        return v.browser_download_url
                    }
                    return acc
                },
                ''
            )

            if (url === '') {
                return acc
            }

            const downloadCount = repo.releases.reduce(
                (acc, release) => release.assets.reduce((acc, v) => acc + v.download_count, acc),
                                                       0
            )

            const details = repo.releases[0].body.replace('\r', '').split('\n').reduce(
                (acc, line) => {
                    if (line.substring(0, 1) !== '>') {
                        acc.description = acc.description + '\n' + line
                        return acc
                    }
                    const colonPos = line.indexOf(':', 1)
                    if (colonPos === -1) {
                        acc.description = acc.description + '\n' + line
                        return acc
                    }
                    return {
                        ...acc,
                        [line.substring(1, colonPos).trim().toLowerCase()]: line.substring(colonPos + 1).trim()
                    }
                },
                { description: '' }
            )

            return [
                ...acc,
                Object.assign({
                    age: parseInt(strFormat(title[0][1]), 10),
                              title: strFormat(title[0][2]),
                              description: strFormat(details.description),
                              thumbs: {
                                  small: repo.thumbnail,
                                  medium: repo.thumbnail
                              },
                              download: strFormat(url),
                              download_count: downloadCount,
                              awards: details.awards !== undefined ? details.awards.substring(1).split('#').map((v) => strFormat(v.trim())) : [],
                              created_at: repo.releases[repo.releases.length - 1].published_at,
                              updated_at: repo.releases[0].published_at
                },
                details.uuid !== undefined ? { uuid: details.uuid } : null,
                details.author !== undefined ? { author: details.author } : null,
                details.voice !== undefined ? { voice: details.voice } : null,
                details.designer !== undefined ? { designer: details.designer } : null,
                details.publisher !== undefined ? { publisher: details.publisher } : null,
                details.category !== undefined ? { category: details.category } : null,
                details.version !== undefined ? { version: details.version } : null,
                details.license !== undefined ? { license: details.license } : null
                )
            ]
        },
        data
    )

    const
    dateIso = (new Date()).toISOString().split('T'),
    date = dateIso[0] + ' ' + dateIso[1].substring(0, 8)

    await fetch(
        'https://api.github.com/gists/' + gist,
        {
            method: 'PATCH',
            headers: {
                Authorization: 'Bearer github_pat_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
                Accept: 'application/vnd.github+json',
                'X-GitHub-Api-Version': '2022-11-28',
            },
            body: JSON.stringify({
                description: 'Update ' + repoURI + ' (' + date + ')',
                                 files: {
                                     [filename]: { content: JSON.stringify({ banner: { image: banner, background, link }, data }) }
                                 }
            })
        }
    )
}


Deno.cron('telmi-interactive-en', '0 1,13 * * *', async () => {
    await githubToTelmi('c2da96666a3a84397f19576d94d15a57', 'telmi-interactive-en.json', 'telmi-store-en', 'https://raw.githubusercontent.com/telmi-store-en/.github/master/profile/banner-telmi.jpg', '#2e144b', 'https://discord.gg/ZTA5FyERbg')
})
```

**Adaptez ce fichier pour indiquer le Token (Ligne "Authorization"), l'ID GIST et le Code pays sur les dernières lignes du script.**

Maintenant que votre fichier `main.ts` est prêt, nous pouvons effectuer le déploiement sur Deno :

```
cd telmi-store-en
deployctl deploy --prod --project=telmi-store-en --unstable src/main.ts
```

Une demande d'autorisation est demandée pour poursuivre.

![](assets/deno_02.png)

Si le déploiement réussis, vous devriez avoir un service accessible sur un domaine comme par exemple : https://telmi-store-en.deno.dev , mais également un fichier `deno.json` créé par le déploiement.

![](assets/deno_03.png)

Finalement, RDV sur [https://dash.deno.com/projects/telmi-store-en/cron](https://dash.deno.com/projects/telmi-store-en/cron) pour visualiser l'état de l'exécution du script par CRON. Le statut "Last Run" doit indiquer "Succeeded".

## Création d'un premier pack d'histoire

> ⚠️ La création des packs d'histoire doit être très rigoureusement géré, autrement, l'exécution du script quotidien sur Deno échouera si l'un de vos packs d'histoire est mal configuré, pouvant rendre alors votre store hors service ⚠️

Pour que la synchronisation puisse fonctionner, il vous faut initialiser un premier pack d'histoire, afin de voir apparaître ce dernier sur telmi-sync.

Donc c'est parti mon Telmi, retournons sur notre organisation Github, pour créer un nouveau dépôt. Prenez exemple des dépôts d'histoire existants, par exemple : [https://github.com/orgs/telmi-store/repositories](https://github.com/orgs/telmi-store/repositories)

Chaque dépôt est structuré très précisément, donc soyez très rigoureux, mais globalement, vous y retrouverez la même logique de structure avec quelques fichiers à modifier.

![](assets/github_07.png)

Puis, une fois votre dépôt prêt, vous pourrez créer une "Realease" dans le menu du même nom, qui contiendra votre pack d'histoire au format **ZIP**.

* Ajouter un nom avec la nomenclature des noms de pack (âge, titre, langue)
* Créer un tag ``latest`` pour cette release
* Ajouter la description, attention au format du contenu, c'est ce dernier qui est analysé sur le store
* Ajouter enfin le pack d'histoire au format **ZIP**

![](assets/github_08.png)

Pour terminer, n'oubliez pas d'ajouter une description à votre dépôt, avec le nom du pack d'histoire utilisant la nomenclature des noms de pack (âge, titre, langue), autrement, le script planifié par CRON sur Deno sera en échec.

![](assets/github_09.png)

C'est tout bon, vérifiez bien le lendemain que votre nouveau pack d'histoire est correctement visible sur Telmi-Sync, puis répéter l'opération pour chaque pack d'histoire 💪

> ⚠️ Soyez prudent concernant les droits d'auteur, aucun pack d'histoire illégal ne doit être mis à disposition du public ⚠️

---