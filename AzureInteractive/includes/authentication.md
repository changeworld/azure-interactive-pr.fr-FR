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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079253"
---
<span data-ttu-id="e1acc-103">L’authentification Azure App Service assure une prise en charge clé en main de l’authentification dans une application de fonction Azure.</span><span class="sxs-lookup"><span data-stu-id="e1acc-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="e1acc-104">Elle s’intègre en toute transparence à un compte Microsoft, ainsi qu’à Facebook, Twitter, Google et Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e1acc-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="e1acc-105">Vous allez ajouter l’authentification App Service pour protéger les API principales de votre application web.</span><span class="sxs-lookup"><span data-stu-id="e1acc-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="e1acc-106">Activer l’authentification App Service</span><span class="sxs-lookup"><span data-stu-id="e1acc-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="e1acc-107">Ouvrez l’application de fonction dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="e1acc-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="e1acc-108">Sous **Fonctionnalités de la plateforme**, sélectionnez **Authentification/autorisation**.</span><span class="sxs-lookup"><span data-stu-id="e1acc-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Sélectionner Authentification/autorisation](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="e1acc-110">Sélectionnez les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="e1acc-110">Select the following values:</span></span>
    
    | <span data-ttu-id="e1acc-111">Paramètre</span><span class="sxs-lookup"><span data-stu-id="e1acc-111">Setting</span></span>      |  <span data-ttu-id="e1acc-112">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="e1acc-112">Suggested value</span></span>   | <span data-ttu-id="e1acc-113">Description</span><span class="sxs-lookup"><span data-stu-id="e1acc-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="e1acc-114">**Authentification App Service**</span><span class="sxs-lookup"><span data-stu-id="e1acc-114">**App Service Authentication**</span></span> | <span data-ttu-id="e1acc-115">Il en va</span><span class="sxs-lookup"><span data-stu-id="e1acc-115">On</span></span> | <span data-ttu-id="e1acc-116">Pour activer l’authentification.</span><span class="sxs-lookup"><span data-stu-id="e1acc-116">Enable authentication.</span></span> |
    | <span data-ttu-id="e1acc-117">**Action when request is not authenticated** (Action à exécuter lorsqu’une requête n’est pas authentifiée)</span><span class="sxs-lookup"><span data-stu-id="e1acc-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="e1acc-118">Log in with Azure Active Directory (Se connecter avec Azure Active Directory)</span><span class="sxs-lookup"><span data-stu-id="e1acc-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="e1acc-119">Sélectionnez une méthode d’authentification configurée (voir plus bas).</span><span class="sxs-lookup"><span data-stu-id="e1acc-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="e1acc-120">**Fournisseurs d’authentification**</span><span class="sxs-lookup"><span data-stu-id="e1acc-120">**Authentication Providers**</span></span> | <span data-ttu-id="e1acc-121">Voir ci-dessous</span><span class="sxs-lookup"><span data-stu-id="e1acc-121">See below</span></span> | <span data-ttu-id="e1acc-122">Voir ci-dessous</span><span class="sxs-lookup"><span data-stu-id="e1acc-122">See below</span></span> |
    | <span data-ttu-id="e1acc-123">**Magasin de jetons**</span><span class="sxs-lookup"><span data-stu-id="e1acc-123">**Token store**</span></span> | <span data-ttu-id="e1acc-124">Il en va</span><span class="sxs-lookup"><span data-stu-id="e1acc-124">On</span></span> | <span data-ttu-id="e1acc-125">Pour autoriser App Service stocker et gérer des jetons.</span><span class="sxs-lookup"><span data-stu-id="e1acc-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="e1acc-126">**URL de redirection externes autorisées**</span><span class="sxs-lookup"><span data-stu-id="e1acc-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="e1acc-127">L’URL de votre application, par exemple : https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="e1acc-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="e1acc-128">Les URL qu’App Service est autorisé à utiliser pour la redirection après authentification d’un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e1acc-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="e1acc-129">Sélectionnez **Azure Active Directory** pour faire apparaître le volet **Paramètres Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="e1acc-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="e1acc-130">Sélectionnez **Express** sous **Mode d’administration** et renseignez les informations suivantes.</span><span class="sxs-lookup"><span data-stu-id="e1acc-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="e1acc-131">Paramètre</span><span class="sxs-lookup"><span data-stu-id="e1acc-131">Setting</span></span>      |  <span data-ttu-id="e1acc-132">Valeur suggérée</span><span class="sxs-lookup"><span data-stu-id="e1acc-132">Suggested value</span></span>   | <span data-ttu-id="e1acc-133">Description</span><span class="sxs-lookup"><span data-stu-id="e1acc-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="e1acc-134">**Mode d’administration**</span><span class="sxs-lookup"><span data-stu-id="e1acc-134">**Management mode**</span></span> | <span data-ttu-id="e1acc-135">Express, Créer une application AD</span><span class="sxs-lookup"><span data-stu-id="e1acc-135">Express, Create new AD app</span></span> | <span data-ttu-id="e1acc-136">Pour configurer automatiquement un principal de service et l’authentification Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e1acc-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="e1acc-137">**Créer une application**</span><span class="sxs-lookup"><span data-stu-id="e1acc-137">**Create app**</span></span> | <span data-ttu-id="e1acc-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="e1acc-138">my-serverless-webapp</span></span> | <span data-ttu-id="e1acc-139">Entrez un nom d’application unique.</span><span class="sxs-lookup"><span data-stu-id="e1acc-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="e1acc-140">Cliquez sur **OK** pour enregistrer les paramètres Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="e1acc-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Paramètres Authentification/autorisation et Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="e1acc-142">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="e1acc-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="e1acc-143">Modifier l’application web pour activer l’authentification</span><span class="sxs-lookup"><span data-stu-id="e1acc-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="e1acc-144">Dans Cloud Shell, assurez-vous que le répertoire actif est **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="e1acc-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="e1acc-145">Pour activer l’authentification dans votre application de fonction, ajoutez la ligne de code suivante à **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="e1acc-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="e1acc-146">Effectuez la modification et affichez le résultat en exécutant les commandes suivantes ou en utilisant un éditeur de lignes de commande comme VIM.</span><span class="sxs-lookup"><span data-stu-id="e1acc-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="e1acc-147">Vérifiez que la modification a été apportée au fichier.</span><span class="sxs-lookup"><span data-stu-id="e1acc-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="e1acc-148">Chargez le fichier dans le stockage d’objets blob.</span><span class="sxs-lookup"><span data-stu-id="e1acc-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="e1acc-149">Test de l’application</span><span class="sxs-lookup"><span data-stu-id="e1acc-149">Test the application</span></span>

1. <span data-ttu-id="e1acc-150">Ouvrez l’application dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="e1acc-150">Open the application in a browser.</span></span> <span data-ttu-id="e1acc-151">Cliquez sur **Log in** (Se connecter) et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="e1acc-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="e1acc-152">Sélectionnez un fichier image et chargez-le.</span><span class="sxs-lookup"><span data-stu-id="e1acc-152">Select an image file and upload it.</span></span>

    ![Page de connexion](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="e1acc-154">Résumé</span><span class="sxs-lookup"><span data-stu-id="e1acc-154">Summary</span></span>

<span data-ttu-id="e1acc-155">Dans ce module, vous avez appris à ajouter l’authentification à l’application à l’aide de l’authentification Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e1acc-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
