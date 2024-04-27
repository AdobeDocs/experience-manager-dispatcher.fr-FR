---
title: Liste de contrôle de sécurité de Dispatcher
description: Liste de contrôle de sécurité, qui doit être renseignée avant la mise en production.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: tm+mt
source-wordcount: '591'
ht-degree: 56%

---

# Liste de contrôle de sécurité de Dispatcher{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe vous recommande de remplir la liste de contrôle suivante avant de passer en production.

>[!CAUTION]
>
>Remplissez la liste de contrôle de sécurité de votre version d’AEM avant la mise en ligne. Voir la [Documentation Adobe Experience Manager](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist).

## Utiliser la version la plus récente de Dispatcher {#use-the-latest-version-of-dispatcher}

Installez la dernière version disponible pour votre plateforme. Veillez à mettre à niveau votre instance de Dispatcher afin d’utiliser la dernière version pour tirer parti des améliorations apportées aux produits et à la sécurité. Voir [Installer Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>Vérifiez la version actuelle de votre installation de Dispatcher en consultant le fichier journal de Dispatcher.
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>Pour trouver le fichier journal, examinez la configuration de Dispatcher dans votre `httpd.conf`.

## Restreindre le nombre de clients qui peuvent vider le cache {#restrict-clients-that-can-flush-your-cache}

Adobe recommande de [limiter les clients qui peuvent vider la mémoire cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Activer le protocole HTTPS pour la sécurité des couches de transfert {#enable-https-for-transport-layer-security}

Adobe recommande d’activer la couche de transfert HTTPS sur les instances de création et de publication.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Restreindre l’accès {#restrict-access}

Lors de la configuration de Dispatcher, limitez autant que possible l’accès externe. Voir [Exemple de section /filter](dispatcher-configuration.md#main-pars_184_1_title) dans la documentation de Dispatcher.

## S’assurer que l’accès aux URL d’administration est refusé  {#make-sure-access-to-administrative-urls-is-denied}

Assurez-vous d’utiliser des filtres pour bloquer l’accès externe aux URL d’administration, par exemple la console web.

Voir [Test de la sécurité de Dispatcher](dispatcher-configuration.md#testing-dispatcher-security) pour une liste d’URL qui doivent être bloquées.

## Utiliser les listes autorisées plutôt que les listes bloquées {#use-allowlists-instead-of-blocklists}

Les listes autorisées sont le meilleur moyen d’assurer un contrôle d’accès puisque, de fait, toutes les requêtes d’accès doivent être refusées, à moins qu’elles ne figurent explicitement sur ces listes. Ce modèle offre un contrôle plus restrictif des nouvelles requêtes qui n’ont peut-être pas encore été testées ou envisagées au cours d’une étape de configuration spécifique.

## Exécuter Dispatcher avec une personne dédiée utilisant le système {#run-dispatcher-with-a-dedicated-system-user}

Lors de la configuration de Dispatcher, vous devez vous assurer que le serveur web est exécuté par un utilisateur dédié disposant de privilèges limités. Il est recommandé d’accorder uniquement l’accès en écriture au dossier du cache de Dispatcher.

En outre, les utilisateurs d&#39;IIS doivent configurer leur site web comme suit :

1. Dans le paramètre de chemin physique de votre site web, sélectionnez **Se connecter en tant qu’utilisateur ou utilisatrice spécifique**.
1. Définissez l’utilisateur ou l’utilisatrice.

## Prévention des attaques par déni de service (DoS)  {#prevent-denial-of-service-dos-attacks}

Une attaque par déni de service (DoS) est une tentative de rendre une ressource informatique indisponible à ses utilisateurs et utilisatrices ciblés.

Au niveau de Dispatcher, il existe [deux méthodes de configuration pour empêcher les attaques DoS ;](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/configure-aem-dispatcher-to-prevent-dos-attacks-aem-community/m-p/447780).

* Utilisez le module mod_rewrite (par exemple, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) pour effectuer des validations d’URL (si les règles de modèle d’URL ne sont pas trop complexes).

* Empêchez Dispatcher de mettre en cache les URL comportant des extensions douteuses à l’aide de [filtres](dispatcher-configuration.md#configuring-access-to-conten-tfilter).\
   Par exemple, modifiez les règles de mise en cache afin de limiter la mise en cache des types MIME prévus, par exemple :

   * `.html`
   * `.jpg`
   * `.gif`
   * `.swf`
   * `.js`
   * `.doc`
   * `.pdf`
   * `.ppt`

  Un exemple de fichier de configuration peut être consulté pour [limiter l’accès externe](#restrict-access). Il comprend les limitations pour les types MIME.

Pour activer la fonctionnalité complète sur les instances de publication en toute sécurité, configurez les filtres pour empêcher l’accès aux nœuds suivants :

* `/etc/`
* `/libs/`

Ensuite, configurez les filtres pour accorder l’accès aux chemins d’accès des nœuds suivants :

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS et JSON)
* `/libs/cq/security/userinfo.json` (Informations sur les utilisateurs CQ)
* `/libs/granite/security/currentuser.json` (**les données ne doivent pas être mises en cache**)

* `/libs/cq/i18n/*` (Internalisation)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configurer Dispatcher pour empêcher les attaques CSRF {#configure-dispatcher-to-prevent-csrf-attacks}

AEM fournit un [framework](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps) visant à empêcher les attaques CSRF. Pour utiliser correctement cette structure, vous devez placer sur la liste autorisée la prise en charge des jetons CSRF dans Dispatcher.
<!-- OLD URL ABOVE USED TO BE https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#verification-steps -->
Pour ce faire, procédez comme suit :

1. Créez un filtre pour autoriser le chemin d’accès `/libs/granite/csrf/token.json` ;
1. Ajoutez l’en-tête `CSRF-Token` à la section `clientheaders` de la configuration Dispatcher.

## Prévention du détournement de clic {#prevent-clickjacking}

Pour empêcher le détournement de clic, Adobe recommande de configurer votre serveur web afin que l’en-tête HTTP `X-FRAME-OPTIONS` soit défini sur `SAMEORIGIN`.

Pour plus d’informations sur le détournement de clic, consultez le site de l’[OWASP](https://owasp.org/www-community/attacks/Clickjacking).

## Test de pénétration {#perform-a-penetration-test}

Adobe recommande d’effectuer un test de pénétration de votre infrastructure AEM avant de passer en production.
