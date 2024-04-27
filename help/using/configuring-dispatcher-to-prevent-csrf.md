---
title: Configurer Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery)
description: Découvrez comment configurer AEM Dispatcher pour empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery).
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '217'
ht-degree: 46%

---

# Configurer Dispatcher afin d’empêcher les attaques par falsification de requête intersites (CSRF, Cross Site Request Forgery){#configuring-dispatcher-to-prevent-csrf-attacks}

AEM fournit une infrastructure visant à empêcher les attaques par falsification de requête intersites. Pour utiliser correctement cette structure, apportez les modifications suivantes à votre configuration Dispatcher :

>[!NOTE]
>
>Veillez à mettre à jour les numéros des règles dans les exemples ci-dessous en fonction de votre configuration existante. N’oubliez pas que les dispatchers utilisent la dernière règle correspondante pour accorder une autorisation ou un refus. Par conséquent, placez les règles près du bas de votre liste existante.

1. Dans le `/clientheaders` de votre `author-farm.any` et `publish-farm.any`, ajoutez l’entrée suivante au bas de la liste :\
   `CSRF-Token`
1. Dans la section /filters de votre `author-farm.any` et `publish-farm.any` ou `publish-filters.any` , ajoutez la ligne suivante pour autoriser les requêtes pour `/libs/granite/csrf/token.json` par le biais de Dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Sous , `/cache /rules` de votre `publish-farm.any`, ajoutez une règle pour empêcher Dispatcher de mettre en cache le `token.json` fichier . En règle générale, les auteurs contournent la mise en cache. Il n’est donc pas nécessaire d’ajouter la règle à votre `author-farm.any`.\
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Pour vérifier que la configuration fonctionne, consultez le fichier dispatcher.log en mode de DÉBOGAGE pour contrôler que le fichier token.json n’est ni mis en cache, ni bloqué par des filtres. Des messages de ce type peuvent apparaître :\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Vous pouvez également vérifier que les demandes réussissent dans votre Apache. `access_log`. Les requêtes pour``/libs/granite/csrf/token.json doivent renvoyer le code d’état HTTP 200.
