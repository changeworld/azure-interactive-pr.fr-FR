---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459993"
---
Vous avez réussi à créer une application serverless complète à l’aide des services Azure.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous aurez terminé votre travail avec cette application, vous pourrez utiliser la commande suivante pour supprimer toutes les ressources créées dans le cadre du didacticiel :

```azurecli
az group delete --name first-serverless-app
```

Quand vous y êtes invité, tapez `y`.  

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :
> [!div class="checklist"]
> * Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées
> * Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions
> * Redimensionner des images à l’aide d’Azure Functions
> * Stocker des métadonnées d’image dans Azure Cosmos DB
> * Utiliser l’API Vision de Cognitive Services pour générer automatiquement des légendes d’image
> * Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs

Pour savoir comment connecter encore plus de services à Functions, passez au didacticiel Logic Apps. 

> [!div class="nextstepaction"]
> [Fonctions avec Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
