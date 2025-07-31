---
title: Optimiser les performances de mise en cache d’un site web
description: Découvrez comment concevoir votre site web afin de tirer le meilleur parti des avantages de la mise en cache.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: ht
source-wordcount: '1128'
ht-degree: 100%

---


# Optimiser les performances de mise en cache d’un site web {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Les versions de Dispatcher sont indépendantes d’AEM. Vous avez peut-être fait l’objet d’une redirection vers cette page si vous avez suivi un lien vers la documentation de Dispatcher. Ce lien était incorporé dans la documentation d’une version précédente d’AEM.

Dispatcher propose un certain nombre de mécanismes intégrés que vous pouvez utiliser pour optimiser les performances. Dans cette section, vous apprendrez comment concevoir votre site web afin de tirer le meilleur parti des avantages de la mise en cache.

>[!NOTE]
>
>Il peut être utile de vous rappeler que le dispatcher stocke le cache sur un serveur web standard. Cela signifie que :
>
>* vous pouvez mettre en cache tous les éléments enregistrés en tant que page ou demandés à l’aide d’une URL ;
>* vous ne pouvez pas enregistrer d’autres éléments, tels que des en-têtes HTTP, des cookies, des données de session et des données de formulaire.
>
>En général, de nombreuses stratégies de mise en cache impliquent de sélectionner les URL appropriées et de ne pas s’en tenir à ces données supplémentaires.

## Utiliser un encodage de page cohérent {#using-consistent-page-encoding}

Les en-têtes de requête HTTP ne sont pas mis en cache. Des problèmes peuvent donc survenir si vous stockez des informations de codage de page dans l’en-tête. Dans ce cas, lorsque le Dispatcher diffuse une page du cache, le codage par défaut du serveur web est utilisé pour la page. Deux méthodes permettent d’éviter ce problème :

* Si vous n’utilisez qu’un seul encodage, assurez-vous que le codage utilisé sur le serveur web est identique à celui par défaut du site web AEM.
* Pour définir le codage, utilisez une balise `<META>` dans la section `head` HTML comme dans l’exemple suivant :

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Éviter les paramètres d’URL {#avoid-url-parameters}

Si possible, évitez les paramètres d’URL des pages que vous souhaitez mettre en cache. Par exemple, si vous disposez d’une galerie d’images, l’URL suivante n’est jamais mise en cache (sauf si le Dispatcher est [configuré en conséquence](dispatcher-configuration.md#main-pars_title_24)) :

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Vous pouvez toutefois placer ces paramètres dans l’URL de page, comme suit :

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Cette URL invoque la même page et le même modèle que gallery.html. Dans la définition du modèle, vous pouvez spécifier le script qui effectue le rendu de la page ou vous pouvez utiliser le même script pour toutes les pages.

## Personnalisation par URL  {#customize-by-url}

Si vous autorisez les utilisateurs et utilisatrices à modifier la taille de la police (ou toute autre personnalisation de la disposition), assurez-vous que les différentes personnalisations sont reflétées dans l’URL.

Par exemple, les cookies ne sont pas mis en cache. Par conséquent, si vous stockez la taille de police dans un cookie (ou un mécanisme similaire), celle-ci ne sera pas conservée pour la page mise en cache. Par conséquent, le Dispatcher renvoie aléatoirement des documents de n’importe quelle taille de police.

L’inclusion de la taille de police dans l’URL en tant que sélecteur évite ce problème :

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Pour la plupart des aspects de disposition, il est également possible d’utiliser des feuilles de style, des scripts côté client, ou les deux. L’une et/ou l’autre de ces options fonctionnent bien avec la mise en cache.
>
>Cette méthode est également utile pour une version imprimée. Dans ce cas, vous pouvez également utiliser une URL telle que:
>
>`www.myCompany.com/news/main.print.html`
>
>À l’aide de l’extension métacaractère du script de la définition du modèle, vous pouvez spécifier un script distinct qui effectue le rendu des pages d’impression.

## Invalider des fichiers image utilisés comme titres {#invalidating-image-files-used-as-titles}

Si vous effectuez le rendu de titres de page ou d’un autre texte sous forme d’images, stockez les fichiers afin qu’ils soient supprimés lors de la mise à jour du contenu sur la page :

1. Placez le fichier image dans le même dossier que la page.
1. Utilisez le format d’affectation de nom suivant pour le fichier image :

   `<page file name>.<image file name>`

Par exemple, vous pouvez stocker le titre de la page myPage.html dans le fichier myPage.title.gif. Ce fichier est automatiquement supprimé si la page est mise à jour. Toute modification du titre de la page est donc automatiquement répercutée dans le cache.

>[!NOTE]
>
>Le fichier image n’existe pas nécessairement sur l’instance AEM. Vous pouvez utiliser un script qui crée dynamiquement le fichier image. Dispatcher stocke ensuite le fichier sur le serveur web.

## Invalider des fichiers image utilisés pour la navigation {#invalidating-image-files-used-for-navigation}

Si vous utilisez des images pour les entrées de navigation, la méthode est fondamentalement la même qu’avec les titres, bien qu’un peu plus complexe. Stockez toutes les images de navigation avec les pages cibles. Si vous utilisez deux images pour « normale » et « active », vous pouvez utiliser les scripts suivants :

* Script qui affiche la page, en tant que normale.
* Script qui traite les requêtes `.normal` et renvoie l’image normale.
* Script qui traite les demandes `.active` et renvoie l’image activée.

Il est important que vous créiez ces images avec le même nom que la page, afin de vous assurer qu’une mise à jour du contenu supprime ces images et la page.

Pour les pages qui ne sont pas modifiées, les images restent dans le cache, bien que les pages elles-mêmes soient automatiquement invalidées.

## Personnalisation {#personalization}

Dispatcher ne peut pas mettre en cache les données personnalisées. Il est donc recommandé de limiter la personnalisation aux éléments nécessaires. En voici la raison :

* Si vous utilisez une page de démarrage personnalisable librement, cette page doit être composée à chaque fois qu’un utilisateur ou une utilisatrice la demande.
* Si, en revanche, vous proposez un choix de dix pages de démarrage différentes, vous pouvez mettre en cache chacune d’elles, améliorant ainsi les performances.

>[!NOTE]
>
>Si vous personnalisez chaque page (par exemple en indiquant le nom de l’utilisateur ou de l’utilisatrice dans la barre de titre), vous ne pourrez pas les mettre en cache, et cela peut engendrer un impact significatif sur les performances.
>
>Cependant, si vous le devez, vous pouvez effectuer les opérations suivantes :
>
>* utiliser iFrames pour diviser la page en une partie identique pour tous les utilisateurs et utilisatrices et une partie identique à toutes les pages de l’utilisateur ou de l’utilisatrice. Vous pouvez ensuite mettre en cache ces deux parties ;
>* utiliser du JavaScript côté client pour afficher des informations personnalisées. Cependant, vous devez vous assurer que la page s’affiche toujours correctement si un utilisateur ou une utilisatrice désactive JavaScript.
>

## Connexions persistantes {#sticky-connections}

Les [connexions persistantes](dispatcher.md#TheBenefitsofLoadBalancing) garantissent que les documents d’un utilisateur ou d’une utilisatrice sont tous composés sur le même serveur. Si un utilisateur ou une utilisatrice quitte ce dossier et y revient ultérieurement, la connexion reste établie. Définissez un dossier à même de contenir tous les documents qui nécessitent des connexions persistantes sur le site web. Essayez de ne pas y stocker d’autres documents. Cela a un impact sur l’équilibrage de la charge si vous utilisez des pages personnalisées et des données de session.

## Types MIME {#mime-types}

Un navigateur peut déterminer le type d’un fichier de deux façons différentes :

1. Par son extension (par exemple : .html, .gif et .jpg)
1. Par le type MIME que le serveur envoie avec le fichier.

Pour la plupart des fichiers, le type MIME est implicite dans l’extension de fichier.

1. Par son extension (par exemple : .html, .gif et .jpg)
1. Par le type MIME que le serveur envoie avec le fichier.

Si le nom de fichier ne comporte aucune extension, il s’affiche sous forme de texte brut.

Le type MIME fait partie de l’en-tête HTTP et, en tant que tel, Dispatcher ne le met pas en cache. Votre application AEM peut renvoyer des fichiers qui n’ont pas d’extension de fichier reconnue. Si les fichiers dépendent plutôt du type MIME, ils peuvent s’afficher incorrectement.

Pour vous assurer que les fichiers sont correctement mis en cache, suivez ces instructions :

* Assurez-vous que les fichiers ont toujours l’extension appropriée.
* Évitez les scripts génériques de diffusion de fichiers avec des URL de type : download.jsp?file=2214. Réécrivez le script afin qu’il utilise les URL qui contiennent la spécification du fichier. Dans l’exemple précédent, cela correspond à `download.2214.pdf`.

