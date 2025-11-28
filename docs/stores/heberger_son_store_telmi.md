# Héberger son Store Telmi

Cette documentation explique en détail comment héberger son store Telmi en auto-hebergement.

Elle s'adresse donc à vous si :

* Vous avez la volonté de gérer et maintenir un store Telmi.
* Vous possédez un serveur.
* Vous savez utiliser Docker.

Le projet **Telmi-Store** initié par Jordan.Deroubaix est disponible ici : [https://github.com/jordanderoubaix/Telmi-store](https://github.com/jordanderoubaix/Telmi-store)

Il est possible d'utiliser une image Docker avec cette configuration Docker-Compose :

```
services:
  telmi-store:
    image: jordanderoubaix/telmi-store:latest
    ports:
      - 80:8888
    environment:
      TARGET_URL: https://stories.domain.tld
      BANNER_IMAGE: https://raw.githubusercontent.com/jordanderoubaix/Telmi-store/refs/heads/main/img/banner-custom-telmi-store.png
      BANNER_LINK: https://discord.gg/ZTA5FyERbg
      BANNER_BACKGROUND: "#2e144b"
      API_KEY: "1234567890"
      REQUEST_SCHEME: https
      LOG_LEVEL: DEBUG
    volumes:
      - /telmiLibrary:/mnt/nfs/telmiLibrary
    restart: unless-stopped
```

Avec cette configuration, l'URL à saisir dans telmi Sync sera : ``https://stories.heuzef.com/store?api_key=1234567890``