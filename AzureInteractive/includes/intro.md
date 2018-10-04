---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460022"
---
Dans ce didacticiel, vous déployez une application web simple qui présente une interface utilisateur HTML. Un backend serverless permet à l’application de charger des images et d’obtenir automatiquement des légendes qui les décrivent.

![Application web en cours d’exécution](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

Le schéma suivant illustre les services Azure utilisés par l’application :

1. Le Stockage Blob traite le contenu web statique (HTML, CSS, JS) et stocke les images.
2. Azure Functions gère les chargements d’images, le redimensionnement et le stockage des métadonnées.
3. Cosmos DB stocke les métadonnées d’image.
4. Logic Apps obtient les légendes d’image à partir de l’API Vision par ordinateur.
5. Azure Active Directory gère l’authentification utilisateur.

![Diagramme de l’architecture de la solution](media/functions-first-serverless-web-app/0-architecture.jpg)

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées
> * Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions
> * Redimensionner des images à l’aide d’Azure Functions
> * Stocker des métadonnées d’image dans Azure Cosmos DB
> * Utiliser l’API Vision de Cognitive Services pour générer automatiquement des légendes d’image
> * Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs.
