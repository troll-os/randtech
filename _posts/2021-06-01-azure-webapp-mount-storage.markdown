___
layout: post
title:  "Monter un partage de fichier fichier sur une Web App pour Container (Docker Compose) Azure - Mount a file share on a Web app for Containers (Docker Compose) on Azure"
date:   2021-06-01
categories: azure web app container file share docker
___

_English below_

Alors oui on va me dire "AGNEUGNEUGE ya deja la doc ki di comen fer", certes mais la doc est pas très 
claire sur certains points et comme d'hab pour un truc pourri j'ai du creuser plus de temps qu'il n'en faut.

Pour rappel le lien de la doc : https://docs.microsoft.com/fr-fr/azure/app-service/configure-connect-to-azure-storage?pivots=container-linux#configure-your-app-with-azure-storage

Quand on a une application avec plusieurs conteneurs Docker, orchestrée par un Docker Compose, la doc est pas très bavarde.

En premier il faut avoir créé votre File Share dans votre Compte de stockage, et ensuite ajouter un
`Mappage de point d'accès` dans la configuration de votre app. Le nom que vous donnez à ce mappage va 
vous servir juste après.

Une fois que c'est fait, dans le Yaml de configuration de votre app,
disponible dans le menu `Centre de déploiement` il faut attacher le volume à votre conteneur de cette manière :
```yaml
    volumes:
      - nom-de-votre-montage:/what/ever/path/
```

Et très important dans les variables de votre application n'oubliez pas de mettre
la variable `WEBSITES_ENABLE_APP_SERVICE_STORAGE` sur la valeur `true`.

Je me sers de ce type de configuration pour monter un File Share contenant des certificats que je passe
à une config Nginx.

Cadeau.

_____

So I know you're about to tell me 'HURRR DURR BUR THE DOC SAYS IT BRUH', yeah of course, but it's actually not that 
clear on some points and as usual for something that should be totally transparent I had to waste more time than needed.

The doc as a reminder: https://docs.microsoft.com/en-us/azure/app-service/configure-connect-to-azure-storage?pivots=container-linux#configure-your-app-with-azure-storage

When you have an app with several Docker containers, orchestrated with Docker Compose, the doc is not actually that helpful at first glance.

First things first, you obviously have to create a File Share on your Storage account, and then go back to your Web App 
configuration and add a `Path Mapping`. Note out the name you just used for this cause it's needed right after.

Once you're done, go to the Yaml configuration, in the `Deployment Center` menu and attach the volume to your
container like so :
```yaml
    volumes:
      - name-of-your-mapping-path:/what/ever/path/
```

And very important afterwards, don't forget to turn in your variables the value `` to `true`
