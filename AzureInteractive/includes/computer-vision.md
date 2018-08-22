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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079254"
---
<span data-ttu-id="d34fc-103">À ce stade, l’application est une galerie fonctionnelle, qui vous permet de charger et d’afficher des images.</span><span class="sxs-lookup"><span data-stu-id="d34fc-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="d34fc-104">Dans ce module, vous allez apprendre à utiliser l’API Vision par ordinateur de Microsoft Cognitive Services afin de générer des légendes pour des images chargées et d’enregistrer les légendes avec les métadonnées d’image dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d34fc-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="d34fc-105">Créer un compte Vision par ordinateur</span><span class="sxs-lookup"><span data-stu-id="d34fc-105">Create a Computer Vision account</span></span>

<span data-ttu-id="d34fc-106">Microsoft Cognitive Services est une collection de services permettant aux développeurs de créer des applications plus intelligentes.</span><span class="sxs-lookup"><span data-stu-id="d34fc-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="d34fc-107">L’API Vision par ordinateur est un service serverless qui traite les images à l’aide d’algorithmes avancés et retourne des informations sur chaque image.</span><span class="sxs-lookup"><span data-stu-id="d34fc-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="d34fc-108">Elle offre un niveau gratuit fournissant jusqu’à 5 000 appels d’API par mois.</span><span class="sxs-lookup"><span data-stu-id="d34fc-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="d34fc-109">Vérifiez que vous êtes toujours connecté à Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="d34fc-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="d34fc-110">Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="d34fc-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="d34fc-111">Créez un compte Cognitive Services de type **ComputerVision** avec un nom unique dans votre groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="d34fc-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="d34fc-112">Pour le niveau gratuit, utilisez **F0** en tant que référence SKU.</span><span class="sxs-lookup"><span data-stu-id="d34fc-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="d34fc-113">Si vous disposez déjà d’un compte Vision par ordinateur, vous devrez peut-être créer un compte Standard (S1), ce qui peut entraîner certains coûts.</span><span class="sxs-lookup"><span data-stu-id="d34fc-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="d34fc-114">Créer des paramètres Function App pour l’URL et la clé Vision par ordinateur</span><span class="sxs-lookup"><span data-stu-id="d34fc-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="d34fc-115">Une URL et une clé sont requises pour appeler l’API Vision par ordinateur.</span><span class="sxs-lookup"><span data-stu-id="d34fc-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="d34fc-116">Récupérez la clé et l’URL de l’API Vision par ordinateur, et enregistrez-les dans des variables Bash.</span><span class="sxs-lookup"><span data-stu-id="d34fc-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="d34fc-117">Dans l’application de fonction, créez des paramètres d’application nommés respectivement **COMP_VISION_KEY** et **COMP_VISION_URL**.</span><span class="sxs-lookup"><span data-stu-id="d34fc-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="d34fc-118">Appeler l’API Vision par ordinateur à partir de la fonction ResizeImage</span><span class="sxs-lookup"><span data-stu-id="d34fc-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="d34fc-119">Dans les étapes suivantes, vous allez modifier la fonction **ResizeImage** pour appeler l’API Vision par ordinateur afin de décrire chaque image chargée et d’enregistrer la description dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d34fc-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="d34fc-120">Ouvrez votre application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="d34fc-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="d34fc-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="d34fc-121">**C#**</span></span>

    1. <span data-ttu-id="d34fc-122">(C#) Dans le volet de navigation de gauche, localisez la fonction **ResizeImage** et ouvrez sa fenêtre de code.</span><span class="sxs-lookup"><span data-stu-id="d34fc-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="d34fc-123">(C#) Remplacez le code par le contenu de [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="d34fc-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="d34fc-124">Ce code utilise `HttpClient` pour appeler l’API Vision par ordinateur et enregistrer son résultat dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d34fc-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="d34fc-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="d34fc-125">**JavaScript**</span></span>

    1. <span data-ttu-id="d34fc-126">(JavaScript) Cette fonction requiert le package `axios` de npm pour adresser un appel HTTP à l’API Vision par ordinateur.</span><span class="sxs-lookup"><span data-stu-id="d34fc-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="d34fc-127">Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.</span><span class="sxs-lookup"><span data-stu-id="d34fc-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="d34fc-128">(JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.</span><span class="sxs-lookup"><span data-stu-id="d34fc-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="d34fc-129">(JavaScript) Exécutez la commande `npm install axios` dans la console.</span><span class="sxs-lookup"><span data-stu-id="d34fc-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="d34fc-130">L’exécution de l’opération peut prendre une ou deux minutes.</span><span class="sxs-lookup"><span data-stu-id="d34fc-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="d34fc-131">(JavaScript) Cliquez sur le nom de la fonction (**ResizeImage**) dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="d34fc-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="d34fc-132">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="d34fc-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="d34fc-133">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="d34fc-133">Click **Save**.</span></span> <span data-ttu-id="d34fc-134">Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="d34fc-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="d34fc-135">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="d34fc-135">Test the application</span></span>

1. <span data-ttu-id="d34fc-136">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="d34fc-136">Open the application in a browser.</span></span> <span data-ttu-id="d34fc-137">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="d34fc-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="d34fc-138">Après quelques secondes, la miniature de la nouvelle image doit apparaître sur la page.</span><span class="sxs-lookup"><span data-stu-id="d34fc-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="d34fc-139">Pointez sur l’image pour afficher la description générée par l’API Vision par ordinateur.</span><span class="sxs-lookup"><span data-stu-id="d34fc-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="d34fc-140">Résumé</span><span class="sxs-lookup"><span data-stu-id="d34fc-140">Summary</span></span>

<span data-ttu-id="d34fc-141">Dans ce module, vous avez appris à générer automatiquement des légendes pour chaque image chargée à l’aide de l’API Vision par ordinateur de Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="d34fc-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="d34fc-142">Vous allez ensuite apprendre à ajouter l’authentification à l’application à l’aide de l’authentification Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d34fc-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>