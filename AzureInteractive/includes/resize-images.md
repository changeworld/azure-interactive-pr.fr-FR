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
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460078"
---
<span data-ttu-id="c71c6-103">Dans le module précédent, vous avez vu comment une fonction serverless peut assurer le chargement sécurisé des images vers un stockage d’objets blob à partir d’une application web.</span><span class="sxs-lookup"><span data-stu-id="c71c6-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="c71c6-104">Dans ce module, vous allez créer une autre fonction serverless pour surveiller les images chargées et créer des miniatures à partir de ces images.</span><span class="sxs-lookup"><span data-stu-id="c71c6-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="c71c6-105">Créer un conteneur de stockage d’objets blob pour les miniatures</span><span class="sxs-lookup"><span data-stu-id="c71c6-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="c71c6-106">Les images en taille réelle sont stockées dans un conteneur nommé **images**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="c71c6-107">Vous avez besoin d’un autre conteneur pour stocker les miniatures de ces images.</span><span class="sxs-lookup"><span data-stu-id="c71c6-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="c71c6-108">Vérifiez que vous êtes toujours connecté à Cloud Shell (Bash).</span><span class="sxs-lookup"><span data-stu-id="c71c6-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="c71c6-109">Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="c71c6-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="c71c6-110">Créez un conteneur nommé **thumbnails** dans votre compte de stockage avec un accès public à tous les blobs.</span><span class="sxs-lookup"><span data-stu-id="c71c6-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="c71c6-111">Créer une fonction serverless déclenchée par blob</span><span class="sxs-lookup"><span data-stu-id="c71c6-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="c71c6-112">Un déclencheur définit la façon dont une fonction est appelée.</span><span class="sxs-lookup"><span data-stu-id="c71c6-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="c71c6-113">La fonction que vous allez créer utilise un déclencheur de blob : la fonction est appelée automatiquement lors du chargement d’un blob (fichier image) dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="c71c6-114">Une fonction doit avoir un déclencheur.</span><span class="sxs-lookup"><span data-stu-id="c71c6-114">A function must have one trigger.</span></span> <span data-ttu-id="c71c6-115">Les déclencheurs sont associés à des données, généralement la charge utile qui a déclenché la fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="c71c6-116">Les liaisons définissent la façon dont une fonction lit ou écrit des données dans Azure ou des services tiers.</span><span class="sxs-lookup"><span data-stu-id="c71c6-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="c71c6-117">Cette fonction crée une version miniature de l’image qui la déclenche et enregistre la miniature dans un conteneur *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="c71c6-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="c71c6-118">Ouvrez votre application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="c71c6-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="c71c6-119">Dans le volet de navigation de gauche de la fenêtre d’applications de fonction, pointez sur **Fonctions** et cliquez sur **+** pour commencer à créer une fonction serverless.</span><span class="sxs-lookup"><span data-stu-id="c71c6-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="c71c6-120">Si une page de démarrage rapide s’affiche, cliquez sur **Fonction personnalisée** pour afficher la liste des modèles de fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="c71c6-121">Recherchez le modèle **BlobTrigger** et sélectionnez-le.</span><span class="sxs-lookup"><span data-stu-id="c71c6-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="c71c6-122">Utilisez ces valeurs pour créer une fonction qui crée des miniatures à mesure que les images sont chargées.</span><span class="sxs-lookup"><span data-stu-id="c71c6-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="c71c6-123">Paramètre</span><span class="sxs-lookup"><span data-stu-id="c71c6-123">Setting</span></span>      |  <span data-ttu-id="c71c6-124">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="c71c6-124">Suggested value</span></span>   | <span data-ttu-id="c71c6-125">Description</span><span class="sxs-lookup"><span data-stu-id="c71c6-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="c71c6-126">**Langage**</span><span class="sxs-lookup"><span data-stu-id="c71c6-126">**Language**</span></span> | <span data-ttu-id="c71c6-127">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="c71c6-127">C# or JavaScript</span></span> | <span data-ttu-id="c71c6-128">Sélectionnez votre langage préféré.</span><span class="sxs-lookup"><span data-stu-id="c71c6-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="c71c6-129">**Nommer votre fonction**</span><span class="sxs-lookup"><span data-stu-id="c71c6-129">**Name your function**</span></span> | <span data-ttu-id="c71c6-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="c71c6-130">ResizeImage</span></span> | <span data-ttu-id="c71c6-131">Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="c71c6-132">**Chemin d’accès**</span><span class="sxs-lookup"><span data-stu-id="c71c6-132">**Path**</span></span> | <span data-ttu-id="c71c6-133">images/{nom}</span><span class="sxs-lookup"><span data-stu-id="c71c6-133">images/{name}</span></span> | <span data-ttu-id="c71c6-134">Exécute la fonction lorsqu’un fichier apparaît dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="c71c6-135">**Informations sur le compte de stockage**</span><span class="sxs-lookup"><span data-stu-id="c71c6-135">**Storage account information**</span></span> | <span data-ttu-id="c71c6-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="c71c6-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="c71c6-137">Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="c71c6-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Saisie des paramètres pour la nouvelle fonction](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="c71c6-139">Cliquez sur **Créer** pour créer la fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="c71c6-140">Une fois la fonction créée, cliquez sur **Intégrer** pour afficher son déclencheur, son entrée et ses liaisons de sortie.</span><span class="sxs-lookup"><span data-stu-id="c71c6-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="c71c6-141">Cliquez sur **Nouvelle sortie** pour créer une liaison de sortie.</span><span class="sxs-lookup"><span data-stu-id="c71c6-141">Click **New output** to create a new output binding.</span></span>

    ![Sélectionner Nouvelle sortie dans l’onglet Intégrer](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="c71c6-143">Sélectionnez **Stockage Blob Azure**, puis cliquez sur **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="c71c6-144">Vous devrez peut-être faire défiler l’écran pour voir le bouton **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Sélectionner Stockage Blob Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="c71c6-146">Saisissez les valeurs suivantes.</span><span class="sxs-lookup"><span data-stu-id="c71c6-146">Enter the following values.</span></span>

    | <span data-ttu-id="c71c6-147">Paramètre</span><span class="sxs-lookup"><span data-stu-id="c71c6-147">Setting</span></span>      |  <span data-ttu-id="c71c6-148">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="c71c6-148">Suggested value</span></span>   | <span data-ttu-id="c71c6-149">Description</span><span class="sxs-lookup"><span data-stu-id="c71c6-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="c71c6-150">**Nom du paramètre de blob**</span><span class="sxs-lookup"><span data-stu-id="c71c6-150">**Blob parameter name**</span></span> | <span data-ttu-id="c71c6-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="c71c6-151">thumbnail</span></span> | <span data-ttu-id="c71c6-152">La fonction utilise le paramètre portant ce nom pour écrire la miniature.</span><span class="sxs-lookup"><span data-stu-id="c71c6-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="c71c6-153">**Use function return value** (Utiliser la valeur de retour de la fonction)</span><span class="sxs-lookup"><span data-stu-id="c71c6-153">**Use function return value**</span></span> | <span data-ttu-id="c71c6-154">Non </span><span class="sxs-lookup"><span data-stu-id="c71c6-154">No</span></span> |  |
    | <span data-ttu-id="c71c6-155">**Chemin d’accès**</span><span class="sxs-lookup"><span data-stu-id="c71c6-155">**Path**</span></span> | <span data-ttu-id="c71c6-156">thumbnails/{nom}</span><span class="sxs-lookup"><span data-stu-id="c71c6-156">thumbnails/{name}</span></span> | <span data-ttu-id="c71c6-157">Les miniatures sont écrites dans un conteneur nommé **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="c71c6-158">**Connexion au compte de stockage**</span><span class="sxs-lookup"><span data-stu-id="c71c6-158">**Storage account connection**</span></span> | <span data-ttu-id="c71c6-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="c71c6-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="c71c6-160">Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion.</span><span class="sxs-lookup"><span data-stu-id="c71c6-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Saisie des paramètres de la liaison de sortie de blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="c71c6-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c71c6-162">**JavaScript**</span></span>

    1. <span data-ttu-id="c71c6-163">(JavaScript) Cliquez sur **Éditeur avancé** dans l’angle supérieur droit de la fenêtre pour faire apparaître le document JSON représentant les liaisons.</span><span class="sxs-lookup"><span data-stu-id="c71c6-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="c71c6-164">(JavaScript) Dans la liaison `blobTrigger`, ajoutez une propriété nommée `dataType` avec la valeur `binary`.</span><span class="sxs-lookup"><span data-stu-id="c71c6-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="c71c6-165">Ceci configure la liaison pour transmettre le contenu du blob à la fonction en tant que données binaires.</span><span class="sxs-lookup"><span data-stu-id="c71c6-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

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

1. <span data-ttu-id="c71c6-166">Cliquez sur **Enregistrer** pour créer la liaison.</span><span class="sxs-lookup"><span data-stu-id="c71c6-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="c71c6-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="c71c6-167">**C#**</span></span>

    1. <span data-ttu-id="c71c6-168">(C#) Sélectionnez le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour ouvrir le code source de la fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="c71c6-169">(C#) La fonction requiert un package NuGet appelé **ImageResizer** pour générer les miniatures.</span><span class="sxs-lookup"><span data-stu-id="c71c6-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="c71c6-170">Les packages NuGet sont ajoutés aux fonctions C# à l’aide d’un fichier **project.json**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="c71c6-171">Pour créer le fichier, cliquez sur **Afficher les fichiers** sur la droite pour afficher les fichiers qui composent la fonction.</span><span class="sxs-lookup"><span data-stu-id="c71c6-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="c71c6-172">(C#) Cliquez sur **Ajouter** pour ajouter un nouveau fichier nommé **project.json**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="c71c6-173">(C#) Copiez le contenu du fichier [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) dans le nouveau fichier créé.</span><span class="sxs-lookup"><span data-stu-id="c71c6-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="c71c6-174">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="c71c6-174">Save the file.</span></span> <span data-ttu-id="c71c6-175">Les packages sont restaurés automatiquement lorsque le fichier est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="c71c6-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Fichier project.json avec ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="c71c6-177">(C#) Sélectionnez **run.csx** sous **Afficher les fichiers** et remplacez son contenu par celui de [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="c71c6-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="c71c6-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c71c6-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="c71c6-179">(JavaScript) Cette fonction requiert le package `jimp` de npm pour redimensionner la photo.</span><span class="sxs-lookup"><span data-stu-id="c71c6-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="c71c6-180">Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="c71c6-181">(JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.</span><span class="sxs-lookup"><span data-stu-id="c71c6-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="c71c6-182">(JavaScript) Exécutez la commande `npm install jimp` dans la console.</span><span class="sxs-lookup"><span data-stu-id="c71c6-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="c71c6-183">L’exécution de l’opération peut prendre une ou deux minutes.</span><span class="sxs-lookup"><span data-stu-id="c71c6-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="c71c6-184">(JavaScript) Cliquez sur le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/ResizeImage / index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="c71c6-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="c71c6-185">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="c71c6-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="c71c6-186">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-186">Click **Save**.</span></span> <span data-ttu-id="c71c6-187">Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="c71c6-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="c71c6-188">Tester la fonction serverless</span><span class="sxs-lookup"><span data-stu-id="c71c6-188">Test the serverless function</span></span>

1. <span data-ttu-id="c71c6-189">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="c71c6-189">Open the application in a browser.</span></span> <span data-ttu-id="c71c6-190">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="c71c6-190">Select an image file and upload it.</span></span> <span data-ttu-id="c71c6-191">Le chargement s’effectue, mais l’application n’affiche pas la photo chargée. En effet, nous n’avons pas encore ajouté la possibilité d’afficher des images.</span><span class="sxs-lookup"><span data-stu-id="c71c6-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="c71c6-192">Dans Cloud Shell, vérifiez que l’image a été chargée dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="c71c6-193">Vérifiez que la miniature a été créée dans un conteneur nommé **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="c71c6-194">Obtenez l’URL de la miniature.</span><span class="sxs-lookup"><span data-stu-id="c71c6-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="c71c6-195">Ouvrez l’URL dans un navigateur et vérifiez que la miniature a été créée correctement.</span><span class="sxs-lookup"><span data-stu-id="c71c6-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="c71c6-196">Avant de passer au didacticiel suivant, supprimez tous les fichiers des conteneurs **images** et **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="c71c6-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="c71c6-197">Résumé</span><span class="sxs-lookup"><span data-stu-id="c71c6-197">Summary</span></span>

<span data-ttu-id="c71c6-198">Dans ce module, vous avez créé une fonction serverless pour créer une miniature chaque fois qu’une image est chargée dans un conteneur de stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="c71c6-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="c71c6-199">Vous allez ensuite apprendre à utiliser Azure Cosmos DB pour stocker et répertorier les métadonnées d’image.</span><span class="sxs-lookup"><span data-stu-id="c71c6-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
