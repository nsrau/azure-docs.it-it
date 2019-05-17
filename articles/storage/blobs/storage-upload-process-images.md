---
title: Caricare i dati immagine nel cloud con Archiviazione di Azure | Microsoft Docs
description: Usare l'archiviazione BLOB di Azure con un'app Web per archiviare i dati dell'app
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: mvc
ms.openlocfilehash: 26b92db330c882aaf258b6e24560cbf2f7930a5f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237141"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Esercitazione: Caricare i dati immagine nel cloud con Archiviazione di Azure

Questa è la prima di una serie di esercitazioni. In questa esercitazione si imparerà a distribuire un'app Web che usa la libreria client di archiviazione di Azure per caricare immagini in un account di archiviazione. Al termine si avrà un'app Web che archivia e visualizza le immagini da Archiviazione di Azure.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione
> * Creare un contenitore e impostare le autorizzazioni
> * Ottenere una chiave di accesso
> * Distribuire un'app Web in Azure
> * Configurare le impostazioni applicazione
> * Interagire con l'app Web

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure. Prima di iniziare, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Per installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse 

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.  

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia 
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

L'esempio carica le immagini in un contenitore BLOB in un account di archiviazione di Azure. Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti. Creare un account di archiviazione nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> Nella parte 2 dell'esercitazione si usa il servizio Griglia di eventi di Azure con l'archiviazione BLOB. Assicurarsi quindi di creare l'account di archiviazione in un'area di Azure che supporta Griglia di eventi. Per un elenco delle aree supportate, vedere [Prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Nel comando seguente sostituire il segnaposto `<blob_storage_account>` con il nome globalmente univoco dell'account di archiviazione BLOB.  

```azurecli-interactive
blobStorageAccount=<blob_storage_account>

az storage account create --name $blobStorageAccount \
--location southeastasia --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
```

## <a name="create-blob-storage-containers"></a>Creare contenitori di archiviazione BLOB

L'app usa due contenitori nell'account di archiviazione BLOB. I contenitori sono simili alle cartelle e vengono usati per archiviare BLOB. Il contenitore *images* è la posizione in cui l'applicazione carica le immagini ad alta risoluzione. In una parte successiva della serie un'app per le funzioni di Azure carica le anteprime delle immagini ridimensionate nel contenitore *thumbnails*.

Usare il comando [az storage account keys list](/cli/azure/storage/account/keys) per ottenere le chiavi dell'account di archiviazione. Usare quindi questa chiave per creare due contenitori con il comando [az storage container create](/cli/azure/storage/container).  

L'accesso pubblico al contenitore *images* è impostato su `off`. L'accesso pubblico al contenitore *thumbnails* è impostato su `container`. L'impostazione di accesso pubblico `container` consente agli utenti che visitano la pagina Web di visualizzare le anteprime.

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

Prendere nota del nome e della chiave dell'account di archiviazione BLOB. L'app di esempio usa queste impostazioni per connettersi all'account di archiviazione per caricare le immagini. 

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Un [piano di servizio app](../../app-service/overview-hosting-plans.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app.

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan).

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Gratuito**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Creare un'app Web

L'app Web offre uno spazio di hosting per il codice dell'app di esempio che viene distribuito dal repository di esempi GitHub. Creare un'[app Web](../../app-service/overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp).  

Nel comando seguente sostituire `<web_app>` con un nome univoco. I caratteri validi sono `a-z`, `0-9` e `-`. Se `<web_app>` è non univoco, verrà visualizzato il messaggio di errore: _Il sito Web con il nome `<web_app>` specificato esiste già._ L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.  

```azurecli-interactive
webapp=<web_app>

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuire l'app di esempio dal repository GitHub

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Il progetto di esempio contiene un'app [MVC ASP.NET](https://www.asp.net/mvc). L'app accetta un'immagine, la salva in un account di archiviazione e visualizza le immagini da un contenitore di anteprime. L'app Web usa gli spazi dei nomi [Microsoft.WindowsAzure.Storage](/dotnet/api/overview/azure/storage), [Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.azure.storage.blob) e Microsoft.WindowsAzure.Storage.Auth della libreria client di archiviazione di Azure per interagire con Archiviazione di Azure.

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source). 

```azurecli-interactive
az webapp deployment source config --name $webapp \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>Configurare le impostazioni dell'app Web

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

L'applicazione Web di esempio usa la [libreria client di archiviazione di Azure](/dotnet/api/overview/azure/storage?view=azure-dotnet) per chiedere i token di accesso, che vengono usati per caricare le immagini. Le credenziali dell'account di archiviazione usate da Azure Storage SDK sono definite nelle impostazioni dell'app Web. Aggiungere le impostazioni dell'app all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=$blobStorageAccountKey  
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

L'applicazione Web di esempio usa la [libreria client di archiviazione di Azure](https://docs.microsoft.com/javascript/api/azure-storage) per chiedere i token di accesso, che vengono usati per caricare le immagini. Le credenziali dell'account di archiviazione usate da Azure Storage SDK sono definite nelle impostazioni dell'app Web. Aggiungere le impostazioni dell'app all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountName=$blobStorageAccount \
AzureStorageConfig__AccountKey=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

L'applicazione Web di esempio usa la [libreria client di archiviazione di Azure](https://github.com/Azure/azure-storage-js) per chiedere i token di accesso, che vengono usati per caricare le immagini. Le credenziali dell'account di archiviazione usate da Azure Storage SDK sono definite nelle impostazioni dell'app Web. Aggiungere le impostazioni dell'app all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
--settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Dopo aver distribuito e configurato l'app Web, è possibile testare la funzionalità di caricamento immagini nell'app.

## <a name="upload-an-image"></a>Caricare un'immagine

Per testare l'applicazione Web, passare all'URL dell'app pubblicata. L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

Selezionare l'area **Upload photos** (Carica foto) per selezionare e caricare un file oppure trascinare un file nell'area. Se è stata caricata correttamente, l'immagine scompare. La sezione **Generated Thumbnails** (Anteprime generate) rimarrà vuota finché non verrà testata più avanti in questo argomento.

![App ImageResizer](media/storage-upload-process-images/figure1.png)

Nel codice di esempio, l'attività `UploadFiletoStorage` nel file *Storagehelper.cs* viene usata per caricare le immagini nel contenitore *images* all'interno dell'account di archiviazione usando il metodo [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet). L'esempio di codice seguente contiene l'attività `UploadFiletoStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

Nell'attività precedente vengono usate le classi e i metodi seguenti:

|Classe  |Metodo  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)       |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)    | [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference)        |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)     | [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync)        |

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Selezionare **Choose File** (Scegli file) per selezionare un file, quindi fare clic su **Upload Image** (Carica l'immagine). La sezione **Generated Thumbnails** (Anteprime generate) rimarrà vuota finché non verrà testata più avanti in questo argomento. 

![App per il caricamento di immagini](media/storage-upload-process-images/upload-app-nodejs.png)

Nel codice di esempio la route `post` è responsabile del caricamento dell'immagine in un contenitore BLOB. La route usa i moduli per consentire l'elaborazione del caricamento:

- [multer](https://github.com/expressjs/multer) implementa la strategia di caricamento per il gestore di route.
- [into-stream](https://github.com/sindresorhus/into-stream) converte il buffer in un flusso come richiesto da [createBlockBlobFromStream].(https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html)

Quando il file viene inviato alla route, i contenuti del file restano in memoria finché il file non viene caricato nel contenitore BLOB.

> [!IMPORTANT]
> Il caricamento di file di grandi dimensioni in memoria può influire negativamente sulle prestazioni dell'app Web. Se si prevede che gli utenti pubblicheranno file di grandi dimensioni, prendere in considerazione la possibilità di inserire temporaneamente i file nel file system del server Web e quindi di pianificare i caricamenti nell'archivio BLOB. Una volta che i file sono nell'archivio BLOB, è possibile rimuoverli dal file system del server.

```javascript
const
      express = require('express')
    , router = express.Router()

    , multer = require('multer')
    , inMemoryStorage = multer.memoryStorage()
    , uploadStrategy = multer({ storage: inMemoryStorage }).single('image')

    , azureStorage = require('azure-storage')
    , blobService = azureStorage.createBlobService()

    , getStream = require('into-stream')
    , containerName = 'images'
;

const handleError = (err, res) => {
    res.status(500);
    res.render('error', { error: err });
};

const getBlobName = originalName => {
    const identifier = Math.random().toString().replace(/0\./, ''); // remove "0." from start of string
    return `${originalName}-${identifier}`;
};

router.post('/', uploadStrategy, (req, res) => {

    const
          blobName = getBlobName(req.file.originalname)
        , stream = getStream(req.file.buffer)
        , streamLength = req.file.buffer.length
    ;

    blobService.createBlockBlobFromStream(containerName, blobName, stream, streamLength, err => {

        if(err) {
            handleError(err);
            return;
        }

        res.render('success', { 
            message: 'File uploaded to Azure Blob storage.' 
        });
    });
});
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Selezionare **Choose File** (Scegli file) per selezionare un file, quindi fare clic su **Upload Image** (Carica l'immagine). La sezione **Generated Thumbnails** (Anteprime generate) rimarrà vuota finché non verrà testata più avanti in questo argomento. 

![App per il caricamento di immagini](media/storage-upload-process-images/upload-app-nodejs.png)

Nel codice di esempio la route `post` è responsabile del caricamento dell'immagine in un contenitore BLOB. La route usa i moduli per consentire l'elaborazione del caricamento:

- [multer](https://github.com/expressjs/multer) implementa la strategia di caricamento per il gestore di route.
- [into-stream](https://github.com/sindresorhus/into-stream) converte il buffer in uno stream come richiesto da [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html).

Quando il file viene inviato alla route, i contenuti del file restano in memoria finché il file non viene caricato nel contenitore BLOB.

> [!IMPORTANT]
> Il caricamento di file di grandi dimensioni in memoria può influire negativamente sulle prestazioni dell'app Web. Se si prevede che gli utenti pubblicheranno file di grandi dimensioni, prendere in considerazione la possibilità di inserire temporaneamente i file nel file system del server Web e quindi di pianificare i caricamenti nell'archivio BLOB. Una volta che i file sono nell'archivio BLOB, è possibile rimuoverli dal file system del server.

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verificare che l'immagine venga visualizzata nell'account di archiviazione

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. In **Servizio BLOB** selezionare**BLOB**, quindi selezionare il contenitore **images**.

Verificare che l'immagine venga visualizzata nel contenitore.

![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test della visualizzazione dell'anteprima

Per testare la visualizzazione dell'anteprima, caricare un'immagine nel contenitore**thumbnails** per controllare se l'app è in grado di leggere il contenitore **thumbnails**.

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. In **Servizio BLOB** selezionare**BLOB**, quindi selezionare il contenitore **thumbnails**. Selezionare **Carica** per aprire il riquadro **Carica BLOB**.

Scegliere un file con la selezione file e quindi scegliere **Carica**.

Tornare all'app per verificare che l'immagine caricata nel contenitore **thumbnails** sia visibile.

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)
![Visualizzazione del contenitore delle immagini](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Nella seconda parte della serie la creazione di immagini di anteprima è automatizzata, quindi questa immagine non è necessaria. Nel contenitore **thumbnails** nel portale di Azure selezionare l'immagine caricata e quindi selezionare **Elimina** per eliminare l'immagine. 

È possibile abilitare la rete CDN per memorizzare nella cache i contenuti dell'account di archiviazione di Azure. Per altre informazioni su come abilitare la rete CDN con il proprio account di archiviazione di Azure, vedere [Integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie si è appreso come configurare un'app Web in modo che interagisca con la risorsa di archiviazione.

Passare ora alla seconda parte della serie per imparare a usare il servizio Griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine.

> [!div class="nextstepaction"]
> [Usare la griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine caricata](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
