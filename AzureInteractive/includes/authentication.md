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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460039"
---
<span data-ttu-id="ab92f-103">L’authentification Azure App Service assure une prise en charge clé en main de l’authentification dans une application de fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="ab92f-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="ab92f-104">Elle s’intègre en toute transparence à un compte Microsoft, ainsi qu’à Facebook, Twitter, Google et Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ab92f-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="ab92f-105">Vous allez ajouter l’authentification App Service pour protéger les API principales de votre application web.</span><span class="sxs-lookup"><span data-stu-id="ab92f-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="ab92f-106">Activer l’authentification App Service</span><span class="sxs-lookup"><span data-stu-id="ab92f-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="ab92f-107">Ouvrez l’application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="ab92f-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="ab92f-108">Sous **Fonctionnalités de la plateforme**, sélectionnez **Authentification/autorisation**.</span><span class="sxs-lookup"><span data-stu-id="ab92f-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Sélectionner Authentification/autorisation](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="ab92f-110">Sélectionnez les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="ab92f-110">Select the following values:</span></span>
    
    | <span data-ttu-id="ab92f-111">Paramètre</span><span class="sxs-lookup"><span data-stu-id="ab92f-111">Setting</span></span>      |  <span data-ttu-id="ab92f-112">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="ab92f-112">Suggested value</span></span>   | <span data-ttu-id="ab92f-113">Description</span><span class="sxs-lookup"><span data-stu-id="ab92f-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="ab92f-114">**Authentification App Service**</span><span class="sxs-lookup"><span data-stu-id="ab92f-114">**App Service Authentication**</span></span> | <span data-ttu-id="ab92f-115">Il en va</span><span class="sxs-lookup"><span data-stu-id="ab92f-115">On</span></span> | <span data-ttu-id="ab92f-116">Pour activer l’authentification.</span><span class="sxs-lookup"><span data-stu-id="ab92f-116">Enable authentication.</span></span> |
    | <span data-ttu-id="ab92f-117">**Action when request is not authenticated** (Action à exécuter lorsqu’une requête n’est pas authentifiée)</span><span class="sxs-lookup"><span data-stu-id="ab92f-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="ab92f-118">Log in with Azure Active Directory (Se connecter avec Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="ab92f-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="ab92f-119">Sélectionnez une méthode d’authentification configurée (voir plus bas).</span><span class="sxs-lookup"><span data-stu-id="ab92f-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="ab92f-120">**Fournisseurs d’authentification**</span><span class="sxs-lookup"><span data-stu-id="ab92f-120">**Authentication Providers**</span></span> | <span data-ttu-id="ab92f-121">Voir ci-dessous</span><span class="sxs-lookup"><span data-stu-id="ab92f-121">See below</span></span> | <span data-ttu-id="ab92f-122">Voir ci-dessous</span><span class="sxs-lookup"><span data-stu-id="ab92f-122">See below</span></span> |
    | <span data-ttu-id="ab92f-123">**Magasin de jetons**</span><span class="sxs-lookup"><span data-stu-id="ab92f-123">**Token store**</span></span> | <span data-ttu-id="ab92f-124">Il en va</span><span class="sxs-lookup"><span data-stu-id="ab92f-124">On</span></span> | <span data-ttu-id="ab92f-125">Pour autoriser App Service stocker et gérer des jetons.</span><span class="sxs-lookup"><span data-stu-id="ab92f-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="ab92f-126">**URL de redirection externes autorisées**</span><span class="sxs-lookup"><span data-stu-id="ab92f-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="ab92f-127">L’URL de votre application, par exemple : https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="ab92f-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="ab92f-128">Les URL qu’App Service est autorisé à utiliser pour la redirection après authentification d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ab92f-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="ab92f-129">Sélectionnez **Azure Active Directory** pour faire apparaître le volet **Paramètres Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="ab92f-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="ab92f-130">Sélectionnez **Express** sous **Mode d’administration** et renseignez les informations suivantes.</span><span class="sxs-lookup"><span data-stu-id="ab92f-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="ab92f-131">Paramètre</span><span class="sxs-lookup"><span data-stu-id="ab92f-131">Setting</span></span>      |  <span data-ttu-id="ab92f-132">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="ab92f-132">Suggested value</span></span>   | <span data-ttu-id="ab92f-133">Description</span><span class="sxs-lookup"><span data-stu-id="ab92f-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="ab92f-134">**Mode d’administration**</span><span class="sxs-lookup"><span data-stu-id="ab92f-134">**Management mode**</span></span> | <span data-ttu-id="ab92f-135">Express, Créer une application AD</span><span class="sxs-lookup"><span data-stu-id="ab92f-135">Express, Create new AD app</span></span> | <span data-ttu-id="ab92f-136">Pour configurer automatiquement un principal de service et l’authentification Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ab92f-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="ab92f-137">**Créer une application**</span><span class="sxs-lookup"><span data-stu-id="ab92f-137">**Create app**</span></span> | <span data-ttu-id="ab92f-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="ab92f-138">my-serverless-webapp</span></span> | <span data-ttu-id="ab92f-139">Entrez un nom d’application unique.</span><span class="sxs-lookup"><span data-stu-id="ab92f-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="ab92f-140">Cliquez sur **OK** pour enregistrer les paramètres Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ab92f-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Paramètres Authentification/autorisation et Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="ab92f-142">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="ab92f-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="ab92f-143">Modifier l’application web pour activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="ab92f-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="ab92f-144">Dans Cloud Shell, assurez-vous que le répertoire actif est **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="ab92f-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="ab92f-145">Pour activer l’authentification dans votre application de fonction, ajoutez la ligne de code suivante à **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="ab92f-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="ab92f-146">Effectuez la modification et affichez le résultat en exécutant les commandes suivantes ou en utilisant un éditeur de lignes de commande comme VIM.</span><span class="sxs-lookup"><span data-stu-id="ab92f-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="ab92f-147">Vérifiez que la modification a été apportée au fichier.</span><span class="sxs-lookup"><span data-stu-id="ab92f-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="ab92f-148">Chargez le fichier dans le stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="ab92f-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="ab92f-149">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="ab92f-149">Test the application</span></span>

1. <span data-ttu-id="ab92f-150">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="ab92f-150">Open the application in a browser.</span></span> <span data-ttu-id="ab92f-151">Cliquez sur **Log in** (Se connecter) et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="ab92f-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="ab92f-152">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="ab92f-152">Select an image file and upload it.</span></span>

    ![Page de connexion](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="ab92f-154">Résumé</span><span class="sxs-lookup"><span data-stu-id="ab92f-154">Summary</span></span>

<span data-ttu-id="ab92f-155">Dans ce module, vous avez appris à ajouter l’authentification à l’application à l’aide de l’authentification Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="ab92f-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
