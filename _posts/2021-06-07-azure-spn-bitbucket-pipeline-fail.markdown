---
layout: post
date:   2021-06-07
title:  "Properly configured Azure SPN fails to deploy through Bitbucket - Pourquoi mon SPN Azure échoue à déployer via Bitbucket"
categories: azure spn service principal name bitbucket pipelines fail
---
_English below_

Utiliser un Principal de service (ou Service Principal Name) fait partie des bonnes pratiques à mettre en place 
sur Azure lorsque l'on souhaite automatiser des processus et identifier des services externes dans notre AD
avec des droits précis et limités.

En l'occurrence nous utilisons des Principaux de service pour authentifier nos pipelines Bitbucket lorsqu'ils
déploient du code sur Azure (Web apps en général).

Il s'avère que nous sommes tombés sur un cas un peu déroutant et qui nous a surpris car nous pensions avoir
déjà déployer sous ce type de configuration.

Le tableau est simple : nous utilisons 2 souscriptions Azure, une qui contient nos RG de Dev et une qui contient
les RG de prod. Avec l'IT de notre boite nous avions pour habitude d'utiliser un SPN pour tous les environnements.
Seulement voilà, mes pipelines se sont mis à planter avec une erreur qui voulait pas dire grand chose :

```text
ERROR: AuthorizationFailed - The client '238874e6...' with object id '238874e...' does not have authorization to perform action 'Microsoft.Web/sites/config/list/action' over scope '/subscriptions/04d9.../resourceGroups/MON-SUPER-RG/providers/Microsoft.Web/sites/la-web-app-de-prod/config/publishingcredentials' or the scope is invalid. If access was recently granted, please refresh your credentials.
ERROR: AuthorizationFailed - The client '238874e6...' with object id '238874e...' does not have authorization to perform action 'Microsoft.Web/sites/publishxml/action' over scope '/subscriptions/04d9.../resourceGroups/MON-SUPER-RG/providers/Microsoft.Web/sites/la-web-app-de-prod' or the scope is invalid. If access was recently granted, please refresh your credentials.
```

En gros le SPN n'avait pas les droits pour publier... Ce qui était vrai mais dur à dire à première vue. On a parcouru et reparcouru les affectations de rôles sur le RG et les ressources
concenées et notre SPN était bien Contributeur dans tous les cas...

En fait ce qu'on a compris après avoir tenté "au pif" de récréer un SPN spécialement pour le RG                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 

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

And very important afterwards, don't forget to turn in your variables the value `WEBSITES_ENABLE_APP_SERVICE_STORAGE` to `true`
