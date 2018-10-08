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
ms.openlocfilehash: 51c7d3e64424d499b473f3b138ce249a9cfd0182
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460079"
---
<span data-ttu-id="18bfa-103">L’application que vous générez est une galerie de photos.</span><span class="sxs-lookup"><span data-stu-id="18bfa-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="18bfa-104">Elle utilise du code JavaScript côté client pour appeler des API afin de charger et d’afficher des images.</span><span class="sxs-lookup"><span data-stu-id="18bfa-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="18bfa-105">Dans ce module, vous allez créer une API à l’aide d’une fonction serverless qui génère une URL limitée dans le temps pour charger une image.</span><span class="sxs-lookup"><span data-stu-id="18bfa-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="18bfa-106">L’application web utilise l’URL générée pour charger une image dans le stockage d’objets blob à l’aide de [l’API REST Stockage Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="18bfa-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="18bfa-107">Créer un conteneur de stockage d’objets blob pour les images</span><span class="sxs-lookup"><span data-stu-id="18bfa-107">Create a blob storage container for images</span></span>

<span data-ttu-id="18bfa-108">L’application requiert un conteneur de stockage distinct pour charger et héberger les images.</span><span class="sxs-lookup"><span data-stu-id="18bfa-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="18bfa-109">Vérifiez que vous êtes toujours connecté à Cloud Shell (Bash).</span><span class="sxs-lookup"><span data-stu-id="18bfa-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="18bfa-110">Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="18bfa-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="18bfa-111">Créez un conteneur nommé **images** dans votre compte de stockage avec un accès public à tous les blobs.</span><span class="sxs-lookup"><span data-stu-id="18bfa-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="18bfa-112">Création d’une application Azure Function</span><span class="sxs-lookup"><span data-stu-id="18bfa-112">Create an Azure Function app</span></span>

<span data-ttu-id="18bfa-113">Azure Functions est un service permettant d’exécuter des fonctions serverless.</span><span class="sxs-lookup"><span data-stu-id="18bfa-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="18bfa-114">Une fonction serverless peut être déclenchée (appelée) par des événements tels qu’une requête HTTP ou lorsqu’un blob est créé dans un conteneur de stockage.</span><span class="sxs-lookup"><span data-stu-id="18bfa-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="18bfa-115">Une application de fonction Azure est un conteneur utilisé pour une ou plusieurs fonctions serverless.</span><span class="sxs-lookup"><span data-stu-id="18bfa-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="18bfa-116">Créez une application de fonction Azure avec un nom unique dans le groupe de ressources que vous avez créé précédemment. Nommez-la **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="18bfa-117">Les applications de fonction requièrent un compte de stockage. Dans ce didacticiel, vous utilisez le compte de stockage existant.</span><span class="sxs-lookup"><span data-stu-id="18bfa-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="18bfa-118">Configurer l’application de fonction</span><span class="sxs-lookup"><span data-stu-id="18bfa-118">Configure the function app</span></span>

<span data-ttu-id="18bfa-119">L’application de fonction dans ce didacticiel requiert la version 1.x du runtime Functions.</span><span class="sxs-lookup"><span data-stu-id="18bfa-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="18bfa-120">Définir le paramètre d’application `FUNCTIONS_WORKER_RUNTIME` sur `~1` épingle l’application de fonction à la dernière version 1.x.</span><span class="sxs-lookup"><span data-stu-id="18bfa-120">Setting the `FUNCTIONS_WORKER_RUNTIME` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="18bfa-121">Configurez les paramètres d’application à l’aide de la commande [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).</span><span class="sxs-lookup"><span data-stu-id="18bfa-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="18bfa-122">Dans la commande Azure CLI suivante, « <app_name> » est le nom de votre application de fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_WORKER_RUNTIME=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="18bfa-123">Créer une fonction serverless déclenchée par HTTP</span><span class="sxs-lookup"><span data-stu-id="18bfa-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="18bfa-124">L’application web de galerie de photos adresse une requête HTTP à une fonction serverless pour générer une URL limitée dans le temps et ainsi charger en toute sécurité une image dans le stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="18bfa-125">La fonction est déclenchée par une requête HTTP et utilise le Kit de développement logiciel (SDK) Stockage Azure pour générer et retourner l’URL sécurisée.</span><span class="sxs-lookup"><span data-stu-id="18bfa-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="18bfa-126">Une fois l’application de fonction créée, recherchez-la dans le Portail Azure à l’aide de la zone de recherche, puis cliquez dessus pour l’ouvrir.</span><span class="sxs-lookup"><span data-stu-id="18bfa-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Ouvrir l’application de fonction](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="18bfa-128">Dans le volet de navigation de gauche de la fenêtre d’applications de fonction, pointez sur **Fonctions** et cliquez sur **+** pour commencer à créer une fonction serverless.</span><span class="sxs-lookup"><span data-stu-id="18bfa-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Créer une fonction](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="18bfa-130">Cliquez sur **Fonction personnalisée** pour afficher la liste des modèles de fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="18bfa-131">Recherchez le modèle **HttpTrigger** et cliquez sur le langage à utiliser (C# ou JavaScript).</span><span class="sxs-lookup"><span data-stu-id="18bfa-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="18bfa-132">Utilisez ces valeurs pour créer une fonction qui génère une URL de chargement de blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="18bfa-133">Paramètre</span><span class="sxs-lookup"><span data-stu-id="18bfa-133">Setting</span></span>      |  <span data-ttu-id="18bfa-134">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="18bfa-134">Suggested value</span></span>   | <span data-ttu-id="18bfa-135">Description</span><span class="sxs-lookup"><span data-stu-id="18bfa-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="18bfa-136">**Langage**</span><span class="sxs-lookup"><span data-stu-id="18bfa-136">**Language**</span></span> | <span data-ttu-id="18bfa-137">C# ou JavaScript</span><span class="sxs-lookup"><span data-stu-id="18bfa-137">C# or JavaScript</span></span> | <span data-ttu-id="18bfa-138">Sélectionnez le langage à utiliser.</span><span class="sxs-lookup"><span data-stu-id="18bfa-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="18bfa-139">**Nommer votre fonction**</span><span class="sxs-lookup"><span data-stu-id="18bfa-139">**Name your function**</span></span> | <span data-ttu-id="18bfa-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="18bfa-140">GetUploadUrl</span></span> | <span data-ttu-id="18bfa-141">Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="18bfa-142">**Niveau d’autorisation**</span><span class="sxs-lookup"><span data-stu-id="18bfa-142">**Authorization level**</span></span> | <span data-ttu-id="18bfa-143">Anonyme</span><span class="sxs-lookup"><span data-stu-id="18bfa-143">Anonymous</span></span> | <span data-ttu-id="18bfa-144">Rend la fonction accessible publiquement.</span><span class="sxs-lookup"><span data-stu-id="18bfa-144">Allow the function to be accessed publicly.</span></span> |

    ![Entrez les paramètres pour une nouvelle fonction déclenchée par HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="18bfa-146">Cliquez sur **Créer** pour créer la fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="18bfa-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="18bfa-147">**C#**</span></span> 

    1. <span data-ttu-id="18bfa-148">Lorsque le code source de la fonction s’affiche, remplacez l’ensemble de **run.csx** par le contenu de [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="18bfa-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="18bfa-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="18bfa-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="18bfa-150">(JavaScript) Cette fonction requiert le package `azure-storage` de npm pour générer le jeton de signature d’accès partagé (SAP) nécessaire à la création de l’URL sécurisée.</span><span class="sxs-lookup"><span data-stu-id="18bfa-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="18bfa-151">Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="18bfa-152">(JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.</span><span class="sxs-lookup"><span data-stu-id="18bfa-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Ouvrir z une fenêtre de console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="18bfa-154">(JavaScript) Vérifiez que le répertoire actif est **d:\home\site\wwwroot** en exécutant la commande `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="18bfa-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="18bfa-155">(JavaScript) Exécutez la commande `npm init -y` pour créer un fichier **package.json** vide.</span><span class="sxs-lookup"><span data-stu-id="18bfa-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="18bfa-156">(JavaScript) Exécutez la commande `npm install --save azure-storage` dans la console pour installer le package et l’enregistrer dans **package.json**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="18bfa-157">L’exécution de l’opération peut prendre une ou deux minutes.</span><span class="sxs-lookup"><span data-stu-id="18bfa-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="18bfa-158">(JavaScript) Cliquez sur le nom de la fonction (**GetUploadUrl**) dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="18bfa-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js après mise à jour](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="18bfa-160">Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.</span><span class="sxs-lookup"><span data-stu-id="18bfa-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="18bfa-161">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-161">Click **Save**.</span></span> <span data-ttu-id="18bfa-162">Consultez le panneau de journaux pour vous assurer que la fonction est correctement compilée.</span><span class="sxs-lookup"><span data-stu-id="18bfa-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="18bfa-163">La fonction génère ce que l’on appelle une URL de signature d’accès partagé (SAP). Cette URL est utilisée pour charger un fichier dans le stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="18bfa-164">L’URL SAP est valide pour une courte période et permet de charger un fichier uniquement.</span><span class="sxs-lookup"><span data-stu-id="18bfa-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="18bfa-165">Consultez la documentation du stockage d’objets blob pour plus d’informations sur [l’utilisation de signatures d’accès partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="18bfa-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="18bfa-166">Ajouter une variable d’environnement pour la chaîne de connexion de stockage</span><span class="sxs-lookup"><span data-stu-id="18bfa-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="18bfa-167">Pour générer l’URL SAP, la fonction que vous venez de créer requiert une chaîne de connexion pour le compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="18bfa-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="18bfa-168">Au lieu de coder en dur la chaîne de connexion dans le corps de la fonction, vous pouvez la stocker en tant que paramètre d’application.</span><span class="sxs-lookup"><span data-stu-id="18bfa-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="18bfa-169">Les paramètres d’application sont accessibles à toutes les fonctions de l’application de fonction en tant que variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="18bfa-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="18bfa-170">Dans Cloud Shell, effectuez une requête pour obtenir la chaîne de connexion du compte de stockage et enregistrez-la dans une variable Bash nommée **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="18bfa-171">Vérifiez que la variable est correctement définie.</span><span class="sxs-lookup"><span data-stu-id="18bfa-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="18bfa-172">Créez un paramètre d’application nommé **AZURE_STORAGE_CONNECTION_STRING** en utilisant la valeur enregistrée à l’étape précédente.</span><span class="sxs-lookup"><span data-stu-id="18bfa-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="18bfa-173">Vérifiez que les résultats de la commande contiennent le nouveau paramètre d’application avec la valeur correcte.</span><span class="sxs-lookup"><span data-stu-id="18bfa-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="18bfa-174">Tester la fonction serverless</span><span class="sxs-lookup"><span data-stu-id="18bfa-174">Test the serverless function</span></span>

<span data-ttu-id="18bfa-175">Outre les fonctions de création et de modification, le Portail Azure offre un outil intégré permettant de tester les fonctions.</span><span class="sxs-lookup"><span data-stu-id="18bfa-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="18bfa-176">Pour tester la fonction serverless HTTP, cliquez sur l’onglet **Test** à droite de la fenêtre de code pour développer le panneau de test.</span><span class="sxs-lookup"><span data-stu-id="18bfa-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="18bfa-177">Sous **Méthode HTTP**, choisissez **GET**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="18bfa-178">Sous **Requête**, cliquez sur *Ajouter un paramètre*\* et ajoutez le paramètre suivant :</span><span class="sxs-lookup"><span data-stu-id="18bfa-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="18bfa-179">Nom</span><span class="sxs-lookup"><span data-stu-id="18bfa-179">name</span></span>      |  <span data-ttu-id="18bfa-180">value</span><span class="sxs-lookup"><span data-stu-id="18bfa-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="18bfa-181">filename</span><span class="sxs-lookup"><span data-stu-id="18bfa-181">filename</span></span> | <span data-ttu-id="18bfa-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="18bfa-182">image1.jpg</span></span> |

1. <span data-ttu-id="18bfa-183">Cliquez sur **Exécuter** dans le panneau de test pour envoyer une requête HTTP à la fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="18bfa-184">La fonction retourne une URL de chargement dans la sortie.</span><span class="sxs-lookup"><span data-stu-id="18bfa-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="18bfa-185">Le panneau de journaux indique que la fonction a été exécutée.</span><span class="sxs-lookup"><span data-stu-id="18bfa-185">The function execution appears in the logs panel.</span></span>

    ![Journaux montrant que la fonction a été exécutée correctement](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="18bfa-187">Configurer le partage CORS dans l’application de fonction</span><span class="sxs-lookup"><span data-stu-id="18bfa-187">Configure CORS in the function app</span></span>

<span data-ttu-id="18bfa-188">Le serveur frontal de l’application étant hébergé dans le stockage d’objets blob, son nom de domaine est différent de celui de l’application de fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="18bfa-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="18bfa-189">Pour que le code JavaScript côté client appelle correctement la fonction que vous venez de créer, l’application de fonction doit être configurée pour le partage des ressources Cross-Origin (CORS).</span><span class="sxs-lookup"><span data-stu-id="18bfa-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="18bfa-190">Dans la barre de navigation de gauche de la fenêtre d’applications de fonction, cliquez sur le nom de votre application de fonction.</span><span class="sxs-lookup"><span data-stu-id="18bfa-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="18bfa-191">Cliquez sur **Fonctionnalités de la plateforme** pour afficher la liste des fonctionnalités avancées.</span><span class="sxs-lookup"><span data-stu-id="18bfa-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="18bfa-192">Sous **API**, cliquez sur **CORS**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-192">Under **API**, click **CORS**.</span></span>

    ![Sélectionner CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="18bfa-194">Ajoutez une origine autorisée pour l’URL de l’application issue du module précédent en omettant le signe **/** à la fin (par exemple, `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="18bfa-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Paramètres CORS avec l’URL de l’application web serverless ajoutée](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="18bfa-196">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-196">Click **Save**.</span></span>

1. <span data-ttu-id="18bfa-197">C#</span><span class="sxs-lookup"><span data-stu-id="18bfa-197">C#</span></span>

   1. <span data-ttu-id="18bfa-198">(C#) Revenez à la fonction `GetUploadUrl`, puis sélectionnez l’onglet **Intégrer**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="18bfa-199">(C#) Sous Méthodes HTTP sélectionnées, sélectionnez **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="18bfa-200">Les méthodes **GET**, **POST** et **OPTIONS** doivent toutes être sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="18bfa-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="18bfa-201">Le partage CORS utilise la méthode OPTIONS, qui n’est pas sélectionnée par défaut pour les fonctions C#.</span><span class="sxs-lookup"><span data-stu-id="18bfa-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="18bfa-202">(C#) Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="18bfa-203">Toujours dans le portail, accédez à l’application de fonction, sélectionnez l’onglet **Vue d’ensemble**, puis cliquez sur **Redémarrer** pour vous assurer que les modifications relatives au partage CORS sont appliquées.</span><span class="sxs-lookup"><span data-stu-id="18bfa-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="18bfa-204">Configurer CORS dans le compte de stockage</span><span class="sxs-lookup"><span data-stu-id="18bfa-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="18bfa-205">L’application effectue aussi des appels JavaScript côté client au Stockage Blob pour charger des fichiers. Vous devez donc également configurer le compte de stockage pour le partage CORS.</span><span class="sxs-lookup"><span data-stu-id="18bfa-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="18bfa-206">Exécutez la commande suivante pour autoriser toutes les origines à charger des fichiers dans le compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="18bfa-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="18bfa-207">Modifier l’application web pour charger des images</span><span class="sxs-lookup"><span data-stu-id="18bfa-207">Modify the web app to upload images</span></span>

<span data-ttu-id="18bfa-208">L’application web récupère les paramètres à partir d’un fichier nommé **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="18bfa-209">Dans les étapes suivantes, vous allez créer le fichier à l’aide de Cloud Shell, puis définir `window.apiBaseUrl` sur l’URL de l’application de fonction et `window.blobBaseUrl` sur l’URL du point de terminaison Stockage Blob Azure.</span><span class="sxs-lookup"><span data-stu-id="18bfa-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="18bfa-210">Dans Cloud Shell, assurez-vous que le répertoire actif est **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="18bfa-211">Effectuez une requête pour obtenir l’URL de l’application de fonction, et stockez-la dans une variable Bash nommée **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="18bfa-212">Vérifiez que la variable est correctement définie.</span><span class="sxs-lookup"><span data-stu-id="18bfa-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="18bfa-213">Pour définir l’URI de base des appels d’API à votre application de fonction, créez **settings.js** et ajoutez l’URL d’application de fonction comme suit.</span><span class="sxs-lookup"><span data-stu-id="18bfa-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="18bfa-214">Vous pouvez effectuer la modification en exécutant la commande suivante ou en utilisant un éditeur de lignes de commande comme VIM.</span><span class="sxs-lookup"><span data-stu-id="18bfa-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="18bfa-215">Vérifiez que le fichier a été correctement écrit.</span><span class="sxs-lookup"><span data-stu-id="18bfa-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="18bfa-216">Effectuez une requête pour obtenir l’URL de base du Stockage Blob et stockez-la dans une variable Bash nommée **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="18bfa-217">Vérifiez que la variable est correctement définie.</span><span class="sxs-lookup"><span data-stu-id="18bfa-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="18bfa-218">Pour définir l’URI de base des appels d’API à votre application de fonction, ajoutez l’URL de stockage à **settings.js**, comme dans la ligne de code suivante.</span><span class="sxs-lookup"><span data-stu-id="18bfa-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="18bfa-219">Vous pouvez effectuer la modification en exécutant la commande suivante ou en utilisant un éditeur de lignes de commande comme VIM.</span><span class="sxs-lookup"><span data-stu-id="18bfa-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="18bfa-220">Vérifiez que le fichier a été correctement écrit et qu’il contient désormais 2 lignes.</span><span class="sxs-lookup"><span data-stu-id="18bfa-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="18bfa-221">Chargez le fichier dans le stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="18bfa-222">Tester l’application web</span><span class="sxs-lookup"><span data-stu-id="18bfa-222">Test the web application</span></span>

<span data-ttu-id="18bfa-223">À ce stade, l’application de galerie est en mesure de charger une image dans le stockage d’objets blob, mais ne peut pas encore en afficher.</span><span class="sxs-lookup"><span data-stu-id="18bfa-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="18bfa-224">Elle essaie d’appeler une fonction `GetImages` que vous créerez dans un module ultérieur et qui n’existe donc pas encore.</span><span class="sxs-lookup"><span data-stu-id="18bfa-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="18bfa-225">Cet appel échoue et la page web semble être bloquée sur un message « Analyse en cours… ». Cependant, l’image que vous sélectionnez est chargée correctement.</span><span class="sxs-lookup"><span data-stu-id="18bfa-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="18bfa-226">Vous pouvez vérifier qu’une image est chargée correctement en examinant le contenu du conteneur **images** dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="18bfa-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="18bfa-227">Accédez à l’application dans une fenêtre de navigateur.</span><span class="sxs-lookup"><span data-stu-id="18bfa-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="18bfa-228">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="18bfa-228">Select an image file and upload it.</span></span> <span data-ttu-id="18bfa-229">Le chargement s’effectue, mais l’application n’affiche pas la photo chargée. En effet, nous n’avons pas encore ajouté la possibilité d’afficher des images.</span><span class="sxs-lookup"><span data-stu-id="18bfa-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="18bfa-230">(La page web semble être bloquée sur un message d’analyse d’image en cours. Vous corrigerez cela plus tard.)</span><span class="sxs-lookup"><span data-stu-id="18bfa-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="18bfa-231">Dans Cloud Shell, vérifiez que l’image a été chargée dans le conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="18bfa-232">Avant de passer au didacticiel suivant, supprimez tous les fichiers du conteneur **images**.</span><span class="sxs-lookup"><span data-stu-id="18bfa-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="18bfa-233">Résumé</span><span class="sxs-lookup"><span data-stu-id="18bfa-233">Summary</span></span>

<span data-ttu-id="18bfa-234">Dans ce module, vous avez créé une application de fonction Azure et appris à utiliser une fonction serverless pour permettre à une application web de charger des images dans un stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="18bfa-235">Vous allez ensuite apprendre à créer des miniatures pour les images chargées en utilisant une fonction serverless déclenchée par blob.</span><span class="sxs-lookup"><span data-stu-id="18bfa-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
