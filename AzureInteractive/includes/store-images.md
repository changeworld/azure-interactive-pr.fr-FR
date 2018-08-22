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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079241"
---
<span data-ttu-id="30d21-103">Azure Cosmos DB est une base de données serverless multi-modèles distribuée par Microsoft au niveau mondial.</span><span class="sxs-lookup"><span data-stu-id="30d21-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="30d21-104">Dans ce module, vous allez apprendre à utiliser Azure Functions pour stocker et récupérer des métadonnées d’image sous forme de documents JSON dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="30d21-105">Créer un compte Cosmos DB, une base de données et une collection</span><span class="sxs-lookup"><span data-stu-id="30d21-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="30d21-106">Un compte Cosmos DB est une ressource Azure qui contient des bases de données Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="30d21-107">Vérifiez que vous êtes toujours connecté à Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="30d21-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="30d21-108">Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="30d21-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="30d21-109">Créez un compte Cosmos DB avec un nom unique dans le même groupe de ressources que les autres ressources de ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="30d21-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="30d21-110">Une fois le compte Cosmos DB créé, créez une base de données nommée **imagesdb** dans le compte.</span><span class="sxs-lookup"><span data-stu-id="30d21-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="30d21-111">Une fois la base de données créée, créez une collection nommée **images** dans la base de données avec un débit de 400 unités de requête (RU).</span><span class="sxs-lookup"><span data-stu-id="30d21-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="30d21-112">Enregistrer un document dans Cosmos DB lorsqu’une miniature est créée</span><span class="sxs-lookup"><span data-stu-id="30d21-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="30d21-113">La liaison de sortie Cosmos DB vous permet de créer des documents dans une collection Cosmos DB à partir d’Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="30d21-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="30d21-114">Dans les étapes suivantes, vous allez configurer une liaison de sortie Cosmos DB dans la fonction **ResizeImage** et modifier la fonction pour retourner un document (objet) à enregistrer.</span><span class="sxs-lookup"><span data-stu-id="30d21-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="30d21-115">Ouvrez l’application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="30d21-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="30d21-116">Dans le volet de navigation de gauche, développez la fonction **ResizeImage**, puis sélectionnez **Intégrer**.</span><span class="sxs-lookup"><span data-stu-id="30d21-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="30d21-117">Sous **Sorties**, cliquez sur **Nouvelle sortie**.</span><span class="sxs-lookup"><span data-stu-id="30d21-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="30d21-118">Recherchez l’élément **Azure Cosmos DB** et sélectionnez-le.</span><span class="sxs-lookup"><span data-stu-id="30d21-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="30d21-119">Puis cliquez sur **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="30d21-119">Then click **Select**.</span></span>

    ![Sélectionner une nouvelle sortie](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="30d21-121">Renseignez les champs sous **Azure Cosmos DB output** (Sortie Azure Cosmos DB) en indiquant les valeurs suivantes.</span><span class="sxs-lookup"><span data-stu-id="30d21-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="30d21-122">Paramètre</span><span class="sxs-lookup"><span data-stu-id="30d21-122">Setting</span></span>      |  <span data-ttu-id="30d21-123">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="30d21-123">Suggested value</span></span>   | <span data-ttu-id="30d21-124">Description</span><span class="sxs-lookup"><span data-stu-id="30d21-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="30d21-125">**Nom du paramètre de document**</span><span class="sxs-lookup"><span data-stu-id="30d21-125">**Document parameter name**</span></span> | <span data-ttu-id="30d21-126">Sélectionnez **Use function return value** (Utiliser la valeur de retour de la fonction).</span><span class="sxs-lookup"><span data-stu-id="30d21-126">Select **Use function return value**</span></span> | <span data-ttu-id="30d21-127">La valeur de la zone de texte est automatiquement définie sur **$return**.</span><span class="sxs-lookup"><span data-stu-id="30d21-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="30d21-128">**Nom de la base de données**</span><span class="sxs-lookup"><span data-stu-id="30d21-128">**Database name**</span></span> | <span data-ttu-id="30d21-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="30d21-129">imagesdb</span></span> | <span data-ttu-id="30d21-130">Utilisez le nom de la base de données que vous avez créée.</span><span class="sxs-lookup"><span data-stu-id="30d21-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="30d21-131">**Nom de la collection**</span><span class="sxs-lookup"><span data-stu-id="30d21-131">**Collection name**</span></span> | <span data-ttu-id="30d21-132">images</span><span class="sxs-lookup"><span data-stu-id="30d21-132">images</span></span> | <span data-ttu-id="30d21-133">Utilisez le nom de la collection que vous avez créée.</span><span class="sxs-lookup"><span data-stu-id="30d21-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="30d21-134">En regard de **Connexion de compte Azure Cosmos DB**, cliquez sur **Nouvelle**.</span><span class="sxs-lookup"><span data-stu-id="30d21-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="30d21-135">Sélectionnez le compte Cosmos DB que vous avez créé précédemment.</span><span class="sxs-lookup"><span data-stu-id="30d21-135">Select the Cosmos DB account you previously created.</span></span>

    ![Entrer les paramètres de la liaison de sortie Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="30d21-137">Cliquez sur **Enregistrer** pour créer la liaison de sortie Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="30d21-138">Cliquez sur le nom de fonction **ResizeImage** à gauche pour ouvrir la fonction.</span><span class="sxs-lookup"><span data-stu-id="30d21-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="30d21-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="30d21-139">**C#**</span></span>

    1. <span data-ttu-id="30d21-140">(C#) Modifiez le type de retour de la fonction (**void**) en lui affectant la valeur **object**.</span><span class="sxs-lookup"><span data-stu-id="30d21-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="30d21-141">(C#) À la fin de la fonction, ajoutez le bloc de code suivant pour retourner le document à enregistrer :</span><span class="sxs-lookup"><span data-stu-id="30d21-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx pour la fonction ResizeImages après modification](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="30d21-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="30d21-143">**JavaScript**</span></span>

    1. <span data-ttu-id="30d21-144">(JavaScript) Modifiez l’instruction `context.done()` dans la clause `else` pour retourner le document à enregistrer dans Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="30d21-145">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="30d21-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="30d21-146">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="30d21-146">Click **Save**.</span></span> <span data-ttu-id="30d21-147">Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="30d21-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="30d21-148">Créer une fonction pour répertorier les images issues de Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="30d21-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="30d21-149">L’application web requiert une API pour récupérer les métadonnées d’image à partir de Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="30d21-150">Dans les étapes qui suivent,</span><span class="sxs-lookup"><span data-stu-id="30d21-150">In the following steps.</span></span> <span data-ttu-id="30d21-151">vous allez créer une fonction déclenchée par HTTP qui utilise une liaison d’entrée Cosmos DB pour interroger la collection de la base de données.</span><span class="sxs-lookup"><span data-stu-id="30d21-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="30d21-152">Dans votre application de fonction, pointez sur **Fonctions** sur la gauche, puis cliquez sur **+** pour créer une fonction.</span><span class="sxs-lookup"><span data-stu-id="30d21-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="30d21-153">Recherchez le modèle **HttpTrigger** et sélectionnez-le.</span><span class="sxs-lookup"><span data-stu-id="30d21-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="30d21-154">Utilisez ces valeurs pour créer une fonction qui génère une URL d’obtention d’images.</span><span class="sxs-lookup"><span data-stu-id="30d21-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="30d21-155">Paramètre</span><span class="sxs-lookup"><span data-stu-id="30d21-155">Setting</span></span>      |  <span data-ttu-id="30d21-156">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="30d21-156">Suggested value</span></span>   | <span data-ttu-id="30d21-157">Description</span><span class="sxs-lookup"><span data-stu-id="30d21-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="30d21-158">**Nommer votre fonction**</span><span class="sxs-lookup"><span data-stu-id="30d21-158">**Name your function**</span></span> | <span data-ttu-id="30d21-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="30d21-159">GetImages</span></span> | <span data-ttu-id="30d21-160">Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction.</span><span class="sxs-lookup"><span data-stu-id="30d21-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="30d21-161">**Niveau d’autorisation**</span><span class="sxs-lookup"><span data-stu-id="30d21-161">**Authorization level**</span></span> | <span data-ttu-id="30d21-162">Anonyme</span><span class="sxs-lookup"><span data-stu-id="30d21-162">Anonymous</span></span> | <span data-ttu-id="30d21-163">Rend la fonction accessible publiquement.</span><span class="sxs-lookup"><span data-stu-id="30d21-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="30d21-164">Cliquez sur **Créer**.</span><span class="sxs-lookup"><span data-stu-id="30d21-164">Click **Create**.</span></span>

1. <span data-ttu-id="30d21-165">Une fois la fonction créée, cliquez sur **Intégrer** sous le nom de la fonction, dans le volet de navigation de gauche.</span><span class="sxs-lookup"><span data-stu-id="30d21-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="30d21-166">Cliquez sur **Nouvelle entrée** et sélectionnez **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="30d21-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Sélectionner Nouvelle entrée](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="30d21-168">Cliquez sur **Sélectionner**.</span><span class="sxs-lookup"><span data-stu-id="30d21-168">Click **Select**.</span></span>

1. <span data-ttu-id="30d21-169">Renseignez les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="30d21-169">Fill out the following values:</span></span>

    | <span data-ttu-id="30d21-170">Paramètre</span><span class="sxs-lookup"><span data-stu-id="30d21-170">Setting</span></span>      |  <span data-ttu-id="30d21-171">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="30d21-171">Suggested value</span></span>   | <span data-ttu-id="30d21-172">Description</span><span class="sxs-lookup"><span data-stu-id="30d21-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="30d21-173">**Nom du paramètre de document**</span><span class="sxs-lookup"><span data-stu-id="30d21-173">**Document parameter name**</span></span> | <span data-ttu-id="30d21-174">documents</span><span class="sxs-lookup"><span data-stu-id="30d21-174">documents</span></span> | <span data-ttu-id="30d21-175">Correspond au nom de paramètre dans la fonction.</span><span class="sxs-lookup"><span data-stu-id="30d21-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="30d21-176">**Nom de la base de données**</span><span class="sxs-lookup"><span data-stu-id="30d21-176">**Database name**</span></span> | <span data-ttu-id="30d21-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="30d21-177">imagesdb</span></span> |  |
    | <span data-ttu-id="30d21-178">**Nom de la collection**</span><span class="sxs-lookup"><span data-stu-id="30d21-178">**Collection name**</span></span> | <span data-ttu-id="30d21-179">images</span><span class="sxs-lookup"><span data-stu-id="30d21-179">images</span></span> |  |
    | <span data-ttu-id="30d21-180">**SQL query**</span><span class="sxs-lookup"><span data-stu-id="30d21-180">**SQL query**</span></span> | <span data-ttu-id="30d21-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="30d21-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="30d21-182">Pour obtenir les documents (documents les plus récents en premier).</span><span class="sxs-lookup"><span data-stu-id="30d21-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="30d21-183">**Connexion de compte Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="30d21-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="30d21-184">Sélectionnez la chaîne de connexion existante</span><span class="sxs-lookup"><span data-stu-id="30d21-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="30d21-185">Cliquez sur **Enregistrer** pour créer la liaison d’entrée.</span><span class="sxs-lookup"><span data-stu-id="30d21-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="30d21-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="30d21-186">**C#**</span></span>

    1. <span data-ttu-id="30d21-187">Cliquez sur le nom de la fonction pour ouvrir la fenêtre de code, puis remplacez l’ensemble de **run.csx** par le contenu de [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="30d21-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="30d21-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="30d21-188">**JavaScript**</span></span>

    1. <span data-ttu-id="30d21-189">Cliquez sur le nom de la fonction pour ouvrir la fenêtre de code, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="30d21-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="30d21-190">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="30d21-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="30d21-191">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="30d21-191">Click **Save**.</span></span> <span data-ttu-id="30d21-192">Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.</span><span class="sxs-lookup"><span data-stu-id="30d21-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="30d21-193">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="30d21-193">Test the application</span></span>

1. <span data-ttu-id="30d21-194">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="30d21-194">Open the application in a browser.</span></span> <span data-ttu-id="30d21-195">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="30d21-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="30d21-196">Après quelques secondes, la miniature de la nouvelle image apparaît sur la page.</span><span class="sxs-lookup"><span data-stu-id="30d21-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="30d21-197">Dans le Portail Azure, utilisez la zone de recherche pour rechercher votre compte Cosmos DB par nom.</span><span class="sxs-lookup"><span data-stu-id="30d21-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="30d21-198">Cliquez dessus pour l’ouvrir.</span><span class="sxs-lookup"><span data-stu-id="30d21-198">Click it to open it.</span></span>

1. <span data-ttu-id="30d21-199">Cliquez sur **l’Explorateur de données** à gauche pour parcourir les collections et les documents.</span><span class="sxs-lookup"><span data-stu-id="30d21-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="30d21-200">Sous la base de données **imagesdb**, sélectionnez la collection **images**.</span><span class="sxs-lookup"><span data-stu-id="30d21-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="30d21-201">Vérifiez qu’un document a été créé pour l’image chargée.</span><span class="sxs-lookup"><span data-stu-id="30d21-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Explorateur de données affichant un document pour une image chargée](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="30d21-203">Résumé</span><span class="sxs-lookup"><span data-stu-id="30d21-203">Summary</span></span>

<span data-ttu-id="30d21-204">Dans ce module, vous avez appris à créer un compte Cosmos DB, une base de données et une collection.</span><span class="sxs-lookup"><span data-stu-id="30d21-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="30d21-205">Vous avez également appris à utiliser les liaisons Cosmos DB pour enregistrer et récupérer des métadonnées d’image dans la collection Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="30d21-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="30d21-206">Vous allez ensuite apprendre à générer automatiquement une légende pour chaque image chargée à l’aide de Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="30d21-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>