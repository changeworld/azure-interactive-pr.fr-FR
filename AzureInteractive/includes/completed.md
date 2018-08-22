---
title: Fichier Include
description: Fichier Include
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079247"
---
Vous avez réussi à créer une application serverless complète à l’aide des services Azure.

## <a name="clean-up-resources"></a>Supprimer les ressources

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