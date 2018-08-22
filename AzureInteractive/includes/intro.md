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
<span data-ttu-id="7f1f9-103">Dans ce didacticiel, vous déployez une application web simple qui présente une interface utilisateur HTML.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="7f1f9-104">Un backend serverless permet à l’application de charger des images et d’obtenir automatiquement des légendes qui les décrivent.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Application web en cours d’exécution](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="7f1f9-106">Le schéma suivant illustre les services Azure utilisés par l’application :</span><span class="sxs-lookup"><span data-stu-id="7f1f9-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="7f1f9-107">Le Stockage Blob traite le contenu web statique (HTML, CSS, JS) et stocke les images.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="7f1f9-108">Azure Functions gère les chargements d’images, le redimensionnement et le stockage des métadonnées.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="7f1f9-109">Cosmos DB stocke les métadonnées d’image.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="7f1f9-110">Logic Apps obtient les légendes d’image à partir de l’API Vision par ordinateur.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="7f1f9-111">Azure Active Directory gère l’authentification utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-111">Azure Active Directory manages user authentication.</span></span>

![Diagramme de l’architecture de la solution](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="7f1f9-113">Ce tutoriel vous montre comment effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7f1f9-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="7f1f9-114">Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="7f1f9-115">Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="7f1f9-116">Redimensionner des images à l’aide d’Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="7f1f9-117">Stocker des métadonnées d’image dans Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="7f1f9-118">Utiliser l’API Vision Cognitive Services pour générer automatiquement des légendes d’image.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="7f1f9-119">Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="7f1f9-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>