---
title: Installer Dispatcher
description: Découvrez comment installer le module de Dispatcher sur Microsoft&reg ; serveur d’informations Internet, serveur web Apache et Sun Java &trade ; serveur web-iPlanet.
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
source-git-commit: 9be9f5935c21ebbf211b5da52280a31772993c2e
workflow-type: tm+mt
source-wordcount: '3748'
ht-degree: 83%

---

# Installer Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Utilisez la page [Notes de mises à jour de Dispatcher](release-notes.md) pour obtenir le dernier fichier d’installation de Dispatcher pour votre système d’exploitation et votre serveur web. Les numéros de version de Dispatcher sont indépendants des numéros de version d’Adobe Experience Manager et sont compatibles avec les versions d’Adobe Experience Manager 6.x, 5.x et Adobe CQ 5.x.

>[!NOTE]
>
>Adobe Experience Manager 6.5 nécessite la version 4.3.2 ou une version ultérieure du Dispatcher. Cela dit, les versions de Dispatcher sont indépendantes d’AEM. Ainsi, par exemple, la version 4.3.2 de Dispatcher est également compatible avec Adobe Experience Manager 6.4.

La convention de nommage de fichier suivante est utilisée :

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

Par exemple, le fichier `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` contient la version 4.3.1 de Dispatcher pour un serveur web Apache 2.4 qui fonctionne sous Linux® i686, empaqueté à l’aide du format **tar**.

Le tableau suivant indique l’identifiant de serveur web utilisé dans les noms de fichier pour chaque serveur web :

| Serveur web | Kit d’installation |
|--- |--- |
| Apache 2.4 | dispatcher-apache **2.4**-&lt;other parameters> |
| Microsoft® Internet Information Server 7.5, 8, 8.5, 10 | dispatcher-**iis**-&lt;autres paramètres> |
| Sun Java™ Web Server iPlanet | dispatcher-**ns**-&lt;autres paramètres> |

>[!CAUTION]
>
>Assurez-vous d’installer la dernière version de Dispatcher disponible pour votre plateforme. Chaque année, mettez à niveau votre instance de Dispatcher pour pouvoir utiliser la dernière version en date et profiter des améliorations du produit.

>[!NOTE]
>
>Les clientes et clients effectuant une mise à niveau spécifique de la version 4.3.3 vers la version 4.3.4 devraient remarquer un comportement différent dans la façon dont les en-têtes de mise en cache sont définis pour le contenu ne pouvant pas être mis en cache. Pour en savoir plus sur ce changement, consultez la page [Notes de mise à jour](/help/using/release-notes.md#nov).

Chaque archive contient les fichiers suivants :

* les modules de Dispatcher ;
* un exemple de fichier de configuration ;
* le fichier README contenant les instructions d’installation et les informations de dernière minute ;
* le fichier MODIFICATIONS qui répertorie les problèmes résolus dans les versions actuelle et antérieures.

>[!NOTE]
>
>Consultez le fichier README pour vérifier toutes les modifications de dernière minute et les notes spécifiques aux plateformes avant de commencer l’installation.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific Web server installation procedures.</p>

 -->

## Microsoft® Internet Information Server {#microsoft-internet-information-server}

Pour plus d&#39;informations sur l&#39;installation de ce serveur Web, consultez les ressources suivantes :

* Documentation de Microsoft® sur Internet Information Server
* [« Site officiel Microsoft® IIS »](https://www.iis.net/)

### Composants IIS requis {#required-iis-components}

Les versions 8.5 et 10 d’IIS nécessitent que les composants IIS suivants soient installés :

* Extensions ISAPI

Vous devez également ajouter le rôle Serveur Web (IIS) . Utilisez le gestionnaire de serveur pour ajouter le rôle et les composants.

## Microsoft IIS - Installation du module de Dispatcher {#microsoft-iis-installing-the-dispatcher-module}

L’archive requise pour Microsoft® Internet Information System est la suivante :

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

Le fichier .zip contient les fichiers suivants :

| File | Description |
|--- |--- |
| `disp_iis.dll` | Fichier de bibliothèque de liens dynamiques de Dispatcher. |
| `disp_iis.ini` | Fichier de configuration d’IIS. Cet exemple peut être mis à jour en fonction de vos besoins. **Remarque** : Le fichier ini doit avoir la même racine de nom (name-root) que le fichier dll. |
| `dispatcher.any` | Exemple de fichier de configuration pour Dispatcher. |
| `author_dispatcher.any` | Exemple de fichier de configuration pour Dispatcher fonctionnant avec l’instance de création. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. **Note** : consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Modifie un fichier qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Utilisez la procédure suivante pour copier les fichiers Dispatcher au bon emplacement.

1. Utilisez l’Explorateur Windows pour créer le répertoire `<IIS_INSTALLDIR>/Scripts` ; par exemple `C:\inetpub\Scripts`.

1. Extrayez les fichiers suivants du package de Dispatcher dans ce répertoire Scripts :

   * `disp_iis.dll`
   * `disp_iis.ini`
   * L’un des fichiers suivants selon que Dispatcher fonctionne avec une instance de création ou de publication AEM :
      * Instance de création: `author_dispatcher.any`
      * Instance de publication: `dispatcher.any`

## Microsoft® IIS - Configurer le fichier INI de Dispatcher {#microsoft-iis-configure-the-dispatcher-ini-file}

Pour configurer l’installation de Dispatcher, modifiez le fichier `disp_iis.ini`. Le format de base du fichier `.ini` se présente comme suit :

```xml
[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1
```

Le tableau suivant décrit chaque propriété.

| Paramètre | Description |
|--- |--- |
| `configpath` | Emplacement du fichier `dispatcher.any` dans le système de fichiers local (chemin d’accès absolu). |
| `logfile` | Emplacement du fichier `dispatcher.log`. Si cet emplacement n’est pas défini, les messages du journal accèdent au journal des événements Windows. |
| `loglevel` | Définit le niveau de journal utilisé pour générer les messages dans le journal des événements. Les valeurs suivantes peuvent être spécifiées au niveau de journalisation pour le fichier journal : <br/> 0 - Messages d’erreur uniquement. <br/>1 - Erreurs et avertissements. <br/>2 - Erreurs, avertissements et messages d’information <br/>3 - Erreurs, avertissements, messages d’information et de débogage. <br/>**Note** : définissez le niveau de journalisation sur 3 pendant l’installation et le test, puis repassez à 0 lors de l’exécution dans un environnement de production. |
| `replaceauthorization` | Spécifie le mode de gestion des en-têtes d’autorisation de la requête HTTP. Les valeurs suivantes sont valides :<br/> 0 - Les en-têtes d’autorisation ne sont pas modifiés. <br/>1 - Remplace n’importe quel en-tête appelé « Authorization » autre que l’en-tête « Basic » par son équivalent `Basic <IIS:LOGON\_USER>`.<br/> |
| `servervariables` | Définit le mode de traitement des variables du serveur.<br/>0 - Les variables du serveur IIS ne sont pas envoyées à Dispatcher ou à l’AEM. <br/>1 - Toutes les variables du serveur IIS (telles que `LOGON\_USER, QUERY\_STRING, ...`) sont envoyées à Dispatcher, ainsi que les en-têtes de requêtes (et également à l’instance AEM si elle n’est pas mise en cache).  <br/>Les variables de serveur incluent `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` et beaucoup d’autres. Voir la documentation d’IIS pour obtenir la liste complète des variables, avec des informations. |
| `enable_chunked_transfer` | Définit s’il faut activer (1) ou désactiver le transfert de bloc (0) pour la réponse client. La valeur par défaut est 0. |

Un exemple de configuration :

```xml
[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0
```

### Configurer Microsoft® IIS {#configuring-microsoft-iis}

Configurez IIS afin d’intégrer le module Dispatcher ISAPI. Dans IIS, vous utilisez le mappage d’applications génériques.

### Configurer l’accès anonyme - IIS 8.5 et 10 {#configuring-anonymous-access-iis-and}

L’agent de réplication de purge par défaut sur l’instance de création est configuré de sorte qu’il n’envoie pas d’informations d’identification de sécurité avec les requêtes de purge. Le site web sur lequel vous utilisez le cache de Dispatcher doit donc autoriser l’accès anonyme.

Si votre site web utilise une méthode d’authentification, l’agent de réplication de purge doit être configuré en conséquence.

1. Ouvrez IIS Manager et sélectionnez le site web que vous utilisez comme cache de Dispatcher.
1. En mode Affichage des fonctionnalités, dans la section IIS, double-cliquez sur Authentification.
1. Si l’authentification anonyme n’est pas activée, sélectionnez Authentification anonyme puis, dans la zone Actions, cliquez sur Activer.

### Intégrer le module ISAPI de Dispatcher - IIS 8.5 et 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Procédez comme suit pour ajouter le module ISAPI de Dispatcher à IIS.

1. Ouvrez IIS Manager.
1. Sélectionnez le site web que vous utilisez comme cache de Dispatcher.
1. En mode Affichage des fonctionnalités, dans la section IIS, double-cliquez sur Mappages de gestionnaires.
1. Dans le panneau Actions de la page Mappages de gestionnaires, cliquez sur Ajouter un mappage de script de caractère générique, ajoutez les valeurs de propriété suivantes, puis cliquez sur OK :

   * Chemin de la requête : &#42;
   * Exécutable : chemin d’accès absolu au fichier disp_iis.dll, par exemple `C:\inetpub\Scripts\disp_iis.dll`.
   * Nom : nom descriptif pour le mappage du gestionnaire, par exemple `Dispatcher`.

1. Dans la boîte de dialogue qui s’affiche, pour ajouter la bibliothèque disp_iis.dll à la liste des restrictions ISAPI et CGI, cliquez sur **Oui**.

   Pour IIS 7.0 et 7.5, la configuration est terminée. Effectuez les étapes restantes si vous configurez IIS 8.0.

1. (IIS 8.0) Dans la liste des mappages de gestionnaires, sélectionnez celui que vous avez créé, puis, dans la zone Actions, cliquez sur Modifier.
1. (IIS 8.0) Dans la boîte de dialogue Modifier le mappage de scripts, cliquez sur le bouton Restrictions des demandes.
1. (IIS 8.0) Pour vous assurer que le gestionnaire est utilisé pour les fichiers et les dossiers qui ne sont pas encore mis en cache, désélectionnez **Appeler le gestionnaire uniquement si la requête est mappée à**. Cliquez sur **OK**.
1. (IIS 8.0) Dans la boîte de dialogue Modifier le mappage de scripts, cliquez sur OK.

### Configuration de l’accès au cache - IIS 8.5 et 10 {#configuring-access-to-the-cache-iis-and}

Accordez à l’utilisateur par défaut du pool d’applications l’accès en écriture au dossier utilisé comme cache de Dispatcher.

1. Cliquez avec le bouton droit sur le dossier racine du site web que vous utilisez comme cache de Dispatcher, puis cliquez sur Propriétés, par exemple `C:\inetpub\wwwroot`.
1. Dans l’onglet Sécurité, cliquez sur Modifier, puis, dans la boîte de dialogue Autorisations, cliquez sur Ajouter. Une boîte de dialogue s’ouvre pour sélectionner les comptes d’utilisateurs et d’utilisatrices. Cliquez sur le bouton Emplacements, sélectionnez le nom de votre ordinateur, puis cliquez sur OK.

   Gardez cette boîte de dialogue ouverte pendant que vous effectuez l’étape suivante.

1. Dans le Gestionnaire IIS, choisissez le site IIS que vous utilisez pour le cache de Dispatcher et, dans la partie droite de la fenêtre, cliquez sur Paramètres avancés.
1. Sélectionnez la valeur de la propriété Pool d’application et copiez-la dans le presse-papiers.
1. Revenez à la boîte de dialogue ouverte. Dans la zone Entrez les noms des objets à sélectionner, saisissez `IIS AppPool\`, puis collez le contenu du Presse-papiers. La valeur doit ressembler à l’exemple suivant :

   `IIS AppPool\DefaultAppPool`

1. Cliquez sur le bouton Vérifier les noms. Lorsque Windows résout le compte d’utilisateur ou d’utilisatrice, cliquez sur OK.
1. Dans la boîte de dialogue Autorisations du dossier de Dispatcher, sélectionnez le compte que vous venez d’ajouter et activez toutes les autorisations pour le compte. **sauf pour le contrôle complet** et cliquez sur OK. Cliquez sur OK pour fermer la boîte de dialogue Propriétés du dossier.

### Enregistrement du type MIME JSON - IIS 8.5 et 10 {#registering-the-json-mime-type-iis-and}

Utilisez la procédure suivante pour enregistrer le type MIME JSON lorsque vous souhaitez que Dispatcher autorise les appels JSON.

1. Dans le Gestionnaire des services Internet, sélectionnez votre site web et cliquez deux fois sur Types MIME à l’aide de la vue Fonctions.
1. Si l’extension JSON ne figure pas dans la liste, dans le panneau Actions, cliquez sur Ajouter, saisissez les valeurs de propriété suivantes, puis cliquez sur OK :

   * Extension de nom de fichier : `.json`
   * MIME Type: `application/json`

### Supprimer le segment masqué bin - IIS 8.5 et 10 {#removing-the-bin-hidden-segment-iis-and}

Suivez la procédure ci-dessous pour supprimer le segment masqué `bin`. Les sites web qui ne sont pas nouveaux peuvent contenir ce segment masqué.

1. Dans le Gestionnaire des services Internet, sélectionnez votre site web et cliquez deux fois sur Filtrage des demandes à l’aide de la vue Fonctionnalités.
1. Sélectionnez le segment `bin`, cliquez sur Supprimer, puis sur Oui dans la boîte de dialogue de confirmation.

### Consigner des messages IIS dans un fichier - IIS 8.5 et 10 {#logging-iis-messages-to-a-file-iis-and}

Utilisez la procédure suivante pour écrire les messages du journal Dispatcher dans un fichier journal plutôt que dans le journal des événements Windows. Configurez Dispatcher pour utiliser le fichier journal et accorder à IIS un accès en écriture au fichier.

1. Utilisez l’Explorateur Windows pour créer un dossier nommé `dispatcher` sous le dossier des journaux de l’installation d’IIS. Le chemin d’accès à ce dossier pour une installation standard est `C:\inetpub\logs\dispatcher`.

1. Cliquez avec le bouton droit sur le dossier de Dispatcher, puis cliquez sur **Propriétés**.
1. Dans l’onglet Sécurité , cliquez sur **Modifier**.
1. Dans la boîte de dialogue Autorisations, cliquez sur **Ajouter**. Une boîte de dialogue s’ouvre pour sélectionner les comptes d’utilisateurs et d’utilisatrices. Cliquez sur le bouton Emplacements, sélectionnez le nom de votre ordinateur, puis cliquez sur OK.

   Gardez cette boîte de dialogue ouverte pendant que vous effectuez l’étape suivante.

1. Dans le Gestionnaire IIS, choisissez le site IIS que vous utilisez pour le cache de Dispatcher et, dans la partie droite de la fenêtre, cliquez sur Paramètres avancés.
1. Sélectionnez la valeur de la propriété Pool d’application et copiez-la dans le presse-papiers.
1. Revenez à la boîte de dialogue ouverte. Dans la zone Entrez les noms des objets à sélectionner, saisissez `IIS AppPool\`, puis collez le contenu du Presse-papiers. La valeur doit ressembler à l’exemple suivant :

   `IIS AppPool\DefaultAppPool`

1. Cliquez sur le bouton Vérifier les noms. Lorsque Windows résout le compte d’utilisateur ou d’utilisatrice, cliquez sur OK.
1. Dans la boîte de dialogue Autorisations du dossier Dispatcher, sélectionnez le compte que vous venez d’ajouter. Activez toutes les autorisations pour le compte, **à l’exception du contrôle total**, puis cliquez sur OK. Cliquez sur OK pour fermer la boîte de dialogue Propriétés du dossier.
1. Utilisez un éditeur de texte pour ouvrir le fichier `disp_iis.ini`
1. Pour configurer l’emplacement du fichier journal, ajoutez une ligne de texte similaire à l’exemple suivant, puis enregistrez le fichier :

   ```xml
   logfile=C:\inetpub\logs\dispatcher\dispatcher.log
   ```

### Étapes suivantes {#next-steps}

Pour pouvoir commencer à utiliser Dispatcher, vous devez avoir des connaissances concernant les éléments suivants :

* [Configurer](dispatcher-configuration.md) Dispatcher
* [Configurer AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.

## Serveur web Apache {#apache-web-server}

>[!CAUTION]
>
>Les instructions d’installation pour **Windows** et **UNIX®** sont abordées ici. Effectuez les étapes avec précaution.

### Installer le serveur web Apache {#installing-apache-web-server}

Pour plus d’informations sur le mode d’installation d’un serveur web Apache, lisez le manuel d’installation [en ligne](https://httpd.apache.org/) ou dans la distribution.

>[!CAUTION]
>
>Si vous créez un fichier binaire Apache en compilant les fichiers source, assurez-vous d’activer la **`dynamic modules support`**. L’activation de cette option peut être effectuée à l’aide de l’une des options suivantes : **—enable-shared** options. Au minimum, incluez le module `mod_so`.
>
>Vous trouverez plus d’informations dans le manuel d’installation du serveur web Apache.

Voir également les [Conseils de sécurité](https://httpd.apache.org/docs/2.4/misc/security_tips.html) et les [Rapports de sécurité](https://httpd.apache.org/security_report.html) du serveur HTTP Apache.

### Serveur web Apache - Ajouter le module de Dispatcher {#apache-web-server-add-the-dispatcher-module}

Le Dispatcher se présente comme suit :

* **Windows** : une bibliothèque de liens dynamiques (DLL)
* **UNIX®** : un objet partagé dynamique (DSO)

Les fichiers d’archives d’installation contiennent les fichiers suivants, selon que vous avez sélectionné Windows ou UNIX® :

| File | Description |
|--- |--- |
| disp_apache&lt;x.y>.dll | Windows : fichier de bibliothèque de liens dynamiques de Dispatcher. |
| dispatcher-apache&lt;x.y>-&lt;rel-nr>.so | UNIX® : fichier de bibliothèque d’objets partagés de Dispatcher. |
| mod_dispatcher.so | UNIX® : exemple de lien. |
| http.conf.disp&lt;x> | Exemple de fichier de configuration pour le serveur Apache. |
| dispatcher.any | Exemple de fichier de configuration pour Dispatcher. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. **Note** : consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Modifie un fichier qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Procédez comme suit pour ajouter Dispatcher à votre serveur web Apache :

1. Placez le fichier Dispatcher dans le répertoire approprié du module Apache :

   * **Windows** : placez `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **UNIX®** : localisez le répertoire `<APACHE_ROOT>/libexec` ou `<APACHE_ROOT>/modules` selon votre installation.\
     Copiez `dispatcher-apache<options>.so` dans ce répertoire.\
     Pour simplifier la maintenance à long terme, vous pouvez également créer un lien symbolique nommé `mod_dispatcher.so` dans Dispatcher :\
     `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copiez le fichier dispatcher.any dans le répertoire `<APACHE_ROOT>/conf`.

   **Remarque :** Vous pouvez placer ce fichier dans un autre emplacement tant que la propriété DispatcherLog du module de Dispatcher est configurée en conséquence. (Voir les entrées de configuration spécifiques à Dispatcher ci-dessous.)

### Serveur web Apache - Configuration des propriétés SELinux  {#apache-web-server-configure-selinux-properties}

Si vous exécutez Dispatcher sur RedHat® Linux® Kernel 2.6 avec SELinux activé, vous pouvez rencontrer des messages d’erreur de ce type dans le fichier journal de Dispatcher.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

Cette erreur est probablement due à une sécurité SELinux activée. Si tel est le cas, effectuez les tâches suivantes :

* Configurez le contexte SELinux du fichier du module Dispatcher.
* Activez les scripts et les modules HTTPD pour établir les connexions réseau.
* Configurez le contexte SELinux du docroot, dans lequel les fichiers mis en cache sont stockés.

Entrez les commandes suivantes dans une fenêtre d’invite de commandes en remplaçant `[path to the dispatcher.so file]` par le chemin d’accès du module Dispatcher que vous avez installé sur le serveur web Apache, et *`path to the docroot`* par le chemin d’accès de l’emplacement du docroot (par exemple, `/opt/cq/cache`) :

```shell
semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_rw_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_rw_content_t "[path to the docroot](/.*)?"
```

### Serveur web Apache - Configurer le serveur web Apache pour Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

Le serveur web Apache doit être configuré à l’aide de `httpd.conf`. Le kit d’installation de Dispatcher contient un exemple de fichier de configuration nommé `httpd.conf.disp<x>`.

Les étapes suivantes sont obligatoires :

1. Accéder à `<APACHE_ROOT>/conf`.
1. Ouvrez `httpd.conf`pour modification.
1. Les entrées de configuration suivantes doivent être ajoutées, dans l’ordre indiqué :

   * **LoadModule** pour charger le module au démarrage.
   * Les entrées de configuration spécifiques à Dispatcher, notamment **DispatcherConfig, DispatcherLog** et **DispatcherLogLevel**.
   * **SetHandler** pour activer le Dispatcher. **LoadModule**.
   * **ModMimeUsePathInfo** pour configurer le comportement de **mod_mime**.

1. (Facultatif) Il est recommandé de modifier la personne propriétaire du répertoire htdocs :

   * Le serveur Apache démarre en tant que racine, bien que les processus enfant démarrent en tant que démon (pour des raisons de sécurité). DocumentRoot (`<APACHE_ROOT>/htdocs`) doit appartenir au démon de l’utilisateur :

     ```xml
     cd <APACHE_ROOT>  
     chown -R daemon:daemon htdocs
     ```

**LoadModule**

Le tableau suivant liste des exemples pouvant être utilisés. Les entrées exactes dépendent de votre serveur web Apache spécifique :

|  |  |
|--- |--- |
| Windows | `... LoadModule dispatcher_module modules\disp_apache.dll ...` |
| UNIX® (en supposant un lien symbolique) | `... LoadModule dispatcher_module libexec/mod_dispatcher.so ...` |

>[!NOTE]
>
>Le premier paramètre de chaque instruction doit être écrit exactement comme dans les exemples ci-dessus.
>
>Consultez les exemples de fichiers de configuration fournis et la documentation du serveur web Apache pour plus de détails sur cette commande.

**Entrées de configuration spécifiques à Dispatcher**

Les entrées de configuration spécifiques à Dispatcher sont placées après l’entrée LoadModule. Le tableau suivant présente un exemple de configuration qui s’applique à la fois à UNIX® et Windows :

**Windows &amp; UNIX®**

```
...
<IfModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...
```

>[!NOTE]
>
>Les clientes et clients effectuant une mise à niveau spécifique de la version 4.3.3 vers la version 4.3.4 devraient remarquer un comportement différent dans la façon dont les en-têtes de mise en cache sont définis pour le contenu ne pouvant pas être mis en cache. Pour en savoir plus sur cette modification, consultez la page [Notes de mise à jour](/help/using/release-notes.md#nov).

Vous trouverez les paramètres de configuration dans le tableau suivant :

| Paramètre | Description |
|--- |--- |
| DispatcherConfig | Emplacement et nom du fichier de configuration de Dispatcher. <br/>Lorsque cette propriété est située dans la configuration du serveur principal, tous les hôtes virtuels héritent de la valeur de la propriété. Toutefois, les hôtes virtuels peuvent inclure une propriété DispatcherConfig pour remplacer la configuration du serveur principal. |
| DispatcherLog | Emplacement et nom du fichier journal. |
| DispatcherLogLevel | Niveau de journal du fichier journal : <br/>0 - Erreurs <br/>1 - Avertissements <br/>2 - Informations <br/>3 - Débogage <br/>**Note** : définissez le niveau de journal sur 3 pendant l’installation et le test, puis sur 0 lors de l’exécution dans un environnement de production. |
| DispatcherNoServerHeader | *Ce paramètre est obsolète et inefficace.*<br/><br/> Définit l’en-tête du serveur à utiliser : <br/><ul><li>undefined ou 0 - L’en-tête du serveur HTTP contient la version AEM. </li><li>1 - L’en-tête du serveur Apache est utilisé.</li></ul> |
| DispatcherDeclineRoot | Définit s’il faut refuser des requêtes à la racine &quot;/&quot; : <br/>**0** - accepte les demandes à / <br/>**1** - Dispatcher ne gère pas les requêtes envoyées à /. Utilisez plutôt mod_alias pour le mappage correct. |
| DispatcherUseProcessedURL | Définit s’il faut utiliser des URL prétraitées pour tout traitement supplémentaire par Dispatcher : <br/>**0** - utilise l’URL d’origine transmise au serveur web. <br/>**1** - Dispatcher utilise l’URL déjà traitée par les gestionnaires qui précèdent Dispatcher (c’est-à-dire `mod_rewrite`) à la place de l’URL d’origine transmise au serveur web. Par exemple, l’URL d’origine ou l’URL traitée est mise en correspondance avec des filtres de Dispatcher. L’URL est également utilisée comme base de la structure des fichiers du cache. Consultez la documentation du site web Apache pour plus d’informations sur mod_rewrite, par exemple Apache 2.4. Lors de l’utilisation de mod_rewrite, utilisez l’indicateur &#39;passthrough&#39; (passage au gestionnaire suivant) pour forcer le moteur de réécriture à définir le champ URI de la structure interne request_rec sur la valeur du champ filename. |
| DispatcherPassError | Définit la façon de prendre en charge les codes d’erreur pour le traitement de ErrorDocument : <br/>**0** - Dispatcher met en file d’attente toutes les réponses d’erreur envoyées au client. <br/>**1** - Dispatcher ne met pas en file d’attente une réponse d’erreur envoyée au client (si le code d’état est supérieur ou égal à 400). Il transmet plutôt le code d’état à Apache, ce qui permet à une directive ErrorDocument de traiter ce dernier. <br/>**Plage de codes** - Indiquez une plage de codes d’erreur pour lesquels la réponse est transmise à Apache. D’autres codes d’erreur sont transmis au client. Par exemple, la configuration suivante transmet les réponses au client pour l’erreur 412 et toutes les autres erreurs sont transmises à Apache : DispatcherPassError 400-411,413-417 |
| DispatcherKeepAliveTimeout | Spécifie le délai d’expiration de conservation, en secondes. Depuis la version 4.2.0 de Dispatcher, la valeur de persistance par défaut est 60. La valeur 0 désactive la persistance. |
| DispatcherNoCanonURL | Si vous définissez ce paramètre sur Activé, l’URL brute est transmise au serveur principal au lieu de l’URL canonisée. Elle remplace alors les paramètres de DispatcherUseProcessedURL. La valeur par défaut est Désactivé. <br/>**Note** : les règles de filtrage de la configuration Dispatcher sont toujours évaluées par rapport à l’URL expurgée et non à l’URL brute. |

>[!NOTE]
>
>Les entrées de chemin d’accès sont relatives au répertoire racine du serveur web Apache.

>[!NOTE]
>
>Les paramètres par défaut de l’en-tête du serveur sont les suivants :
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`
>
>Indique la version d’AEM, à des fins statistiques. Si vous souhaitez désactiver ces informations disponibles dans l’en-tête, vous pouvez définir les éléments suivants :
>
>`ServerTokens Prod`
>
>Pour plus d’informations, voir la [documentation Apache sur la directive ServerTokens (par exemple, pour Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html).

**SetHandler**

Après ces entrées, vous devez ajouter une instruction **SetHandler** au contexte de la configuration (`<Directory>`, `<Location>`) pour que Dispatcher traite les requêtes entrantes. L’exemple suivant configure Dispatcher pour traiter les demandes du site web complet :

**Windows et UNIX®**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

L’exemple suivant configure Dispatcher pour traiter les demandes d’un domaine virtuel :

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**UNIX®**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>Le paramètre de la variable **SetHandler** La déclaration doit être écrite *identique aux exemples ci-dessus* car il s’agit du nom du gestionnaire défini dans le module.
>
>Consultez les exemples de fichiers de configuration fournis et la documentation du serveur web Apache pour plus de détails sur cette commande.

**ModMimeUsePathInfo**

Après l’instruction **SetHandler**, vous devez également ajouter la définition **ModMimeUsePathInfo**.

>[!NOTE]
>
>Utilisez et configurez uniquement le paramètre `ModMimeUsePathInfo` si vous utilisez la version 4.0.9 de Dispatcher ou une version ultérieure
>
>Dispatcher version 4.0.9 a été publié en 2011. Si vous utilisez une ancienne version, la mise à niveau vers une version récente de Dispatcher est appropriée.

Le paramètre **ModMimeUsePathInfo** doit être défini sur `On` pour toutes les configurations d’Apache :

`ModMimeUsePathInfo On`

Le module mod_mime (par exemple [Module Apache mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) est utilisé pour affecter des métadonnées de contenu au contenu sélectionné pour une réponse HTTP. La configuration par défaut signifie que `mod_mime` détermine le type de contenu. Ainsi, seule la partie de l’URL qui correspond à un fichier ou à un répertoire est prise en compte.

S’il est `On`, le paramètre `ModMimeUsePathInfo` indique que `mod_mime` doit déterminer le type de contenu en fonction de l’URL *complète*. Cela signifie que des métadonnées sont appliquées aux ressources virtuelles selon leur extension.

L’exemple suivant active **ModMimeUsePathInfo** :

**Windows et UNIX®**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Activer la prise en charge HTTPS (UNIX® and Linux®) {#enable-support-for-https-unix-and-linux}

Dispatcher utilise OpenSSL pour mettre en œuvre une communication sécurisée sur HTTP. À partir de la version Dispatcher **4.2.0**, OpenSSL 1.0.0 et OpenSSL 1.0.1 sont pris en charge. Dispatcher utilise OpenSSL 1.0.0 par défaut. Pour utiliser OpenSSL 1.0.1, procédez comme suit pour créer des liens symboliques afin que Dispatcher utilise les bibliothèques OpenSSL installées.

1. Ouvrez un terminal et remplacez le répertoire actuel par le répertoire où les bibliothèques OpenSSL sont installées, par exemple :

   ```shell
   cd /usr/lib64
   ```

1. Pour créer les liens symboliques, entrez les commandes suivantes :

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>Si vous utilisez une version personnalisée d’Apache, assurez-vous qu’Apache et Dispatcher sont compilés à l’aide de la même version de [OpenSSL](https://www.openssl.org/source/).

### Étapes suivantes {#next-steps-1}

Pour pouvoir commencer à utiliser Dispatcher, vous devez procéder aux étapes suivantes :

* [Configurer](dispatcher-configuration.md) Dispatcher
* [Configurer AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.

## Server web Sun Java™ System/iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Les instructions pour les environnements Windows et UNIX® sont couvertes ici.
>
>Faites preuve de prudence lorsque vous sélectionnez lequel exécuter.

### Serveur web Sun Java™ System/iPlanet - Installer le serveur web {#sun-java-system-web-server-iplanet-installing-your-web-server}

Pour obtenir des informations complètes sur l’installation de ces serveurs web, consultez leur documentation respective :

* Serveur web Sun Java™ System
* Serveur web iPlanet

### Serveur web Sun Java™ System/iPlanet - Ajouter le module Dispatcher {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

Le Dispatcher se présente comme suit :

* **Windows** : une bibliothèque de liens dynamiques (DLL)
* **UNIX®** : un objet partagé dynamique (DSO)

Les fichiers d’archives d’installation contiennent les fichiers suivants, selon que vous avez sélectionné Windows ou UNIX® :

| File | Description |
|---|---|
| `disp_ns.dll` | Windows : fichier de bibliothèque de liens dynamiques de Dispatcher. |
| `dispatcher.so` | UNIX® : fichier de bibliothèque d’objets partagés de Dispatcher. |
| `dispatcher.so` | UNIX® : exemple de lien. |
| `obj.conf.disp` | Exemple de fichier de configuration pour le serveur web iPlanet/Sun Java™ System. |
| `dispatcher.any` | Exemple de fichier de configuration pour Dispatcher. |
| LISEZMOI | Fichier Lisezmoi contenant les instructions d’installation et les informations de dernière minute. **Note** : consultez le fichier avant de commencer l’installation. |
| MODIFICATIONS | Modifie un fichier qui répertorie les problèmes résolus dans les versions actuelle et antérieures. |

Procédez comme suit pour ajouter Dispatcher à votre serveur web :

1. Placez le fichier de Dispatcher dans le répertoire `plugin` du serveur web :

### Serveur web Sun Java™ System/iPlanet - Configurer pour Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

Le serveur web doit être configuré à l’aide de `obj.conf`. Le kit d’installation de Dispatcher contient un exemple de fichier de configuration nommé `obj.conf.disp`.

1. Accéder à `<WEBSERVER_ROOT>/config`.
1. Ouvrez `obj.conf`pour modification.
1. Copiez la ligne qui va\
   `Service fn="dispService"`\
   de `obj.conf.disp` à la section d’initialisation de `obj.conf`.

1. Enregistrez les modifications.
1. Ouvrez `magnus.conf` pour modification.
1. Copiez les deux lignes qui vont de\
   `Init funcs="dispService, dispInit"`\
   et\
   `Init fn="dispInit"`\
   de `obj.conf.disp` à la section d’initialisation de `magnus.conf`.

1. Enregistrez les modifications.

>[!NOTE]
>
>Les configurations suivantes doivent toutes se trouver sur une seule ligne. En outre, la variable `$(SERVER_ROOT)` et `$(PRODUCT_SUBDIR)` doivent être remplacées par leurs valeurs respectives.

**Initialisation**

Le tableau suivant répertorie des exemples pouvant être utilisés ; les entrées exactes dépendent de votre serveur web spécifique :

**Windows et UNIX®**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

Où :

| Paramètre | Description |
|--- |--- |
| `config` | Emplacement et nom du fichier de configuration `dispatcher.any.` |
| `logfile` | Emplacement et nom du fichier journal. |
| `loglevel` | Niveau de journal lors de l’écriture de messages dans le fichier journal : <br/>**0** Erreurs <br/>**1** Avertissements <br/>**2** Infos <br/>**3** Déboguer <br/>**Note :** définissez le niveau de journal sur 3 pendant l’installation et le test, puis sur 0 lors de l’exécution dans un environnement de production. |
| `keepalivetimeout` | Spécifie le délai d’expiration de conservation, en secondes. Depuis la version 4.2.0 de Dispatcher, la valeur de persistance par défaut est 60. La valeur 0 désactive la persistance. |

En fonction de vos besoins, vous pouvez paramétrer Dispatcher comme service pour vos objets. Pour configurer Dispatcher pour l’ensemble de votre site web, modifiez l’objet par défaut :

**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**UNIX®**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Étapes suivantes {#next-steps-2}

Pour pouvoir commencer à utiliser Dispatcher, vous devez avoir des connaissances sur les éléments suivants :

* [Configurer](dispatcher-configuration.md) Dispatcher
* [Configurer AEM](page-invalidate.md) pour qu’il fonctionne avec Dispatcher.
