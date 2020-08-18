---
title: Caricare i dati immagine nel cloud con Archiviazione di Azure | Microsoft Docs
description: Usare Archiviazione BLOB di Azure con le app Web per archiviare i dati delle app in un account di archiviazione. Questa esercitazione consente di creare un'app Web che archivia le immagini in Archiviazione di Azure e le visualizza.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-javascript
ms.openlocfilehash: 3ac1855c0cc72a3c4afc20598d023f0adce2bd76
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034142"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Esercitazione: Caricare i dati immagine nel cloud con Archiviazione di Azure

Questa è la prima di una serie di esercitazioni. Questa esercitazione illustra come distribuire un'app Web. L'app Web usa la libreria client di archiviazione BLOB di Azure per caricare immagini in un account di archiviazione. Al termine si avrà un'app Web che archivia e visualizza le immagini da Archiviazione di Azure.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![App di ridimensionamento immagini in .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![App di ridimensionamento immagini in JavaScript](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

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

Per installare e usare l'interfaccia della riga di comando di Azure in locale, è necessario eseguire la versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.  

Nell'esempio seguente viene creato il gruppo di risorse denominato `myResourceGroup`.

```bash
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

L'esempio carica le immagini in un contenitore BLOB in un account di archiviazione di Azure. Un account di archiviazione offre uno spazio dei nomi univoco per archiviare gli oggetti dati di Archiviazione di Azure e accedere a tali oggetti. Creare un account di archiviazione nel gruppo di risorse creato usando il comando [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> Nella parte 2 dell'esercitazione si usa il servizio Griglia di eventi di Azure con l'archiviazione BLOB. Assicurarsi quindi di creare l'account di archiviazione in un'area di Azure che supporta Griglia di eventi. Per un elenco delle aree supportate, vedere [Prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

Nel comando seguente sostituire il segnaposto `<blob_storage_account>` con il nome globalmente univoco dell'account di archiviazione BLOB.

```bash
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Creare contenitori di archiviazione BLOB

L'app usa due contenitori nell'account di archiviazione BLOB. I contenitori sono simili alle cartelle e vengono usati per archiviare BLOB. Il contenitore *images* è la posizione in cui l'applicazione carica le immagini ad alta risoluzione. In una parte successiva della serie un'app per le funzioni di Azure carica le anteprime delle immagini ridimensionate nel contenitore *thumbnails*.

Usare il comando [az storage account keys list](/cli/azure/storage/account/keys) per ottenere le chiavi dell'account di archiviazione. Usare quindi questa chiave per creare due contenitori con il comando [az storage container create](/cli/azure/storage/container).

L'accesso pubblico al contenitore *images* è impostato su `off`. L'accesso pubblico al contenitore *thumbnails* è impostato su `container`. L'impostazione di accesso pubblico `container` consente agli utenti che visitano la pagina Web di visualizzare le anteprime.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Prendere nota del nome e della chiave dell'account di archiviazione BLOB. L'app di esempio usa queste impostazioni per connettersi all'account di archiviazione per caricare le immagini. 

## <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Un [piano di servizio app](../../app-service/overview-hosting-plans.md) specifica la località, le dimensioni e le funzionalità della server farm Web che ospita l'app.

Creare un piano di servizio app con il comando [az appservice plan create](/cli/azure/appservice/plan).

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Gratuito**:

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Creare un'app Web

L'app Web offre uno spazio di hosting per il codice dell'app di esempio che viene distribuito dal repository di esempi GitHub. Creare un'[app Web](../../app-service/overview.md) nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp).  

Nel comando seguente sostituire `<web_app>` con un nome univoco. I caratteri validi sono `a-z`, `0-9` e `-`. Se il valore di `<web_app>` non è univoco, verrà visualizzato il messaggio di errore: *Il sito Web con il nome `<web_app>` specificato esiste già.* L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.  

```bash
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Distribuire l'app di esempio dal repository GitHub

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/storage-blob-upload-from-webapp). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Il progetto di esempio contiene un'app [MVC ASP.NET](https://www.asp.net/mvc). L'app accetta un'immagine, la salva in un account di archiviazione e visualizza le immagini da un contenitore di anteprime. L'app Web usa gli spazi dei nomi [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) e [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) per interagire con il servizio di archiviazione di Azure.

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Il servizio app offre diversi modi per distribuire contenuto in un'applicazione Web. In questa esercitazione si distribuisce l'app Web da un [repository pubblico di esempio GitHub](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs). Configurare la distribuzione GitHub nell'applicazione Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```bash
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Configurare le impostazioni dell'app Web

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

L'app Web di esempio usa le [API di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage) per caricare le immagini. Le credenziali dell'account di archiviazione sono definite nelle impostazioni dell'app Web. Aggiungere le impostazioni dell'app all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

L'app Web di esempio usa la [libreria client di Archiviazione di Azure per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) per caricare le immagini. Le credenziali dell'account di archiviazione sono definite nelle impostazioni dell'app Web. Aggiungere le impostazioni dell'app all'app distribuita con il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```bash
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Dopo aver distribuito e configurato l'app Web, è possibile testare la funzionalità di caricamento immagini nell'app.

## <a name="upload-an-image"></a>Caricare un'immagine

Per testare l'applicazione Web, passare all'URL dell'app pubblicata. L'URL predefinito dell'app Web è `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Selezionare l'area **Upload photos** (Carica foto) per specificare e caricare un file oppure trascinare un file nell'area. Se è stata caricata correttamente, l'immagine scompare. La sezione **Generated Thumbnails** (Anteprime generate) rimarrà vuota finché non verrà testata più avanti in questa esercitazione.

![Caricamento foto in .NET](media/storage-upload-process-images/figure1.png)

Nel codice di esempio l'attività `UploadFileToStorage` nel file *Storagehelper.cs* viene usata per caricare le immagini nel contenitore *images* all'interno dell'account di archiviazione usando il metodo [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync). L'esempio di codice seguente contiene l'attività `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Nell'attività precedente vengono usate le classi e i metodi seguenti:

| Classe | Metodo |
|-------|--------|
| [Uri](/dotnet/api/system.uri) | [Costruttore URI](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [Costruttore StorageSharedKeyCredential(String, String)](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Selezionare **Choose File** (Scegli file) per selezionare un file, quindi fare clic su **Upload Image** (Carica l'immagine). La sezione **Generated Thumbnails** (Anteprime generate) rimarrà vuota finché non verrà testata più avanti in questa esercitazione.

![Caricare foto in Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

Nel codice di esempio la route `post` è responsabile del caricamento dell'immagine in un contenitore BLOB. La route usa i moduli per consentire l'elaborazione del caricamento:

- [multer](https://github.com/expressjs/multer) implementa la strategia di caricamento per il gestore di route.
- [into-stream](https://github.com/sindresorhus/into-stream) converte il buffer in un flusso, come richiesto da [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

Quando il file viene inviato alla route, i contenuti del file restano in memoria finché il file non viene caricato nel contenitore BLOB.

> [!IMPORTANT]
> Il caricamento di file di grandi dimensioni in memoria può influire negativamente sulle prestazioni dell'app Web. Se si prevede che gli utenti pubblicheranno file di grandi dimensioni, prendere in considerazione la possibilità di inserire temporaneamente i file nel file system del server Web e quindi di pianificare i caricamenti nell'archivio BLOB. Una volta che i file sono nell'archivio BLOB, è possibile rimuoverli dal file system del server.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Verificare che l'immagine venga visualizzata nell'account di archiviazione

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. Selezionare **Contenitori**, quindi selezionare il contenitore **images**.

Verificare che l'immagine venga visualizzata nel contenitore.

![Elenco di contenitori di immagini nel portale di Azure](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Test della visualizzazione dell'anteprima

Per testare la visualizzazione dell'anteprima, caricare un'immagine nel contenitore**thumbnails** per controllare se l'app è in grado di leggere il contenitore **thumbnails**.

Accedere al [portale di Azure](https://portal.azure.com). Nel menu a sinistra selezionare **Account di archiviazione** e quindi selezionare il nome dell'account di archiviazione. Selezionare **Contenitori**, quindi selezionare il contenitore **thumbnails**. Selezionare **Carica** per aprire il riquadro **Carica BLOB**.

Scegliere un file con la selezione file e quindi scegliere **Carica**.

Tornare all'app per verificare che l'immagine caricata nel contenitore **thumbnails** sia visibile.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![App di ridimensionamento immagini .NET con la nuova immagine visualizzata](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![App di ridimensionamento immagini Node.js con la nuova immagine visualizzata](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

Nella seconda parte della serie la creazione di immagini di anteprima è automatizzata, quindi questa immagine non è necessaria. Nel contenitore **thumbnails** selezionare l'immagine caricata e quindi selezionare **Elimina** per rimuoverla.

È possibile abilitare la rete per la distribuzione di contenuti (rete CDN) per memorizzare nella cache i contenuti dall'account di archiviazione di Azure. Per altre informazioni, vedere [Integrare un account di archiviazione di Azure con la rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie si è appreso come configurare un'app Web in modo che interagisca con la risorsa di archiviazione.

Passare ora alla seconda parte della serie per imparare a usare il servizio Griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine.

> [!div class="nextstepaction"]
> [Usare la griglia di eventi per attivare una funzione di Azure per ridimensionare un'immagine caricata](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
