<span data-ttu-id="7da50-101">Stockage Blob Azure est un service économique et hautement évolutif qui peut être utilisé pour héberger des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="7da50-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="7da50-102">Dans le cadre de ce didacticiel, vous allez l’utiliser pour traiter du contenu statique (par exemple, HTML, JavaScript ou CSS) pour l’application web que vous créez.</span><span class="sxs-lookup"><span data-stu-id="7da50-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="7da50-103">Créer un compte de stockage</span><span class="sxs-lookup"><span data-stu-id="7da50-103">Create a Storage account</span></span>

<span data-ttu-id="7da50-104">Un compte de stockage est une ressource Azure qui vous permet de stocker des tables, files d’attente, fichiers, blobs (objets) et disques de machine virtuelle.</span><span class="sxs-lookup"><span data-stu-id="7da50-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="7da50-105">Connectez-vous à Cloud Shell (Bash) en sélectionnant le bouton **Enter focus mode** (Activer le mode Focus).</span><span class="sxs-lookup"><span data-stu-id="7da50-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="7da50-106">Ce bouton est situé en haut à droite ou en bas de la page, selon la largeur de la fenêtre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="7da50-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="7da50-107">Le mode Focus ancre une fenêtre Cloud Shell sur le côté droit de la fenêtre du navigateur. Vous pouvez ainsi facilement exécuter les commandes indiquées dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="7da50-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="7da50-108">Dans Azure, un groupe de ressources est un conteneur réunissant les ressources Azure associées à des fins de simplicité de gestion.</span><span class="sxs-lookup"><span data-stu-id="7da50-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="7da50-109">Créez un groupe de ressources nommé **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="7da50-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="7da50-110">Dans le cadre de ce didacticiel, le contenu statique (fichiers HTML, CSS et JavaScript) est hébergé dans le Stockage Blob.</span><span class="sxs-lookup"><span data-stu-id="7da50-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="7da50-111">Le Stockage Blob requiert un compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="7da50-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="7da50-112">Créez un compte de stockage (usage général V2) dans le groupe de ressources.</span><span class="sxs-lookup"><span data-stu-id="7da50-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="7da50-113">Remplacez `<storage account name>` par un nom unique.</span><span class="sxs-lookup"><span data-stu-id="7da50-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="7da50-114">À l’aide de la barre de recherche en haut du [Portail Azure](https://portal.azure.com), recherchez le compte de stockage que vous venez de créer et ouvrez-le.</span><span class="sxs-lookup"><span data-stu-id="7da50-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="7da50-115">Dans le volet de navigation de gauche, sélectionnez **Site web statique (préversion)** pour configurer un conteneur pour l’hébergement de site web statique.</span><span class="sxs-lookup"><span data-stu-id="7da50-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="7da50-116">Sélectionnez **Activé** pour activer le site web statique.</span><span class="sxs-lookup"><span data-stu-id="7da50-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="7da50-117">Entrez *index.html* en tant que nom du document d’index.</span><span class="sxs-lookup"><span data-stu-id="7da50-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="7da50-118">Le champ contient déjà *index.html* dans une police grise. Cependant, il s’agit d’un texte d’exemple uniquement. Vous devez quand même entrer cette valeur dans le champ.</span><span class="sxs-lookup"><span data-stu-id="7da50-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="7da50-119">Cliquez sur **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7da50-119">Click **Save**.</span></span>
    
    ![Saisie des paramètres de site web statique](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="7da50-121">Sous **Point de terminaison principal**, indiquez un emplacement d’enregistrement à partir duquel vous pourrez copier facilement le point de terminaison principal tout en travaillant sur ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="7da50-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="7da50-122">Il s’agit de l’URL de votre application web.</span><span class="sxs-lookup"><span data-stu-id="7da50-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="7da50-123">Charger l’application web</span><span class="sxs-lookup"><span data-stu-id="7da50-123">Upload the web application</span></span>

1. <span data-ttu-id="7da50-124">Les fichiers sources de l’application que vous créez dans le cadre de ce didacticiel sont situés dans un [référentiel GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="7da50-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="7da50-125">Assurez-vous que vous êtes dans votre répertoire de base dans Cloud Shell et clonez ce référentiel.</span><span class="sxs-lookup"><span data-stu-id="7da50-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="7da50-126">Le référentiel est cloné dans `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="7da50-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="7da50-127">L’application web côté client se trouve dans le dossier **www** et est générée à l’aide de l’infrastructure JavaScript Vue.js.</span><span class="sxs-lookup"><span data-stu-id="7da50-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="7da50-128">Basculez vers ce dossier et exécutez les commandes npm pour installer les dépendances de l’application et générer l’application.</span><span class="sxs-lookup"><span data-stu-id="7da50-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="7da50-129">L’exécution des dernières commandes peut prendre plusieurs minutes.</span><span class="sxs-lookup"><span data-stu-id="7da50-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="7da50-130">L’application est générée dans le dossier **dist**.</span><span class="sxs-lookup"><span data-stu-id="7da50-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="7da50-131">Choisissez **dist** comme répertoire actif, et chargez l’application dans le conteneur d’objets blob **$web**.</span><span class="sxs-lookup"><span data-stu-id="7da50-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="7da50-132">Ouvrez l’URL de stockage de point de terminaison principal des sites web statiques dans un navigateur web pour afficher l’application.</span><span class="sxs-lookup"><span data-stu-id="7da50-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Page d’accueil de votre première application web serverless](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="7da50-134">Résumé</span><span class="sxs-lookup"><span data-stu-id="7da50-134">Summary</span></span>

<span data-ttu-id="7da50-135">Dans ce module, vous avez créé un groupe de ressources nommé **first-serverless-app** contenant un compte de stockage.</span><span class="sxs-lookup"><span data-stu-id="7da50-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="7da50-136">Un conteneur d’objets blob du compte de stockage, nommé **$web**, stocke le contenu statique pour votre application web et rend le contenu disponible publiquement.</span><span class="sxs-lookup"><span data-stu-id="7da50-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="7da50-137">Vous allez ensuite apprendre à utiliser une fonction serverless pour charger des images dans le stockage d’objets blob à partir de cette application web.</span><span class="sxs-lookup"><span data-stu-id="7da50-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
