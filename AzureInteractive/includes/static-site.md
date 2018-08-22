Stockage Blob Azure est un service économique et hautement évolutif qui peut être utilisé pour héberger des fichiers statiques. Dans le cadre de ce didacticiel, vous allez l’utiliser pour traiter du contenu statique (par exemple, HTML, JavaScript ou CSS) pour l’application web que vous créez.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

Un compte de stockage est une ressource Azure qui vous permet de stocker des tables, files d’attente, fichiers, blobs (objets) et disques de machine virtuelle.

1. Connectez-vous à Cloud Shell (Bash) en sélectionnant le bouton **Enter focus mode** (Activer le mode Focus). Ce bouton est situé en haut à droite ou en bas de la page, selon la largeur de la fenêtre du navigateur. Le mode Focus ancre une fenêtre Cloud Shell sur le côté droit de la fenêtre du navigateur. Vous pouvez ainsi facilement exécuter les commandes indiquées dans le didacticiel.

1. Dans Azure, un groupe de ressources est un conteneur réunissant les ressources Azure associées à des fins de simplicité de gestion. Créez un groupe de ressources nommé **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. Dans le cadre de ce didacticiel, le contenu statique (fichiers HTML, CSS et JavaScript) est hébergé dans le Stockage Blob. Le Stockage Blob requiert un compte de stockage. Créez un compte de stockage (usage général V2) dans le groupe de ressources. Remplacez `<storage account name>` par un nom unique.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. À l’aide de la barre de recherche en haut du [Portail Azure](https://portal.azure.com), recherchez le compte de stockage que vous venez de créer et ouvrez-le.

1. Dans le volet de navigation de gauche, sélectionnez **Site web statique (préversion)** pour configurer un conteneur pour l’hébergement de site web statique.
    - Sélectionnez **Activé** pour activer le site web statique.
    - Entrez *index.html* en tant que nom du document d’index. Le champ contient déjà *index.html* dans une police grise. Cependant, il s’agit d’un texte d’exemple uniquement. Vous devez quand même entrer cette valeur dans le champ.
    - Cliquez sur **Enregistrer**.
    
    ![Saisie des paramètres de site web statique](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Sous **Point de terminaison principal**, indiquez un emplacement d’enregistrement à partir duquel vous pourrez copier facilement le point de terminaison principal tout en travaillant sur ce didacticiel. Il s’agit de l’URL de votre application web.

## <a name="upload-the-web-application"></a>Charger l’application web

1. Les fichiers sources de l’application que vous créez dans le cadre de ce didacticiel sont situés dans un [référentiel GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Assurez-vous que vous êtes dans votre répertoire de base dans Cloud Shell et clonez ce référentiel.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    Le référentiel est cloné dans `/home/<username>/functions-first-serverless-web-application`.

1. L’application web côté client se trouve dans le dossier **www** et est générée à l’aide de l’infrastructure JavaScript Vue.js. Basculez vers ce dossier et exécutez les commandes npm pour installer les dépendances de l’application et générer l’application. L’exécution des dernières commandes peut prendre plusieurs minutes.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    L’application est générée dans le dossier **dist**.

1. Choisissez **dist** comme répertoire actif, et chargez l’application dans le conteneur d’objets blob **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Ouvrez l’URL de stockage de point de terminaison principal des sites web statiques dans un navigateur web pour afficher l’application.

    ![Page d’accueil de votre première application web serverless](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Résumé

Dans ce module, vous avez créé un groupe de ressources nommé **first-serverless-app** contenant un compte de stockage. Un conteneur d’objets blob du compte de stockage, nommé **$web**, stocke le contenu statique pour votre application web et rend le contenu disponible publiquement. Vous allez ensuite apprendre à utiliser une fonction serverless pour charger des images dans le stockage d’objets blob à partir de cette application web.
