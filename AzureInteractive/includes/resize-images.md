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
Dans le module précédent, vous avez vu comment une fonction serverless peut assurer le chargement sécurisé des images vers un stockage d’objets blob à partir d’une application web. Dans ce module, vous allez créer une autre fonction serverless pour surveiller les images chargées et créer des miniatures à partir de ces images.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Créer un conteneur de stockage d’objets blob pour les miniatures

Les images en taille réelle sont stockées dans un conteneur nommé **images**. Vous avez besoin d’un autre conteneur pour stocker les miniatures de ces images.

1. Vérifiez que vous êtes toujours connecté à Cloud Shell (Bash).  Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell. 

1. Créez un conteneur nommé **thumbnails** dans votre compte de stockage avec un accès public à tous les blobs.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Créer une fonction serverless déclenchée par blob

Un déclencheur définit la façon dont une fonction est appelée. La fonction que vous allez créer utilise un déclencheur de blob : la fonction est appelée automatiquement lors du chargement d’un blob (fichier image) dans le conteneur **images**. Une fonction doit avoir un déclencheur. Les déclencheurs sont associés à des données, généralement la charge utile qui a déclenché la fonction.

Les liaisons définissent la façon dont une fonction lit ou écrit des données dans Azure ou des services tiers. Cette fonction crée une version miniature de l’image qui la déclenche et enregistre la miniature dans un conteneur *thumbnails*.

1. Ouvrez votre application de fonction dans le Portail Azure.

1. Dans le volet de navigation de gauche de la fenêtre d’applications de fonction, pointez sur **Fonctions** et cliquez sur **+** pour commencer à créer une fonction serverless. Si une page de démarrage rapide s’affiche, cliquez sur **Fonction personnalisée** pour afficher la liste des modèles de fonction.

1. Recherchez le modèle **BlobTrigger** et sélectionnez-le.

1. Utilisez ces valeurs pour créer une fonction qui crée des miniatures à mesure que les images sont chargées.

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Langage** | C# ou JavaScript | Sélectionnez votre langage préféré. |
    | **Nommer votre fonction** | ResizeImage | Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction. |
    | **Chemin d’accès** | images/{nom} | Exécute la fonction lorsqu’un fichier apparaît dans le conteneur **images**. |
    | **Informations sur le compte de stockage** | AZURE_STORAGE_CONNECTION_STRING | Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion. |

    ![Saisie des paramètres pour la nouvelle fonction](media/functions-first-serverless-web-app/3-new-function.png)

1. Cliquez sur **Créer** pour créer la fonction.

1. Une fois la fonction créée, cliquez sur **Intégrer** pour afficher son déclencheur, son entrée et ses liaisons de sortie.

1. Cliquez sur **Nouvelle sortie** pour créer une liaison de sortie.

    ![Sélectionner Nouvelle sortie dans l’onglet Intégrer](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Sélectionnez **Stockage Blob Azure**, puis cliquez sur **Sélectionner**. Vous devrez peut-être faire défiler l’écran pour voir le bouton **Sélectionner**.

    ![Sélectionner Stockage Blob Azure](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Saisissez les valeurs suivantes.

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Nom du paramètre de blob** | thumbnail | La fonction utilise le paramètre portant ce nom pour écrire la miniature. |
    | **Use function return value** (Utiliser la valeur de retour de la fonction) | Non  |  |
    | **Chemin d’accès** | thumbnails/{nom} | Les miniatures sont écrites dans un conteneur nommé **thumbnails**. |
    | **Connexion au compte de stockage** | AZURE_STORAGE_CONNECTION_STRING | Utilisez la variable d’environnement créée précédemment avec la chaîne de connexion. |

    ![Saisie des paramètres de la liaison de sortie de blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Cliquez sur **Éditeur avancé** dans l’angle supérieur droit de la fenêtre pour faire apparaître le document JSON représentant les liaisons.

    1. (JavaScript) Dans la liaison `blobTrigger`, ajoutez une propriété nommée `dataType` avec la valeur `binary`. Ceci configure la liaison pour transmettre le contenu du blob à la fonction en tant que données binaires.

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

1. Cliquez sur **Enregistrer** pour créer la liaison.

1. **C#**

    1. (C#) Sélectionnez le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour ouvrir le code source de la fonction.

    1. (C#) La fonction requiert un package NuGet appelé **ImageResizer** pour générer les miniatures. Les packages NuGet sont ajoutés aux fonctions C# à l’aide d’un fichier **project.json**. Pour créer le fichier, cliquez sur **Afficher les fichiers** sur la droite pour afficher les fichiers qui composent la fonction.
    
    1. (C#) Cliquez sur **Ajouter** pour ajouter un nouveau fichier nommé **project.json**.
    
    1. (C#) Copiez le contenu du fichier [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) dans le nouveau fichier créé. Enregistrez le fichier . Les packages sont restaurés automatiquement lorsque le fichier est mis à jour.
    
        ![Fichier project.json avec ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Sélectionnez **run.csx** sous **Afficher les fichiers** et remplacez son contenu par celui de [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Cette fonction requiert le package `jimp` de npm pour redimensionner la photo. Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.

    1. (JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.

    1. (JavaScript) Exécutez la commande `npm install jimp` dans la console. L’exécution de l’opération peut prendre une ou deux minutes.

    1. (JavaScript) Cliquez sur le nom de fonction **ResizeImage** dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/ResizeImage / index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.

1. Cliquez sur **Enregistrer**. Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.


## <a name="test-the-serverless-function"></a>Tester la fonction serverless

1. Ouvrez l’application dans un navigateur. Sélectionnez un fichier image et chargez-le. Le chargement s’effectue, mais l’application n’affiche pas la photo chargée. En effet, nous n’avons pas encore ajouté la possibilité d’afficher des images.

1. Dans Cloud Shell, vérifiez que l’image a été chargée dans le conteneur **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Vérifiez que la miniature a été créée dans un conteneur nommé **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Obtenez l’URL de la miniature.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Ouvrez l’URL dans un navigateur et vérifiez que la miniature a été créée correctement.

1. Avant de passer au didacticiel suivant, supprimez tous les fichiers des conteneurs **images** et **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Résumé

Dans ce module, vous avez créé une fonction serverless pour créer une miniature chaque fois qu’une image est chargée dans un conteneur de stockage d’objets blob. Vous allez ensuite apprendre à utiliser Azure Cosmos DB pour stocker et répertorier les métadonnées d’image.
