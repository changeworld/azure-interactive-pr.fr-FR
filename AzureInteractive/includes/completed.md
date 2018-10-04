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
<span data-ttu-id="c62ed-103">Vous avez réussi à créer une application serverless complète à l’aide des services Azure.</span><span class="sxs-lookup"><span data-stu-id="c62ed-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="c62ed-104">Supprimer des ressources</span><span class="sxs-lookup"><span data-stu-id="c62ed-104">Clean up resources</span></span>

<span data-ttu-id="c62ed-105">Lorsque vous aurez terminé votre travail avec cette application, vous pourrez utiliser la commande suivante pour supprimer toutes les ressources créées dans le cadre du didacticiel :</span><span class="sxs-lookup"><span data-stu-id="c62ed-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="c62ed-106">Quand vous y êtes invité, tapez `y`.</span><span class="sxs-lookup"><span data-stu-id="c62ed-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="c62ed-107">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="c62ed-107">Next steps</span></span>

<span data-ttu-id="c62ed-108">Dans ce tutoriel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="c62ed-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="c62ed-109">Configurer le Stockage Blob Azure pour héberger un site web statique et des images chargées</span><span class="sxs-lookup"><span data-stu-id="c62ed-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="c62ed-110">Charger des images vers le Stockage Blob Azure à l’aide d’Azure Functions</span><span class="sxs-lookup"><span data-stu-id="c62ed-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="c62ed-111">Redimensionner des images à l’aide d’Azure Functions</span><span class="sxs-lookup"><span data-stu-id="c62ed-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="c62ed-112">Stocker des métadonnées d’image dans Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="c62ed-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="c62ed-113">Utiliser l’API Vision de Cognitive Services pour générer automatiquement des légendes d’image</span><span class="sxs-lookup"><span data-stu-id="c62ed-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="c62ed-114">Utiliser Azure Active Directory pour sécuriser l’application web en authentifiant les utilisateurs</span><span class="sxs-lookup"><span data-stu-id="c62ed-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="c62ed-115">Pour savoir comment connecter encore plus de services à Functions, passez au didacticiel Logic Apps.</span><span class="sxs-lookup"><span data-stu-id="c62ed-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="c62ed-116">Fonctions avec Logic Apps</span><span class="sxs-lookup"><span data-stu-id="c62ed-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
