---
source-git-commit: 2d90738d01fef6e37a2c25784ed4d1338c037c23
workflow-type: ht
source-wordcount: '715'
ht-degree: 100%

---
# Instructions relatives à la contribution à la documentation d’Adobe Experience Manager

## Philosophie de la documentation

Les utilisateurs et utilisatrices d’Adobe Experience Manager travaillent dans des environnements très concurrentiels et font de leur mieux afin de créer des expériences numériques qui les distingueront de la concurrence. Par conséquent, lorsqu’Adobe fournit de nouveaux outils avancés dans AEM, ces outils sont complétés par une documentation précise et claire pour permettre aux clientes et clients d’exploiter immédiatement leur investissement AEM et maximiser leur ROI.

L’objectif de la documentation AEM est de la placer entre les mains des utilisateurs et utilisatrices d’AEM dès que possible. Adobe privilégie donc une documentation précise et utilisable, et la met à jour et l’améliore constamment.

## Contributions à la documentation

Afin d’améliorer continuellement la documentation d’AEM, toute la communauté des utilisateurs d’AEM peut apporter sa contribution. Que ce soit par le biais de demandes d’extraction ou de problèmes, les améliorations apportées à la documentation peuvent être des corrections, des clarifications, des extensions et des exemples supplémentaires.

## Normes de la documentation

Bien qu’Adobe apprécie les contributions à la documentation, toute contribution à la documentation d’AEM, sous la forme d’une requête d’extraction ou d’un problème, doit être conforme aux normes de contribution et de documentation.

Les contributions qui ne satisfont pas à ces normes peuvent être rejetées.

### Adobe documente les cas d’utilisation standard.

La documentation d’AEM couvre les cas d’utilisation standard. Les cas d’utilisation au-delà de la portée de l’installation et de l’utilisation standard du produit ne font pas partie de la documentation AEM.

### En général, Adobe ne documente pas les bugs ni leurs solutions de contournement.

La documentation d’AEM couvre les cas d’utilisation standard. Pour cette raison, les bugs, leurs effets et leurs solutions de contournement ne sont pas documentés.

Les exceptions à cette règle concernent les notes de mise à jour qui répertorient les problèmes connus ainsi que les solutions possibles après approbation par l’équipe de gestion des produits AEM.

### Les contributions à la documentation ne sont pas destinées à répondre aux questions techniques.

Toute contribution susceptible d’améliorer la documentation AEM est la bienvenue. Toutefois, les commentaires, les problèmes et les demandes d’extraction sont destinés uniquement à être des *contributions*. Ils n’ont pas pour but de répondre à vos questions sur l’utilisation et l’implémentation d’AEM ou la résolution de problèmes techniques.

Vous pouvez signaler toute question relative à l’utilisation d’AEM ou à des erreurs techniques à l’aide du processus d’assistance classique au moyen du [Portail d’assistance aux entreprises Experience Cloud](https://experienceleague.adobe.com/fr?support-solution=General#support) ou de discussions dans la [communauté Experience Manager](https://experienceleaguecommunities.adobe.com/t5/adobe-experience-manager/ct-p/adobe-experience-manager-community?profile.language=fr).

***Les contributions à la documentation d’AEM ne remplacent pas l’assistance clientèle Adobe***, et toute contribution de ce type qui recherche des réponses aux questions liées au support technique sera refusée.

### Les contributions doivent clairement référencer les pages de documentation concernées.

Si vous créez une demande pour suggérer des améliorations à la documentation, vous devez inclure des liens vers les pages concernées. Si vous créez un problème à l’aide du lien **Modifier cette page** sur une page de documentation, le problème est créé automatiquement avec un lien vers la page.

Cette méthode ne s’applique pas aux requêtes d’extraction qui, par nature, font référence aux pages concernées.

## Directives relatives à la documentation

Adobe demande que toutes les contributions à notre documentation suivent certaines directives de style.

Suivre ces directives facilite la révision et l’intégration rapide de votre contribution dans la documentation Adobe.

### Langue et style

#### Langue

* La documentation AEM est créée et conservée en anglais américain.
* Veillez à ce que les expressions restent le plus simple possible.
* Restez clair et concis.

Souvenez-vous que les lecteurs et lectrices de la documentation AEM sont internationaux et peuvent ne pas être des locuteurs et locutrices anglais natifs ou bilingues. Évitez les expressions familières et restez aussi clair et simple que possible.

#### Suivi du guide de style Microsoft®

[Le guide de style Microsoft®](https://learn.microsoft.com/en-us/style-guide/welcome/) est gratuit et concerne la documentation logicielle. Il s’applique à la documentation AEM, dans la mesure du possible.

### Mise en forme

| Élément | Style |
|---|---|
| Élément ou option de l’interface utilisateur | **gras** |
| Nom de fichier, chemin, entrée utilisateur, paramètre-valeurs | `monospaced` |
| Code, ligne de commande | ```Code Block``` |

### Captures d’écran

Les captures d’écran doivent être utilisées de manière judicieuse et uniquement lorsqu’une description textuelle est insuffisante.

Les marqueurs ou autres annotations dans les captures d’écran (comme les cadres rouges, les flèches ou le texte) ne doivent pas être utilisés. Ainsi, les captures d’écran sont plus faciles à réutiliser ou à répliquer dans les versions localisées de la documentation.

### Références spécifiques à la version

Dans la mesure du possible, évitez toute référence directe à une version spécifique dans tout le contenu de la documentation. La documentation est ainsi plus flexible et extensible pour les versions ultérieures.

### Utilisation de Day, AEM, CQ, CRX

Le produit doit toujours être désigné par son nom complet, **Adobe Experience Manager**, la première fois qu’il apparaît dans un article, et peut ensuite être appelé **AEM**.

Day, le logiciel Day, CQ et CRX ne doivent pas être utilisés, sauf lorsqu’il est impossible de faire autrement, par exemple dans les noms de classe ou en faisant référence à l’historique d’AEM.