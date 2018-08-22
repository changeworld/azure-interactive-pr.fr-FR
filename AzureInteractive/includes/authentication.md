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
L’authentification Azure App Service assure une prise en charge clé en main de l’authentification dans une application de fonction Azure. Elle s’intègre en toute transparence à un compte Microsoft, ainsi qu’à Facebook, Twitter, Google et Azure Active Directory. Vous allez ajouter l’authentification App Service pour protéger les API principales de votre application web.

## <a name="enable-app-service-authentication"></a>Activer l’authentification App Service

1. Ouvrez l’application de fonction dans le Portail Azure.

1. Sous **Fonctionnalités de la plateforme**, sélectionnez **Authentification/autorisation**.

    ![Sélectionner Authentification/autorisation](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Sélectionnez les valeurs suivantes :
    
    | Paramètre      |  Valeur suggérée   | Description                                        |
    | --- | --- | ---|
    | **Authentification App Service** | Il en va | Pour activer l’authentification. |
    | **Action when request is not authenticated** (Action à exécuter lorsqu’une requête n’est pas authentifiée) | Log in with Azure Active Directory (Se connecter avec Azure Active Directory) | Sélectionnez une méthode d’authentification configurée (voir plus bas). |
    | **Fournisseurs d’authentification** | Voir ci-dessous | Voir ci-dessous |
    | **Magasin de jetons** | Il en va | Pour autoriser App Service stocker et gérer des jetons. |
    | **URL de redirection externes autorisées** | L’URL de votre application, par exemple : https://firstserverlessweb.z4.web.core.windows.net/ | Les URL qu’App Service est autorisé à utiliser pour la redirection après authentification d’un utilisateur. |

1. Sélectionnez **Azure Active Directory** pour faire apparaître le volet **Paramètres Azure Active Directory**.

    1. Sélectionnez **Express** sous **Mode d’administration** et renseignez les informations suivantes.
    
        | Paramètre      |  Valeur suggérée   | Description                                        |
        | --- | --- | ---|
        | **Mode d’administration** | Express, Créer une application AD | Pour configurer automatiquement un principal de service et l’authentification Azure Active Directory. |
        | **Créer une application** | my-serverless-webapp | Entrez un nom d’application unique. |
    
    1. Cliquez sur **OK** pour enregistrer les paramètres Azure Active Directory.

    ![Paramètres Authentification/autorisation et Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Cliquez sur **Enregistrer**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Modifier l’application web pour activer l’authentification

1. Dans Cloud Shell, assurez-vous que le répertoire actif est **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Pour activer l’authentification dans votre application de fonction, ajoutez la ligne de code suivante à **settings.js**.

    `window.authEnabled = true`

    Effectuez la modification et affichez le résultat en exécutant les commandes suivantes ou en utilisant un éditeur de lignes de commande comme VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Vérifiez que la modification a été apportée au fichier.

    ```azurecli
    cat settings.js
    ```

1. Chargez le fichier dans le stockage d’objets blob.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Test de l’application

1. Ouvrez l’application dans un navigateur. Cliquez sur **Log in** (Se connecter) et connectez-vous.

1. Sélectionnez un fichier image et chargez-le.

    ![Page de connexion](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Résumé

Dans ce module, vous avez appris à ajouter l’authentification à l’application à l’aide de l’authentification Azure App Service.
