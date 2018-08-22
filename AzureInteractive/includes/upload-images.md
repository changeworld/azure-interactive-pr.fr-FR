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
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079242"
---
L’application que vous générez est une galerie de photos. Elle utilise du code JavaScript côté client pour appeler des API afin de charger et d’afficher des images. Dans ce module, vous allez créer une API à l’aide d’une fonction serverless qui génère une URL limitée dans le temps pour charger une image. L’application web utilise l’URL générée pour charger une image dans le stockage d’objets blob à l’aide de [l’API REST Stockage Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Créer un conteneur de stockage d’objets blob pour les images

L’application requiert un conteneur de stockage distinct pour charger et héberger les images.

1. Vérifiez que vous êtes toujours connecté à Cloud Shell (Bash). Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell.

1.  Créez un conteneur nommé **images** dans votre compte de stockage avec un accès public à tous les blobs.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

Azure Functions est un service permettant d’exécuter des fonctions serverless. Une fonction serverless peut être déclenchée (appelée) par des événements tels qu’une requête HTTP ou lorsqu’un blob est créé dans un conteneur de stockage.

Une application de fonction Azure est un conteneur utilisé pour une ou plusieurs fonctions serverless.

1. Créez une application de fonction Azure avec un nom unique dans le groupe de ressources que vous avez créé précédemment. Nommez-la **first-serverless-app**. Les applications de fonction requièrent un compte de stockage. Dans ce didacticiel, vous utilisez le compte de stockage existant.

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a>Créer une fonction serverless déclenchée par HTTP

L’application web de galerie de photos adresse une requête HTTP à une fonction serverless pour générer une URL limitée dans le temps et ainsi charger en toute sécurité une image dans le stockage d’objets blob. La fonction est déclenchée par une requête HTTP et utilise le Kit de développement logiciel (SDK) Stockage Azure pour générer et retourner l’URL sécurisée.

1. Une fois l’application de fonction créée, recherchez-la dans le Portail Azure à l’aide de la zone de recherche, puis cliquez dessus pour l’ouvrir.

    ![Ouvrir l’application de fonction](media/functions-first-serverless-web-app/2-search-function-app.png)

1. Dans le volet de navigation de gauche de la fenêtre d’applications de fonction, pointez sur **Fonctions** et cliquez sur **+** pour commencer à créer une fonction serverless.

    ![Créer une fonction](media/functions-first-serverless-web-app/2-new-function.png)

1. Cliquez sur **Fonction personnalisée** pour afficher la liste des modèles de fonction.

1. Recherchez le modèle **HttpTrigger** et cliquez sur le langage à utiliser (C# ou JavaScript).

1. Utilisez ces valeurs pour créer une fonction qui génère une URL de chargement de blob.

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Langage** | C# ou JavaScript | Sélectionnez le langage à utiliser. |
    | **Nommer votre fonction** | GetUploadUrl | Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction. |
    | **Niveau d’autorisation** | Anonyme | Rend la fonction accessible publiquement. |

    ![Entrez les paramètres pour une nouvelle fonction déclenchée par HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Cliquez sur **Créer** pour créer la fonction.

1. **C#** 

    1. Lorsque le code source de la fonction s’affiche, remplacez l’ensemble de **run.csx** par le contenu de [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Cette fonction requiert le package `azure-storage` de npm pour générer le jeton de signature d’accès partagé (SAP) nécessaire à la création de l’URL sécurisée. Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.

    1. (JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.

        ![Ouvrir z une fenêtre de console](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Vérifiez que le répertoire actif est **d:\home\site\wwwroot** en exécutant la commande `cd d:\home\site\wwwroot`.

    1. (JavaScript) Exécutez la commande `npm init -y` pour créer un fichier **package.json** vide.

    1. (JavaScript) Exécutez la commande `npm install --save azure-storage` dans la console pour installer le package et l’enregistrer dans **package.json**. L’exécution de l’opération peut prendre une ou deux minutes.

    1. (JavaScript) Cliquez sur le nom de la fonction (**GetUploadUrl**) dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![index.js après mise à jour](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.

1. Cliquez sur **Enregistrer**. Consultez le panneau de journaux pour vous assurer que la fonction est correctement compilée.

La fonction génère ce que l’on appelle une URL de signature d’accès partagé (SAP). Cette URL est utilisée pour charger un fichier dans le stockage d’objets blob. L’URL SAP est valide pour une courte période et permet de charger un fichier uniquement. Consultez la documentation du stockage d’objets blob pour plus d’informations sur [l’utilisation de signatures d’accès partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Ajouter une variable d’environnement pour la chaîne de connexion de stockage

Pour générer l’URL SAP, la fonction que vous venez de créer requiert une chaîne de connexion pour le compte de stockage. Au lieu de coder en dur la chaîne de connexion dans le corps de la fonction, vous pouvez la stocker en tant que paramètre d’application. Les paramètres d’application sont accessibles à toutes les fonctions de l’application de fonction en tant que variables d’environnement.

1. Dans Cloud Shell, effectuez une requête pour obtenir la chaîne de connexion du compte de stockage et enregistrez-la dans une variable Bash nommée **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Vérifiez que la variable est correctement définie.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Créez un paramètre d’application nommé **AZURE_STORAGE_CONNECTION_STRING** en utilisant la valeur enregistrée à l’étape précédente.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Vérifiez que les résultats de la commande contiennent le nouveau paramètre d’application avec la valeur correcte.


## <a name="test-the-serverless-function"></a>Tester la fonction serverless

Outre les fonctions de création et de modification, le Portail Azure offre un outil intégré permettant de tester les fonctions.

1. Pour tester la fonction serverless HTTP, cliquez sur l’onglet **Test** à droite de la fenêtre de code pour développer le panneau de test.

1. Sous **Méthode HTTP**, choisissez **GET**.

1. Sous **Requête**, cliquez sur *Ajouter un paramètre** et ajoutez le paramètre suivant :

    | Nom      |  value   | 
    | --- | --- |
    | filename | image1.jpg |

1. Cliquez sur **Exécuter** dans le panneau de test pour envoyer une requête HTTP à la fonction.

1. La fonction retourne une URL de chargement dans la sortie. Le panneau de journaux indique que la fonction a été exécutée.

    ![Journaux montrant que la fonction a été exécutée correctement](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Configurer le partage CORS dans l’application de fonction

Le serveur frontal de l’application étant hébergé dans le stockage d’objets blob, son nom de domaine est différent de celui de l’application de fonction Azure. Pour que le code JavaScript côté client appelle correctement la fonction que vous venez de créer, l’application de fonction doit être configurée pour le partage des ressources Cross-Origin (CORS).

1. Dans la barre de navigation de gauche de la fenêtre d’applications de fonction, cliquez sur le nom de votre application de fonction.

1. Cliquez sur **Fonctionnalités de la plateforme** pour afficher la liste des fonctionnalités avancées.

1. Sous **API**, cliquez sur **CORS**.

    ![Sélectionner CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Ajoutez une origine autorisée pour l’URL de l’application issue du module précédent en omettant le signe **/** à la fin (par exemple, `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Paramètres CORS avec l’URL de l’application web serverless ajoutée](media/functions-first-serverless-web-app/2-add-cors.png)

1. Cliquez sur **Enregistrer**.

1. C#

   1. (C#) Revenez à la fonction `GetUploadUrl`, puis sélectionnez l’onglet **Intégrer**.

   1. (C#) Sous Méthodes HTTP sélectionnées, sélectionnez **OPTIONS**.

      Les méthodes **GET**, **POST** et **OPTIONS** doivent toutes être sélectionnées. Le partage CORS utilise la méthode OPTIONS, qui n’est pas sélectionnée par défaut pour les fonctions C#.  

   1. (C#) Cliquez sur **Enregistrer**.

1. Toujours dans le portail, accédez à l’application de fonction, sélectionnez l’onglet **Vue d’ensemble**, puis cliquez sur **Redémarrer** pour vous assurer que les modifications relatives au partage CORS sont appliquées.

## <a name="configure-cors-in-the-storage-account"></a>Configurer CORS dans le compte de stockage

L’application effectue aussi des appels JavaScript côté client au Stockage Blob pour charger des fichiers. Vous devez donc également configurer le compte de stockage pour le partage CORS.

1. Exécutez la commande suivante pour autoriser toutes les origines à charger des fichiers dans le compte de stockage.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Modifier l’application web pour charger des images

L’application web récupère les paramètres à partir d’un fichier nommé **settings.js**. Dans les étapes suivantes, vous allez créer le fichier à l’aide de Cloud Shell, puis définir `window.apiBaseUrl` sur l’URL de l’application de fonction et `window.blobBaseUrl` sur l’URL du point de terminaison Stockage Blob Azure.

1. Dans Cloud Shell, assurez-vous que le répertoire actif est **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Effectuez une requête pour obtenir l’URL de l’application de fonction, et stockez-la dans une variable Bash nommée **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Vérifiez que la variable est correctement définie.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Pour définir l’URI de base des appels d’API à votre application de fonction, créez **settings.js** et ajoutez l’URL d’application de fonction comme suit.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Vous pouvez effectuer la modification en exécutant la commande suivante ou en utilisant un éditeur de lignes de commande comme VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Vérifiez que le fichier a été correctement écrit.

    ```azurecli
    cat settings.js
    ```

1. Effectuez une requête pour obtenir l’URL de base du Stockage Blob et stockez-la dans une variable Bash nommée **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Vérifiez que la variable est correctement définie.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Pour définir l’URI de base des appels d’API à votre application de fonction, ajoutez l’URL de stockage à **settings.js**, comme dans la ligne de code suivante.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Vous pouvez effectuer la modification en exécutant la commande suivante ou en utilisant un éditeur de lignes de commande comme VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Vérifiez que le fichier a été correctement écrit et qu’il contient désormais 2 lignes.

    ```azurecli
    cat settings.js
    ```

1. Chargez le fichier dans le stockage d’objets blob.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Tester l’application web

À ce stade, l’application de galerie est en mesure de charger une image dans le stockage d’objets blob, mais ne peut pas encore en afficher. Elle essaie d’appeler une fonction `GetImages` que vous créerez dans un module ultérieur et qui n’existe donc pas encore. Cet appel échoue et la page web semble être bloquée sur un message « Analyse en cours… ». Cependant, l’image que vous sélectionnez est chargée correctement.

Vous pouvez vérifier qu’une image est chargée correctement en examinant le contenu du conteneur **images** dans le Portail Azure.

1. Accédez à l’application dans une fenêtre de navigateur. Sélectionnez un fichier image et chargez-le. Le chargement s’effectue, mais l’application n’affiche pas la photo chargée. En effet, nous n’avons pas encore ajouté la possibilité d’afficher des images. (La page web semble être bloquée sur un message d’analyse d’image en cours. Vous corrigerez cela plus tard.)

1. Dans Cloud Shell, vérifiez que l’image a été chargée dans le conteneur **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Avant de passer au didacticiel suivant, supprimez tous les fichiers du conteneur **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Résumé

Dans ce module, vous avez créé une application de fonction Azure et appris à utiliser une fonction serverless pour permettre à une application web de charger des images dans un stockage d’objets blob. Vous allez ensuite apprendre à créer des miniatures pour les images chargées en utilisant une fonction serverless déclenchée par blob.
