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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079187"
---
<span data-ttu-id="0905a-103">Dans le module précédent, vous avez vu comment une fonction serverless peut assurer le chargement sécurisé des images vers un stockage d’objets blob à partir d’une application web.</span><span class="sxs-lookup"><span data-stu-id="0905a-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="0905a-104">Dans ce module, vous allez créer une autre fonction serverless pour surveiller les images chargées et créer des miniatures à partir de ces images.</span><span class="sxs-lookup"><span data-stu-id="0905a-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="0905a-105">Créer un conteneur de stockage d’objets blob pour les miniatures</span><span class="sxs-lookup"><span data-stu-id="0905a-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="0905a-106">Les images en taille réelle sont stockées dans un conteneur nommé **images**.</span><span class="sxs-lookup"><span data-stu-id="0905a-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="0905a-107">Vous avez besoin d’un autre conteneur pour stocker les miniatures de ces images.</span><span class="sxs-lookup"><span data-stu-id="0905a-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="0905a-108">Vérifiez que vous êtes toujours connecté à Cloud Shell (Bash).</span><span class="sxs-lookup"><span data-stu-id="0905a-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="0905a-109">Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="0905a-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="0905a-110">Créez un conteneur nommé **thumbnails** dans votre compte de stockage avec un accès public à tous les blobs.</span><span class="sxs-lookup"><span data-stu-id="0905a-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="0905a-111">Créer une fonction serverless déclenchée par blob</span><span class="sxs-lookup"><span data-stu-id="0905a-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="0905a-112">Un déclencheur définit la façon dont une fonction est appelée.</span><span class="sxs-lookup"><span data-stu-id="0905a-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="0905a-113">La fonction que vous allez créer utilise un déclencheur de blob : la fonction est appelée automatiquement lors du chargement d’un blob (fichier image) dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="0905a-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="0905a-114">Une fonction doit avoir un déclencheur.</span><span class="sxs-lookup"><span data-stu-id="0905a-114">A function must have one trigger.</span></span> <span data-ttu-id="0905a-115">Les déclencheurs sont associés à des données, généralement la charge utile qui a déclenché la fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="0905a-116">Les liaisons définissent la façon dont une fonction lit ou écrit des données dans Azure ou des services tiers.</span><span class="sxs-lookup"><span data-stu-id="0905a-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="0905a-117">Cette fonction crée une version miniature de l’image qui la déclenche et enregistre la miniature dans un conteneur *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="0905a-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="0905a-118">Ouvrez votre application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="0905a-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="0905a-119">Dans le volet de navigation de gauche de la fenêtre d’applications de fonction, pointez sur **Fonctions** et cliquez sur **+** pour commencer à créer une fonction serverless.</span><span class="sxs-lookup"><span data-stu-id="0905a-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="0905a-120">Si une page de démarrage rapide s’affiche, cliquez sur **Fonction personnalisée** pour afficher la liste des modèles de fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="0905a-121">Recherchez le modèle **BlobTrigger** et sélectionnez-le.</span><span class="sxs-lookup"><span data-stu-id="0905a-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="0905a-122">Utilisez ces valeurs pour créer une fonction qui crée des miniatures à mesure que les images sont chargées.</span><span class="sxs-lookup"><span data-stu-id="0905a-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="0905a-123">Paramètre</span><span class="sxs-lookup"><span data-stu-id="0905a-123">Setting</span></span>      |  <span data-ttu-id="0905a-124">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="0905a-124">Suggested value</span></span>   | <span data-ttu-id="0905a-125">Description</span><span class="sxs-lookup"><span data-stu-id="0905a-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="0905a-126">**Langage**</span><span class="sxs-lookup"><span data-stu-id="0905a-126">**Language**</span></span> | <span data-ttu-id="0905a-127">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="0905a-127">C# or JavaScript</span></span> | <span data-ttu-id="0905a-128">Sélectionnez votre langage préféré.</span><span class="sxs-lookup"><span data-stu-id="0905a-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="0905a-129">**Nommer votre fonction**</span><span class="sxs-lookup"><span data-stu-id="0905a-129">**Name your function**</span></span> | <span data-ttu-id="0905a-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="0905a-130">ResizeImage</span></span> | <span data-ttu-id="0905a-131">Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="0905a-132">**Chemin d’accès**</span><span class="sxs-lookup"><span data-stu-id="0905a-132">**Path**</span></span> | <span data-ttu-id="0905a-133">images/{nom}</span><span class="sxs-lookup"><span data-stu-id="0905a-133">images/{name}</span></span> | <span data-ttu-id="0905a-134">Exécute la fonction lorsqu’un fichier apparaît dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="0905a-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="0905a-135">**Informations sur le compte de stockage**</span><span class="sxs-lookup"><span data-stu-id="0905a-135">**Storage account information**</span></span> | <span data-ttu-id="0905a-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="0905a-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="0905a-137">Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="0905a-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Saisie des paramètres pour la nouvelle fonction](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="0905a-139">Cliquez sur **Créer** pour créer la fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="0905a-140">Une fois la fonction créée, cliquez sur **Intégrer** pour afficher son déclencheur, son entrée et ses liaisons de sortie.</span><span class="sxs-lookup"><span data-stu-id="0905a-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="0905a-141">Cliquez sur **Nouvelle sortie** pour créer une liaison de sortie.</span><span class="sxs-lookup"><span data-stu-id="0905a-141">Click **New output** to create a new output binding.</span></span>

    ![Sélectionner Nouvelle sortie dans l’onglet Intégrer](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="0905a-143">Sélectionnez **Stockage Blob Azure**, puis cliquez sur **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="0905a-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="0905a-144">Vous devrez peut-être faire défiler l’écran pour voir le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="0905a-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Sélectionner Stockage Blob Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="0905a-146">Saisissez les valeurs suivantes.</span><span class="sxs-lookup"><span data-stu-id="0905a-146">Enter the following values.</span></span>

    | <span data-ttu-id="0905a-147">Paramètre</span><span class="sxs-lookup"><span data-stu-id="0905a-147">Setting</span></span>      |  <span data-ttu-id="0905a-148">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="0905a-148">Suggested value</span></span>   | <span data-ttu-id="0905a-149">Description</span><span class="sxs-lookup"><span data-stu-id="0905a-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="0905a-150">**Nom du paramètre de blob**</span><span class="sxs-lookup"><span data-stu-id="0905a-150">**Blob parameter name**</span></span> | <span data-ttu-id="0905a-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="0905a-151">thumbnail</span></span> | <span data-ttu-id="0905a-152">La fonction utilise le paramètre portant ce nom pour écrire la miniature.</span><span class="sxs-lookup"><span data-stu-id="0905a-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="0905a-153">**Use function return value** (Utiliser la valeur de retour de la fonction)</span><span class="sxs-lookup"><span data-stu-id="0905a-153">**Use function return value**</span></span> | <span data-ttu-id="0905a-154">Non </span><span class="sxs-lookup"><span data-stu-id="0905a-154">No</span></span> |  |
    | <span data-ttu-id="0905a-155">**Chemin d’accès**</span><span class="sxs-lookup"><span data-stu-id="0905a-155">**Path**</span></span> | <span data-ttu-id="0905a-156">thumbnails/{nom}</span><span class="sxs-lookup"><span data-stu-id="0905a-156">thumbnails/{name}</span></span> | <span data-ttu-id="0905a-157">Les miniatures sont écrites dans un conteneur nommé **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="0905a-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="0905a-158">**Connexion au compte de stockage**</span><span class="sxs-lookup"><span data-stu-id="0905a-158">**Storage account connection**</span></span> | <span data-ttu-id="0905a-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="0905a-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="0905a-160">Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="0905a-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Saisie des paramètres de la liaison de sortie de blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="0905a-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="0905a-162">**JavaScript**</span></span>

    1. <span data-ttu-id="0905a-163">(JavaScript) Cliquez sur **Éditeur avancé** dans l’angle supérieur droit de la fenêtre pour faire apparaître le document JSON représentant les liaisons.</span><span class="sxs-lookup"><span data-stu-id="0905a-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="0905a-164">(JavaScript) Dans la liaison `blobTrigger`, ajoutez une propriété nommée `dataType` avec la valeur `binary`.</span><span class="sxs-lookup"><span data-stu-id="0905a-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="0905a-165">Ceci configure la liaison pour transmettre le contenu du blob à la fonction en tant que données binaires.</span><span class="sxs-lookup"><span data-stu-id="0905a-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="0905a-166">Cliquez sur **Enregistrer** pour créer la liaison.</span><span class="sxs-lookup"><span data-stu-id="0905a-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="0905a-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="0905a-167">**C#**</span></span>

    1. <span data-ttu-id="0905a-168">(C#) Sélectionnez le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour ouvrir le code source de la fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="0905a-169">(C#) La fonction requiert un package NuGet appelé **ImageResizer** pour générer les miniatures.</span><span class="sxs-lookup"><span data-stu-id="0905a-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="0905a-170">Les packages NuGet sont ajoutés aux fonctions C# à l’aide d’un fichier **project.json**.</span><span class="sxs-lookup"><span data-stu-id="0905a-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="0905a-171">Pour créer le fichier, cliquez sur **Afficher les fichiers** sur la droite pour afficher les fichiers qui composent la fonction.</span><span class="sxs-lookup"><span data-stu-id="0905a-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="0905a-172">(C#) Cliquez sur **Ajouter** pour ajouter un nouveau fichier nommé **project.json**.</span><span class="sxs-lookup"><span data-stu-id="0905a-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="0905a-173">(C#) Copiez le contenu du fichier [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) dans le nouveau fichier créé.</span><span class="sxs-lookup"><span data-stu-id="0905a-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="0905a-174">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="0905a-174">Save the file.</span></span> <span data-ttu-id="0905a-175">Les packages sont restaurés automatiquement lorsque le fichier est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="0905a-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Fichier project.json avec ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="0905a-177">(C#) Sélectionnez **run.csx** sous **Afficher les fichiers** et remplacez son contenu par celui de [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="0905a-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="0905a-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="0905a-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="0905a-179">(JavaScript) Cette fonction requiert le package `jimp` de npm pour redimensionner la photo.</span><span class="sxs-lookup"><span data-stu-id="0905a-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="0905a-180">Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.</span><span class="sxs-lookup"><span data-stu-id="0905a-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="0905a-181">(JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.</span><span class="sxs-lookup"><span data-stu-id="0905a-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="0905a-182">(JavaScript) Exécutez la commande `npm install jimp` dans la console.</span><span class="sxs-lookup"><span data-stu-id="0905a-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="0905a-183">L’exécution de l’opération peut prendre une ou deux minutes.</span><span class="sxs-lookup"><span data-stu-id="0905a-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="0905a-184">(JavaScript) Cliquez sur le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/ResizeImage / index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="0905a-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="0905a-185">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="0905a-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="0905a-186">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="0905a-186">Click **Save**.</span></span> <span data-ttu-id="0905a-187">Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="0905a-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="0905a-188">Tester la fonction serverless</span><span class="sxs-lookup"><span data-stu-id="0905a-188">Test the serverless function</span></span>

1. <span data-ttu-id="0905a-189">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="0905a-189">Open the application in a browser.</span></span> <span data-ttu-id="0905a-190">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="0905a-190">Select an image file and upload it.</span></span> <span data-ttu-id="0905a-191">Le chargement s’effectue, mais l’application n’affiche pas la photo chargée. En effet, nous n’avons pas encore ajouté la possibilité d’afficher des images.</span><span class="sxs-lookup"><span data-stu-id="0905a-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="0905a-192">Dans Cloud Shell, vérifiez que l’image a été chargée dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="0905a-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="0905a-193">Vérifiez que la miniature a été créée dans un conteneur nommé **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="0905a-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="0905a-194">Obtenez l’URL de la miniature.</span><span class="sxs-lookup"><span data-stu-id="0905a-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="0905a-195">Ouvrez l’URL dans un navigateur et vérifiez que la miniature a été créée correctement.</span><span class="sxs-lookup"><span data-stu-id="0905a-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="0905a-196">Avant de passer au didacticiel suivant, supprimez tous les fichiers des conteneurs **images** et **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="0905a-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="0905a-197">Résumé</span><span class="sxs-lookup"><span data-stu-id="0905a-197">Summary</span></span>

<span data-ttu-id="0905a-198">Dans ce module, vous avez créé une fonction serverless pour créer une miniature chaque fois qu’une image est chargée dans un conteneur de stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="0905a-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="0905a-199">Vous allez ensuite apprendre à utiliser Azure Cosmos DB pour stocker et répertorier les métadonnées d’image.</span><span class="sxs-lookup"><span data-stu-id="0905a-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
