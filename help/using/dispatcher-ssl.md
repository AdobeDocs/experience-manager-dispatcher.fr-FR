---
title: Utiliser SSL avec Dispatcher
description: Découvrez comment configurer le Dispatcher pour communiquer avec AEM à l’aide de connexions SSL.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '1305'
ht-degree: 91%

---

# Utiliser SSL avec Dispatcher {#using-ssl-with-dispatcher}

Utilisez les connexions SSL entre le Dispatcher et l’ordinateur de rendu :

* [SSL à sens unique](#use-ssl-when-dispatcher-connects-to-aem)
* [SSL mutuel](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Les opérations liées aux certificats SSL sont liées à des produits tiers. Elles ne sont pas couvertes par le contrat d’assistance et de maintenance Adobe Platinum.

## Utiliser SSL lorsque Dispatcher se connecte à AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configurez le Dispatcher pour communiquer avec l’instance de rendu AEM ou CQ à l’aide de connexions SSL.

Avant de configurer Dispatcher, configurez AEM ou CQ pour utiliser SSL. Pour plus d’informations, consultez ce qui suit :

* [SSL/TLS par défaut](https://experienceleague.adobe.com/fr/docs/experience-manager-65/content/security/ssl-by-default)
* [Utiliser l’assistant SSL dans AEM](https://experienceleague.adobe.com/fr/docs/experience-manager-learn/foundation/security/use-the-ssl-wizard)

### En-têtes de requête liés à SSL {#ssl-related-request-headers}

Lorsque Dispatcher reçoit une requête HTTPS, il inclut les en-têtes suivants dans la requête suivante qu’il envoie à AEM ou CQ :

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

Une requête via Apache 2.4 avec `mod_ssl` inclut des en-têtes similaires à l’exemple suivant :

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configurer Dispatcher pour utiliser SSL {#configuring-dispatcher-to-use-ssl}

Pour configurer Dispatcher de sorte qu’il se connecte à AEM ou CQ avec SSL, votre fichier [dispatcher.any](dispatcher-configuration.md) requiert les propriétés suivantes :

* Un hôte virtuel qui traite les requêtes HTTPS.
* La section `renders` de l’hôte virtuel inclut un élément qui identifie le nom et le port de l’hôte de l’instance CQ ou AEM qui utilise HTTPS.
* L’élément `renders` inclut une propriété nommée `secure` d’une valeur de `1`.

Remarque : créez un autre hôte virtuel pour le traitement des requêtes HTTP, le cas échéant.

L’exemple de fichier `dispatcher.any` suivant affiche les valeurs des propriétés pour la connexion via HTTPS à une instance CQ qui s’exécute sur l’hôte `localhost` et le port `8443` :

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Configuration du SSL mutuel entre Dispatcher et AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

Pour utiliser le protocole SSL mutuel, configurez les connexions entre Dispatcher et l’ordinateur de rendu (généralement une instance de publication AEM ou CQ) :

* Dispatcher se connecte à l’instance de rendu via SSL.
* L’instance de rendu vérifie la validité du certificat du Dispatcher.
* Dispatcher vérifie que l’autorité de certification du certificat de l’instance de rendu est approuvée.
* (Facultatif) Dispatcher vérifie que le certificat de l’instance de rendu correspond à l’adresse du serveur de l’instance de rendu.

Pour configurer le protocole SSL mutuel, vous avez besoin de certificats signés par une autorité de certification approuvée (CA). Les certificats auto-signés ne sont pas appropriés. Vous pouvez faire office d’autorité de certification ou utiliser les services d’une autorité de certification tierce pour signer vos certificats. Pour configurer le protocole SSL mutuel, vous devez disposer des éléments suivants :

* Des certificats signés pour l’instance de rendu et Dispatcher.
* Le certificat de l’autorité de certification (si vous faites office d’autorité de certification).
* Des bibliothèques OpenSSL pour générer l’autorité de certification, les certificats et les requêtes de certificat.

Pour configurer le protocole SSL mutuel, procédez comme suit :

1. [Installez](dispatcher-install.md) la version la plus récente de Dispatcher pour votre plateforme. Utilisez un fichier binaire de Dispatcher qui prend en charge le protocole SSL (SSL se trouve dans le nom du fichier, par exemple `dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar`).
1. [Créez ou obtenez un certificat signé par une autorité de certification](dispatcher-ssl.md#main-pars-title-3) pour le Dispatcher et l’instance de rendu.
1. [Créez un magasin de clés contenant le certificat du rendu](dispatcher-ssl.md#main-pars-title-6) et configurez le service HTTP du rendu.
1. [Configurez le module de serveur web de Dispatcher](dispatcher-ssl.md#main-pars-title-4) pour le protocole SSL mutuel.

### Créer ou obtenir des certificats signés par une autorité de certification {#creating-or-obtaining-ca-signed-certificates}

Créez ou obtenez des certificats signés par une autorité de certification qui authentifient l’instance de publication et Dispatcher.

#### Création de votre autorité de certification {#creating-your-ca}

Si vous agissez comme autorité de certification, utilisez [OpenSSL](https://www.openssl.org/) pour créer l’autorité de certification qui signe les certificats du serveur et du client. (Les bibliothèques OpenSSL doivent être installées.) Si vous utilisez une autorité de certification tierce, n’effectuez pas cette procédure.

1. Ouvrez un terminal et modifiez le répertoire actuel par le répertoire qui contient le fichier `CA.sh`, par exemple `/usr/local/ssl/misc`.
1. Pour créer l’autorité de certification, saisissez la commande suivante, puis fournissez des valeurs lorsque l’on vous y invite :

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Plusieurs propriétés dans le fichier `openssl.cnf` déterminent le comportement du script CA.sh. Modifiez ce fichier selon les besoins avant de créer votre autorité de certification.

#### Création des certificats {#creating-the-certificates}

Utilisez OpenSSL pour créer des demandes de certificat à envoyer à l’autorité de certification tierce ou à signer avec votre autorité de certification.

Lorsque vous créez un certificat, OpenSSL utilise la propriété Nom commun pour identifier l’entité titulaire du certificat. Pour le certificat de l’instance de rendu, utilisez le nom d’hôte de l’ordinateur de l’instance comme nom commun si vous configurez Dispatcher pour accepter le certificat. Suivez cette procédure uniquement s’il correspond au nom de l’hôte de l’instance de publication. Voir la propriété [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11).

1. Ouvrez un terminal et modifiez le répertoire actuel par le répertoire qui contient le fichier CH.sh de vos bibliothèques OpenSSL.
1. Saisissez la commande suivante et fournissez des valeurs lorsque l’on vous y invite. Si nécessaire, utilisez le nom d’hôte de l’instance de publication comme Nom commun. Le nom d’hôte est un nom pouvant être résolu par DNS pour l’adresse IP du rendu :

   ```shell
   ./CA.sh -newreq
   ```

   Si vous utilisez une autorité de certification tierce, envoyez le fichier newreq.pem à l’autorité de certification pour signature. Si vous agissez comme autorité de certification, passez à l’étape 3.

1. Pour signer le certificat à l’aide du certificat de votre autorité de certification, saisissez la commande suivante :

   ```shell
   ./CA.sh -sign
   ```

   Deux fichiers nommés `newcert.pem` et `newkey.pem` sont créés dans le répertoire contenant vos fichiers de gestion d’autorité de certification. Ces deux fichiers correspondent au certificat public et à la clé privée de l’ordinateur de rendu, respectivement.

1. Renommez `newcert.pem` en `rendercert.pem`, et renommez `newkey.pem` en `renderkey.pem`.
1. Répétez les étapes 2 et 3 pour créer un certificat et une clé publique pour le module Dispatcher. Assurez-vous d’utiliser un Nom commun spécifique à l’instance Dispatcher.
1. Renommez `newcert.pem` en `dispcert.pem`, et renommez `newkey.pem` en `dispkey.pem`.

### Configurer SSL sur l’ordinateur de rendu {#configuring-ssl-on-the-render-computer}

Configurez le protocole SSL sur l’instance de rendu à l’aide des fichiers `rendercert.pem` et `renderkey.pem`.

#### Convertissez le certificat de rendu au format JKS (Java™ KeyStore) {#converting-the-render-certificate-to-jks-format}

Utilisez la commande suivante pour convertir le certificat de rendu, qui est un fichier PEM, en fichier PKCS#12. Incluez également le certificat de l’autorité de certification qui a signé le certificat de rendu :

1. Dans une fenêtre de terminal, remplacez le répertoire actuel par l’emplacement du certificat et de la clé privée de rendu.
1. Pour convertir le certificat de rendu, qui est un fichier PEM, en fichier PKCS#12, saisissez la commande suivante. Incluez également le certificat de l’autorité de certification qui a signé le certificat de rendu :

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. Pour convertir le fichier PKCS#12 au format Java™ KeyStore (JKS), saisissez la commande suivante :

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. Le Java™ Keystore est créé à l’aide d’un alias par défaut. Modifiez l’alias si vous le souhaitez :

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Ajouter le certificat d’autorité de certification au TrustStore du rendu {#adding-the-ca-cert-to-the-render-s-truststore}

Si vous agissez comme autorité de certification, importez votre certificat d’autorité de certification dans un magasin de clés. Configurez ensuite la JVM qui exécute l’instance de rendu pour qu’elle fasse confiance au magasin de clés.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Utilisez un éditeur de texte pour ouvrir le fichier cacert.pem et supprimez tout le texte qui précède la ligne suivante :

   `-----BEGIN CERTIFICATE-----`

1. Utilisez la commande suivante pour importer le certificat dans un magasin de clés :

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. Pour configurer la JVM qui exécute votre instance de rendu afin qu’elle fasse confiance au magasin de clés, utilisez la propriété système suivante :

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   Par exemple, si vous utilisez le script crx-quickstart/bin/quickstart pour démarrer votre instance de publication, vous pouvez modifier la propriété CQ_JVM_OPTS :

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuration de l’instance de rendu {#configuring-the-render-instance}

Pour configurer le service HTTP de l’instance de rendu afin qu’il utilise SSL, utilisez le certificat de rendu en suivant les instructions contenues dans la section *`Enable SSL on the Publish Instance`* :

* AEM 6.2 : [activation de HTTP via SSL](https://experienceleague.adobe.com/fr/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.1 : [activation de HTTP via SSL](https://experienceleague.adobe.com/fr/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* Anciennes versions d’AEM : voir [cette page](https://experienceleague.adobe.com/fr/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions).

### Configuration de SSL pour le module Dispatcher {#configuring-ssl-for-the-dispatcher-module}

Pour configurer Dispatcher de sorte qu’il utilise le protocole SSL mutuel, préparez le certificat de Dispatcher, puis configurez le module de serveur web.

### Création d’un certificat Dispatcher unifié {#creating-a-unified-dispatcher-certificate}

Combinez le certificat de Dispatcher et la clé privée non chiffrée en un seul fichier PEM. Utilisez un éditeur de texte ou la commande `cat` pour créer un fichier semblable à l’exemple suivant :

1. Ouvrez un terminal et remplacez le répertoire actuel par l’emplacement du fichier dispkey.pem.
1. Pour déchiffrer la clé privée, entrez la commande suivante :

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Utilisez un éditeur de texte ou la commande `cat` pour combiner la clé privée non chiffrée avec le certificat dans un fichier unique semblable à l’exemple suivant :

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Spécifier le certificat à utiliser pour Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Ajoutez les propriétés suivantes à la [configuration du module de Dispatcher](dispatcher-install.md#main-pars-55-35-1022) (dans `httpd.conf`):

* `DispatcherCertificateFile` : chemin d’accès au fichier de certificat unifié de Dispatcher, contenant le certificat public et la clé privée non chiffrée. Ce fichier est utilisé lorsque le serveur SSL demande le certificat client du Dispatcher.
* `DispatcherCACertificateFile` : chemin d’accès au fichier de certificat de l’autorité de certification. Utilisé si le serveur SSL présente une autorité de certification qui n’est pas approuvée par une autorité racine.
* `DispatcherCheckPeerCN` : spécification de l’activation (`On`) ou de la désactivation (`Off`) de la vérification des noms d’hôte pour les certificats du serveur distant.

Le code suivant est un exemple de configuration :

```xml
<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>
```
