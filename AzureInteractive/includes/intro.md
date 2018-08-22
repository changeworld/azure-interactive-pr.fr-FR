---
title: Fichier Include
description: Fichier Include
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079236"
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
> * Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées.
> * Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions.
> * Redimensionner des images à l’aide d’Azure Functions.
> * Stocker des métadonnées d’image dans Azure Cosmos DB.
> * Utiliser l’API Vision Cognitive Services pour générer automatiquement des légendes d’image.
> * Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs.