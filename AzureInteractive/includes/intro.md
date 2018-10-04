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
<span data-ttu-id="6ba00-103">Dans ce didacticiel, vous déployez une application web simple qui présente une interface utilisateur HTML.</span><span class="sxs-lookup"><span data-stu-id="6ba00-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="6ba00-104">Un backend serverless permet à l’application de charger des images et d’obtenir automatiquement des légendes qui les décrivent.</span><span class="sxs-lookup"><span data-stu-id="6ba00-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Application web en cours d’exécution](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="6ba00-106">Le schéma suivant illustre les services Azure utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="6ba00-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="6ba00-107">Le Stockage Blob traite le contenu web statique (HTML, CSS, JS) et stocke les images.</span><span class="sxs-lookup"><span data-stu-id="6ba00-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="6ba00-108">Azure Functions gère les chargements d’images, le redimensionnement et le stockage des métadonnées.</span><span class="sxs-lookup"><span data-stu-id="6ba00-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="6ba00-109">Cosmos DB stocke les métadonnées d’image.</span><span class="sxs-lookup"><span data-stu-id="6ba00-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="6ba00-110">Logic Apps obtient les légendes d’image à partir de l’API Vision par ordinateur.</span><span class="sxs-lookup"><span data-stu-id="6ba00-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="6ba00-111">Azure Active Directory gère l’authentification utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6ba00-111">Azure Active Directory manages user authentication.</span></span>

![Diagramme de l’architecture de la solution](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="6ba00-113">Ce tutoriel vous montre comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="6ba00-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="6ba00-114">Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées</span><span class="sxs-lookup"><span data-stu-id="6ba00-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="6ba00-115">Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions</span><span class="sxs-lookup"><span data-stu-id="6ba00-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="6ba00-116">Redimensionner des images à l’aide d’Azure Functions</span><span class="sxs-lookup"><span data-stu-id="6ba00-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="6ba00-117">Stocker des métadonnées d’image dans Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="6ba00-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="6ba00-118">Utiliser l’API Vision de Cognitive Services pour générer automatiquement des légendes d’image</span><span class="sxs-lookup"><span data-stu-id="6ba00-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="6ba00-119">Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="6ba00-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
