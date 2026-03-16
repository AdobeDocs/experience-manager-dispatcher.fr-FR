---
title: Amélioration de l’ETag Dispatcher pour la validation du réseau CDN
description: Disponibilité, statut de prise en charge et comportement de INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT dans AEM as a Cloud Service.
source-git-commit: ac0fafd060643903735ff565072ef2c5bee970be
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---

# Amélioration de l’ETag Dispatcher pour la validation du réseau CDN

## Vue d’ensemble

L’indicateur `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` permet à Dispatcher d’évaluer l’en-tête de requête `If-None-Match` sur les accès au cache. Lorsque la valeur du `If-None-Match` entrant correspond au `ETag` mis en cache, Dispatcher peut renvoyer des `304 Not Modified` au lieu de `200 OK`.

Ce comportement est conçu pour réduire les transferts de payload inutiles entre le réseau CDN et Dispatcher et augmenter l’efficacité du cache conditionnel.

## Disponibilité

- Version de Dispatcher : `2.0.264`
- build AEM SDK : `aem-sdk-2026.2.24464.20260214T050318Z-260100`

## Prise en charge d’AEM as a Cloud Service

Dans AEM as a Cloud Service, cette fonctionnalité est prise en charge pour l’utilisation par le client.

Les clients peuvent l’activer en définissant la variable d’environnement `INTERNAL_AEM_DISPATCHER_ETAG_ENHANCEMENT` dans Cloud Manager. Adobe peut également l’activer au nom du client, si nécessaire.

Lorsque cette option est activée, et lorsque le réseau CDN envoie des `If-None-Match` et que le `ETag` approprié est présent dans le cache de Dispatcher, des taux de réponse `304` plus élevés entre le réseau CDN et Dispatcher sont attendus. Cette augmentation est le résultat escompté.

## Exemple de configuration (en-tête ETag du cache)

Pour que cette amélioration soit efficace, assurez-vous que Dispatcher met en cache l’en-tête de réponse `ETag` et que votre serveur web est configuré pour éviter de générer des ETags basés sur le système de fichiers.

Exemple `dispatcher.any` section de cache :

```text
/cache {
  /headers {
    "Cache-Control"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "ETag"
  }
}
```

Exemple de directive Apache dans le contexte vhost de Dispatcher :

```apache
FileETag none
```

Pour obtenir des conseils de mise en cache des en-têtes de base, voir [Mise en cache des en-têtes de réponse HTTP](dispatcher-configuration.md#caching-http-response-headers).

## Exemple de validation

Après avoir activé la variable d’environnement et déployé les modifications de configuration :

1. Demandez une seule fois de réchauffer le cache et de capturer le `ETag` renvoyé.
1. Effectuez une nouvelle demande avec `If-None-Match: <etag-value>`.
1. Confirmer Dispatcher renvoie `304 Not Modified` pour les flux de revalidation d’accès au cache.

## Référence publique (comportement associé)

Pour obtenir des conseils de base destinés aux clients sur la mise en cache des en-têtes et la gestion des `ETag` dans Dispatcher, consultez :

- [Configuration de Dispatcher - Mise en cache des en-têtes de réponse HTTP](https://experienceleague.adobe.com/fr/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration#caching-http-response-headers)

« Cette fonctionnalité est disponible dans Dispatcher `2.0.264` (AEM SDK `2026.2.24464`). Lorsqu’il est activé, Dispatcher peut valider les `If-None-Match` par rapport aux valeurs `ETag` mises en cache et renvoyer des `304 Not Modified` sur les accès au cache. Dans AEM as a Cloud Service, cela est pris en charge et peut être activé via la configuration de l’environnement Cloud Manager. »
