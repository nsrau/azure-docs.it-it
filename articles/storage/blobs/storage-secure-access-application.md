---
title: Proteggere l'accesso ai dati di un'applicazione nel cloud con Archiviazione di Azure | Microsoft Docs
description: Usare i token di firma di accesso condiviso, la crittografia e HTTPS per proteggere i dati dell'applicazione nel cloud
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: tutorial
ms.date: 03/06/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 66a5f7e6872a76c91f1f5f1a4b0b1973cb890b0f
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Proteggere l'accesso ai dati di un'applicazione nel cloud

Questa è la terza di una serie di esercitazioni. Si apprende come proteggere l'accesso all'account di archiviazione. 

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Usare i token di firma di accesso condiviso per accedere a immagini di anteprima
> * Attivare la crittografia lato server
> * Abilitare il trasporto solo HTTPS

[Archiviazione BLOB di Azure](../common/storage-introduction.md#blob-storage) è un servizio affidabile per l'archiviazione di file per le applicazioni. Questa esercitazione estende [l'argomento precedente][previous-tutorial] e spiega come proteggere l'accesso all'account di archiviazione da un'applicazione Web. Dopo il completamento le immagini vengono crittografate e l'app Web usa i token di firma di accesso condiviso sicuri per accedere alle immagini di anteprima.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione è necessario avere completato l'esercitazione di archiviazione precedente: [Automatizzare il ridimensionamento delle immagini caricate con la Griglia di eventi di Azure][previous-tutorial]. 

## <a name="set-container-public-access"></a>Impostare l'accesso pubblico al contenitore

In questa parte della serie di esercitazioni si usano i token di firma di accesso condiviso per l'accesso alle anteprime. In questo passaggio si imposta l'accesso pubblico del contenitore _thumbs_ su `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurare i token di firma di accesso condiviso per le anteprime

Nella prima parte di questa serie di esercitazioni l'applicazione Web visualizzava immagini di un contenitore pubblico. In questa parte della serie vengono usati token di [firma di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) per recuperare le immagini di anteprima. I token di firma di accesso condiviso consentono di garantire l'accesso limitato a un contenitore o un BLOB in base all'IP, al protocollo, all'intervallo di tempo o ai diritti concessi.

In questo esempio il repository di codice sorgente usa il ramo `sasTokens`, che dispone di un esempio di codice aggiornato. Eliminare la distribuzione di GitHub esistente con [az webapp deployment source delete](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_delete). Quindi configurare la distribuzione GitHub all'app Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config).  

Nel comando seguente `<web-app>` è il nome dell'app Web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

Il ramo `sasTokens` del repository aggiorna il file `StorageHelper.cs`. Sostituisce l'attività `GetThumbNailUrls` con l'esempio di codice riportato di seguito. L'attività aggiornata recupera gli URL delle anteprime impostando [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) per specificare l'ora di inizio, l'ora di scadenza e le autorizzazioni per il token di firma di accesso condiviso. Dopo la distribuzione l'app Web recupera le anteprime con un URL tramite un token di firma di accesso condiviso. L'attività aggiornata viene visualizzata nell'esempio seguente:
    
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

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

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
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Risultati](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Autorizzazioni](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Modello di crittografia lato server

La soluzione [Crittografia del servizio di archiviazione (SSE)](../common/storage-service-encryption.md) di Azure consente di proteggere e salvaguardare i dati. La soluzione SSE garantisce la crittografia dei dati inattivi e gestisce la crittografia, la decrittografia e la gestione delle chiavi. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

Crittografia del servizio di archiviazione crittografa automaticamente i dati in tutti i livelli di prestazioni (Standard e Premium), tutti i modelli di distribuzione (distribuzione classica e Azure Resource Manager) e tutti i servizi di archiviazione di Azure (BLOB, code, tabelle e file). 

## <a name="enable-https-only"></a>Abilitare solo HTTPS

Per garantire che le richieste di dati da e verso un account di archiviazione siano protette, è possibile consentire solo le richieste HTTPS. Aggiornare il protocollo richiesto per l'account di archiviazione tramite il comando [az storage account update](/cli/azure/storage/account#az_storage_account_update).

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