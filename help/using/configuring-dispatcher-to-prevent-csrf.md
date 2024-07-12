---
title: Configurer Adobe Experience Manager Dispatcher afin d’empêcher les attaques CSRF
description: Découvrez comment configurer Adobe Experience Manager Dispatcher pour empêcher les attaques cross-site request forgery (CSRF).
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
source-git-commit: 0a1aa854ea286a30c3527be8fc7c0998726a663f
workflow-type: tm+mt
source-wordcount: '235'
ht-degree: 100%

---

# Configurer Adobe Experience Manager Dispatcher afin d’empêcher les attaques CSRF{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM (Adobe Experience Manager) fournit un framework conçu pour empêcher les attaques CSRF. Pour utiliser correctement ce framework, apportez les modifications suivantes à votre configuration de Dispatcher :

>[!NOTE]
>
>Veillez à mettre à jour les numéros des règles dans les exemples ci-dessous en fonction de votre configuration existante. N’oubliez pas que Dispatcher utilise la dernière règle correspondante pour accorder une autorisation ou un refus. Par conséquent, placez les règles au niveau du bas de votre liste existante.

1. Dans la section `/clientheaders` de `author-farm.any` et `publish-farm.any`, ajoutez l’entrée suivante au bas de la liste :\
   `CSRF-Token`
1. Dans la section /filters de vos fichiers `author-farm.any` et `publish-farm.any` ou `publish-filters.any`, ajoutez la ligne suivante afin d’autoriser les requêtes pour `/libs/granite/csrf/token.json` au moyen de Dispatcher.\
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`

1. Dans la section `/cache /rules` de votre fichier `publish-farm.any`, ajoutez une règle permettant d’empêcher Dispatcher de mettre en cache le fichier `token.json`. En général, les auteurs et autrices contournent la mise en cache, de sorte que vous n’avez pas à ajouter de règle au fichier `author-farm.any`.

   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

Pour vérifier que la configuration fonctionne, consultez le fichier dispatcher.log en mode DEBUG (débogage). Cela vous permet de vérifier le fichier `token.json` afin de vous assurer qu’il n’est pas mis en cache ou bloqué par des filtres. Des messages de ce type peuvent apparaître :\
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`\
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

Vous pouvez également vérifier que les demandes réussissent dans le fichier Apache `access_log`. Les requêtes pour ``/libs/granite/csrf/token.json doivent renvoyer le code d’état HTTP 200.
