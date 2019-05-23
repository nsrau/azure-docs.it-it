---
title: Proteggere l'accesso ai dati di un'applicazione nel cloud con Archiviazione di Azure | Microsoft Docs
description: Usare i token di firma di accesso condiviso, la crittografia e HTTPS per proteggere i dati dell'applicazione nel cloud.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.reviewer: cbrooks
ms.custom: mvc
ms.openlocfilehash: 8e56b02b84c0324f723ead1bbf156c847edbbeb5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787979"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Proteggere l'accesso ai dati di un'applicazione nel cloud

Questa è la terza di una serie di esercitazioni. Si apprende come proteggere l'accesso all'account di archiviazione. 

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Usare i token di firma di accesso condiviso per accedere a immagini di anteprima
> * Attivare la crittografia lato server
> * Abilitare il trasporto solo HTTPS

[Archiviazione BLOB di Azure](../common/storage-introduction.md#blob-storage) è un servizio affidabile per l'archiviazione di file per le applicazioni. Questa esercitazione estende [l'argomento precedente][previous-tutorial] e spiega come proteggere l'accesso all'account di archiviazione da un'applicazione Web. Dopo il completamento le immagini vengono crittografate e l'app Web usa i token di firma di accesso condiviso sicuri per accedere alle immagini di anteprima.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario aver completato l'esercitazione di archiviazione precedente: [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi][previous-tutorial]. 

## <a name="set-container-public-access"></a>Impostare l'accesso pubblico al contenitore

In questa parte della serie di esercitazioni si usano i token di firma di accesso condiviso per l'accesso alle anteprime. In questo passaggio si imposta l'accesso pubblico del contenitore _thumbnails_ su `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurare i token di firma di accesso condiviso per le anteprime

Nella prima parte di questa serie di esercitazioni l'applicazione Web visualizzava immagini di un contenitore pubblico. In questa parte della serie vengono usati token di [firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) per recuperare le immagini di anteprima. I token di firma di accesso condiviso consentono di garantire l'accesso limitato a un contenitore o un BLOB in base all'IP, al protocollo, all'intervallo di tempo o ai diritti concessi.

In questo esempio il repository di codice sorgente usa il ramo `sasTokens`, che dispone di un esempio di codice aggiornato. Eliminare la distribuzione di GitHub esistente con [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Quindi configurare la distribuzione GitHub all'app Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).  

Nel comando seguente `<web-app>` è il nome dell'app Web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Il ramo `sasTokens` del repository aggiorna il file `StorageHelper.cs`. Sostituisce l'attività `GetThumbNailUrls` con l'esempio di codice riportato di seguito. L'attività aggiornata recupera gli URL delle anteprime impostando [SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy) per specificare l'ora di inizio, l'ora di scadenza e le autorizzazioni per il token di firma di accesso condiviso. Dopo la distribuzione l'app Web recupera le anteprime con un URL tramite un token di firma di accesso condiviso. L'attività aggiornata viene visualizzata nell'esempio seguente:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

Nell'attività precedente vengono usate le classi, le proprietà e i metodi seguenti:

|Classe  |Properties| Metodi  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.azure.cosmos.table.storagecredentials)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)        |
|[CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient)     | |[GetContainerReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getcontainerreference)         |
|[CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer)     | |[SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)       |
|[BlobContinuationToken](/dotnet/api/microsoft.azure.storage.blob.blobcontinuationtoken)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment)    | [Risultati](/dotnet/api/microsoft.azure.storage.blob.blobresultsegment.results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy)     | [SharedAccessStartTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime)<br>[Autorizzazioni](/dotnet/api/microsoft.azure.storage.blob.sharedaccessblobpolicy.permissions) |        |

## <a name="server-side-encryption"></a>Modello di crittografia lato server

La soluzione [Crittografia del servizio di archiviazione (SSE)](../common/storage-service-encryption.md) di Azure consente di proteggere e salvaguardare i dati. La soluzione SSE garantisce la crittografia dei dati inattivi e gestisce la crittografia, la decrittografia e la gestione delle chiavi. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

La crittografia del servizio di archiviazione crittografa automaticamente i dati in tutti i livelli di prestazioni (Standard e Premium), tutti i modelli di distribuzione (distribuzione classica e Azure Resource Manager) e tutti i servizi di Archiviazione di Azure (BLOB, code, tabelle e file). 

## <a name="enable-https-only"></a>Abilitare solo HTTPS

Per garantire che le richieste di dati da e verso un account di archiviazione siano protette, è possibile consentire solo le richieste HTTPS. Aggiornare il protocollo richiesto per l'account di archiviazione tramite il comando [az storage account update](/cli/azure/storage/account).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Testare la connessione usando `curl` e il protocollo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Ora che il trasferimento sicuro è obbligatorio, viene visualizzato il messaggio seguente:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Passaggi successivi

Nella terza parte della serie si è appreso come garantire l'accesso all'account di archiviazione, ad esempio come:

> [!div class="checklist"]
> * Usare i token di firma di accesso condiviso per accedere a immagini di anteprima
> * Attivare la crittografia lato server
> * Abilitare il trasporto solo HTTPS

Passare alla parte 4 della serie per apprendere come monitorare e risolvere i problemi di un'applicazione di archiviazione cloud.

> [!div class="nextstepaction"]
> [Monitorare e risolvere i problemi un'applicazione di archiviazione cloud](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
