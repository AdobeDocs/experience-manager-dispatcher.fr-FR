---
title: Résoudre les problèmes liés à Dispatcher
description: Découvrez comment résoudre les problèmes liés à Dispatcher.
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: ht
source-wordcount: '472'
ht-degree: 100%

---

# Résoudre les problèmes liés à Dispatcher {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Toutefois, la documentation de Dispatcher est incorporée dans la documentation d’AEM. Utilisez toujours la documentation du Dispatcher incluse dans la documentation pour la dernière version d’AEM.
>
>Vous avez peut-être fait l’objet d’une redirection vers cette page si vous avez suivi un lien vers la documentation de Dispatcher. Ce lien est incorporé dans la documentation d’une version précédente d’AEM.

>[!NOTE]
>
>Pour plus d’informations, consultez les sections <!-- URL is 404[Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), -->[Résoudre les problèmes liés à la purge du cache de Dispatcher](https://experienceleague.adobe.com/search.html?lang=fr#q=troubleshooting%20dispatcher%20flushing%20issues&sort=relevancy&f:el_product=[Experience%20Manager]) et les [Questions fréquentes sur les problèmes les plus courants de Dispatcher](dispatcher-faq.md).

## Vérifier la configuration de base {#check-the-basic-configuration}

Comme toujours, les premières étapes consistent à vérifier les éléments de base :

* [Confirmer le fonctionnement de base](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Passez en revue tous les fichiers journaux du serveur web et de Dispatcher. Si nécessaire, augmentez le paramètre `loglevel` utilisé pour la [journalisation](/help/using/dispatcher-configuration.md#logging) de Dispatcher.

* [Vérifiez votre configuration](/help/using/dispatcher-configuration.md) :

   * Disposez-vous de plusieurs instances de Dispatcher ?

      * Avez-vous déterminé quelle est instance de Dispatcher qui gère le site web ou la page en cours de test ?

   * Avez-vous implémenté des filtres ?

      * Ces filtres ont-ils un impact sur le sujet que vous étudiez ?

## Outils de diagnostic IIS {#iis-diagnostic-tools}

IIS propose divers outils de trace, en fonction de la version :

* IIS 6 : vous pouvez télécharger et configurer des outils de diagnostic IIS.
* IIS 7 : le suivi est entièrement intégré.

Ces outils peuvent vous aider à surveiller l’activité.

<!-- Both URLs in this topic 404! >
## IIS and 404 Not Found {#iis-and-not-found}

When using IIS, you might experience `404 Not Found` being returned in various scenarios. If so, see the following Knowledge Base articles.

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Requests to URLs that contain the base path `/bin` return a `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Also check that the Dispatcher cache root and the IIS document root are set to the same directory. -->

## Problèmes lors de la suppression de modèles de workflow {#problems-deleting-workflow-models}

**Symptômes**

Problèmes lors de la tentative de suppression de modèles de workflow lors de l’accès à une instance de création AEM via Dispatcher.

**Étapes à reproduire :**

1. Connectez-vous à votre instance de création (vérifiez que les requêtes sont acheminées via Dispatcher).
1. Créez un processus ; par exemple, en définissant le titre workflowToDelete.
1. Vérifiez que le workflow a bien été créé.
1. Sélectionnez un workflow et cliquez dessus avec le bouton droit, puis cliquez sur **Supprimer**.

1. Cliquez sur **Oui** pour confirmer.
1. Une boîte de message d’erreur s’affiche. Elle affiche les informations suivantes :\
   `ERROR 'Could not delete workflow model!!`.

**Résolution**

Ajoutez les en-têtes suivants à la section `/clientheaders` du fichier `dispatcher.any` :

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interférence avec mod_dir (Apache) {#interference-with-mod-dir-apache}

Ce processus décrit comment Dispatcher interagit avec `mod_dir` dans le serveur web Apache, car cela peut entraîner des effets variés, potentiellement inattendus :

### Apache 1.3 {#apache}

Dans Apache 1.3, `mod_dir` traite chaque requête pour laquelle l’URL est mappée à un répertoire du système de fichiers.

mod_dir :

* redirige la demande vers un fichier `index.html` existant ;
* génère une liste de répertoires.

Lorsque Dispatcher est activé, il traite ces demandes en s’enregistrant lui-même en tant que gestionnaire pour le type de contenu `httpd/unix-directory`.

### Apache 2.x {#apache-x}

Dans Apache 2.x, les choses sont différentes. Un module peut gérer différentes étapes de la requête, telles que la correction de l’URL. `mod_dir` gère cette étape en redirigeant une requête (lorsque l’URL est mappée à un répertoire) vers l’URL comportant un signe `/` ajouté.

Dispatcher n’intercepte pas la correction `mod_dir` mais gère entièrement la requête vers l’URL redirigée (c’est-à-dire avec le signe `/` ajouté). Ce processus peut poser un problème si le serveur distant (par exemple AEM) gère les requêtes envoyées à `/a_path` différemment des requêtes envoyées à `/a_path/` (lorsque `/a_path` est mappé sur un répertoire existant).

Dans cette situation, vous devez effectuer l’une des opérations suivantes :

* Désactivez `mod_dir` pour la sous-arborescence `Directory` ou `Location` gérée par Dispatcher.

* Utilisez `DirectorySlash Off` pour configurer `mod_dir` de manière à ne pas ajouter le signe `/`.
