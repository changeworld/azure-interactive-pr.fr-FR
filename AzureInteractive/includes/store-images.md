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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460056"
---
Azure Cosmos DB est une base de données serverless multi-modèles distribuée par Microsoft au niveau mondial. Dans ce module, vous allez apprendre à utiliser Azure Functions pour stocker et récupérer des métadonnées d’image sous forme de documents JSON dans Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Créer un compte Cosmos DB, une base de données et une collection

Un compte Cosmos DB est une ressource Azure qui contient des bases de données Cosmos DB.

1. Vérifiez que vous êtes toujours connecté à Cloud Shell.  Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell. 

1. Créez un compte Cosmos DB avec un nom unique dans le même groupe de ressources que les autres ressources de ce didacticiel.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Une fois le compte Cosmos DB créé, créez une base de données nommée **imagesdb** dans le compte.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Une fois la base de données créée, créez une collection nommée **images** dans la base de données avec un débit de 400 unités de requête (RU).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Enregistrer un document dans Cosmos DB lorsqu’une miniature est créée

La liaison de sortie Cosmos DB vous permet de créer des documents dans une collection Cosmos DB à partir d’Azure Functions. Dans les étapes suivantes, vous allez configurer une liaison de sortie Cosmos DB dans la fonction **ResizeImage** et modifier la fonction pour retourner un document (objet) à enregistrer.

1. Ouvrez l’application de fonction dans le Portail Azure.

1. Dans le volet de navigation de gauche, développez la fonction **ResizeImage**, puis sélectionnez **Intégrer**.

1. Sous **Sorties**, cliquez sur **Nouvelle sortie**.

1. Recherchez l’élément **Azure Cosmos DB** et sélectionnez-le. Puis cliquez sur **Sélectionner**.

    ![Sélectionner une nouvelle sortie](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Renseignez les champs sous **Azure Cosmos DB output** (Sortie Azure Cosmos DB) en indiquant les valeurs suivantes.

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Nom du paramètre de document** | Sélectionnez **Use function return value** (Utiliser la valeur de retour de la fonction). | La valeur de la zone de texte est automatiquement définie sur **$return**. |
    | **Nom de la base de données** | imagesdb | Utilisez le nom de la base de données que vous avez créée. |
    | **Nom de la collection** | images | Utilisez le nom de la collection que vous avez créée. |

1. En regard de **Connexion de compte Azure Cosmos DB**, cliquez sur **Nouvelle**. Sélectionnez le compte Cosmos DB que vous avez créé précédemment.

    ![Entrer les paramètres de la liaison de sortie Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Cliquez sur **Enregistrer** pour créer la liaison de sortie Cosmos DB.

1. Cliquez sur le nom de fonction **ResizeImage** à gauche pour ouvrir la fonction.

1. **C#**

    1. (C#) Modifiez le type de retour de la fonction (**void**) en lui affectant la valeur **object**.

    1. (C#) À la fin de la fonction, ajoutez le bloc de code suivant pour retourner le document à enregistrer :
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx pour la fonction ResizeImages après modification](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Modifiez l’instruction `context.done()` dans la clause `else` pour retourner le document à enregistrer dans Cosmos DB.

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

1. Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.

1. Cliquez sur **Enregistrer**. Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Créer une fonction pour répertorier les images issues de Cosmos DB

L’application web requiert une API pour récupérer les métadonnées d’image à partir de Cosmos DB. Dans les étapes qui suivent, vous allez créer une fonction de déclencheur HTTP qui utilise une liaison d’entrée Cosmos DB pour interroger la collection de la base de données.

1. Dans votre application de fonction, pointez sur **Fonctions** sur la gauche, puis cliquez sur **+** pour créer une fonction.

1. Recherchez le modèle **HttpTrigger** et sélectionnez-le.

1. Utilisez ces valeurs pour créer une fonction qui génère une URL d’obtention d’images.

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Nommer votre fonction** | GetImages | Tapez ce nom exactement comme indiqué, de sorte que l’application détecte la fonction. |
    | **Niveau d’autorisation** | Anonyme | Rend la fonction accessible publiquement. |

1. Cliquez sur **Créer**.

1. Une fois la fonction créée, cliquez sur **Intégrer** sous le nom de la fonction, dans le volet de navigation de gauche.

1. Cliquez sur **Nouvelle entrée** et sélectionnez **Azure Cosmos DB**. 

    ![Sélectionner Nouvelle entrée](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Cliquez sur **Sélectionner**.

1. Renseignez les valeurs suivantes :

    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Nom du paramètre de document** | documents | Correspond au nom de paramètre dans la fonction. |
    | **Nom de la base de données** | imagesdb |  |
    | **Nom de la collection** | images |  |
    | **SQL query** | select * from c order by c._ts desc | Pour obtenir les documents (documents les plus récents en premier). |
    | **Connexion de compte Azure Cosmos DB** | Sélectionnez la chaîne de connexion existante |  |

1. Cliquez sur **Enregistrer** pour créer la liaison d’entrée.

1. **C#**

    1. Cliquez sur le nom de la fonction pour ouvrir la fenêtre de code, puis remplacez l’ensemble de **run.csx** par le contenu de [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Cliquez sur le nom de la fonction pour ouvrir la fenêtre de code, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.

1. Cliquez sur **Enregistrer**. Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.


## <a name="test-the-application"></a>Test de l’application

1. Ouvrez l’application dans un navigateur. Sélectionnez un fichier image et chargez-le.

1. Après quelques secondes, la miniature de la nouvelle image apparaît sur la page.

1. Dans le Portail Azure, utilisez la zone de recherche pour rechercher votre compte Cosmos DB par nom. Cliquez dessus pour l’ouvrir.

1. Cliquez sur **l’Explorateur de données** à gauche pour parcourir les collections et les documents.

1. Sous la base de données **imagesdb**, sélectionnez la collection **images**.

1. Vérifiez qu’un document a été créé pour l’image chargée.

    ![Explorateur de données affichant un document pour une image chargée](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Résumé

Dans ce module, vous avez appris à créer un compte Cosmos DB, une base de données et une collection. Vous avez également appris à utiliser les liaisons Cosmos DB pour enregistrer et récupérer des métadonnées d’image dans la collection Cosmos DB. Vous allez ensuite apprendre à générer automatiquement une légende pour chaque image chargée à l’aide de Microsoft Cognitive Services.
