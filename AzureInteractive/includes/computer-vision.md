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
À ce stade, l’application est une galerie fonctionnelle, qui vous permet de charger et d’afficher des images. Dans ce module, vous allez apprendre à utiliser l’API Vision par ordinateur de Microsoft Cognitive Services afin de générer des légendes pour des images chargées et d’enregistrer les légendes avec les métadonnées d’image dans Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Créer un compte Vision par ordinateur

Microsoft Cognitive Services est une collection de services permettant aux développeurs de créer des applications plus intelligentes. L’API Vision par ordinateur est un service serverless qui traite les images à l’aide d’algorithmes avancés et retourne des informations sur chaque image. Elle offre un niveau gratuit fournissant jusqu’à 5 000 appels d’API par mois.

1. Vérifiez que vous êtes toujours connecté à Cloud Shell. Dans le cas contraire, sélectionnez **Enter focus mode** (Activer le mode Focus) pour ouvrir une fenêtre Cloud Shell. 

1. Créez un compte Cognitive Services de type **ComputerVision** avec un nom unique dans votre groupe de ressources. Pour le niveau gratuit, utilisez **F0** en tant que référence SKU. Si vous disposez déjà d’un compte Vision par ordinateur, vous devrez peut-être créer un compte Standard (S1), ce qui peut entraîner certains coûts.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Créer des paramètres Function App pour l’URL et la clé Vision par ordinateur

Une URL et une clé sont requises pour appeler l’API Vision par ordinateur.

1. Récupérez la clé et l’URL de l’API Vision par ordinateur, et enregistrez-les dans des variables Bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Dans l’application de fonction, créez des paramètres d’application nommés respectivement **COMP_VISION_KEY** et **COMP_VISION_URL**.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Appeler l’API Vision par ordinateur à partir de la fonction ResizeImage

Dans les étapes suivantes, vous allez modifier la fonction **ResizeImage** pour appeler l’API Vision par ordinateur afin de décrire chaque image chargée et d’enregistrer la description dans Cosmos DB.

1. Ouvrez votre application de fonction dans le Portail Azure.

1. **C#**

    1. (C#) Dans le volet de navigation de gauche, localisez la fonction **ResizeImage** et ouvrez sa fenêtre de code.

    1. (C#) Remplacez le code par le contenu de [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Ce code utilise `HttpClient` pour appeler l’API Vision par ordinateur et enregistrer son résultat dans Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Cette fonction requiert le package `axios` de npm pour adresser un appel HTTP à l’API Vision par ordinateur. Pour installer le package npm, cliquez sur le nom de l’application Function App dans le volet de navigation de gauche, puis cliquez sur **Fonctionnalités de la plateforme**.

    1. (JavaScript) Cliquez sur **Console** pour faire apparaître une fenêtre de console.

    1. (JavaScript) Exécutez la commande `npm install axios` dans la console. L’exécution de l’opération peut prendre une ou deux minutes.

    1. (JavaScript) Cliquez sur le nom de la fonction (**ResizeImage**) dans le volet de navigation de gauche pour faire apparaître la fonction, puis remplacez l’ensemble de **index.js** par le contenu de [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Cliquez sur **Journaux** sous la fenêtre de code pour développer le panneau de journaux.

1. Cliquez sur **Enregistrer**. Consultez le panneau de journaux pour vous assurer que la fonction a été correctement enregistrée et qu’il n’y a aucune erreur.


## <a name="test-the-application"></a>Test de l’application

1. Ouvrez l’application dans un navigateur. Sélectionnez un fichier image et chargez-le.

1. Après quelques secondes, la miniature de la nouvelle image doit apparaître sur la page. Pointez sur l’image pour afficher la description générée par l’API Vision par ordinateur.


## <a name="summary"></a>Résumé

Dans ce module, vous avez appris à générer automatiquement des légendes pour chaque image chargée à l’aide de l’API Vision par ordinateur de Microsoft Cognitive Services. Vous allez ensuite apprendre à ajouter l’authentification à l’application à l’aide de l’authentification Azure App Service.