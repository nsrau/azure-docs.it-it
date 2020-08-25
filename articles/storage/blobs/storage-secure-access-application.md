---
title: Proteggere l'accesso ai dati delle applicazioni
titleSuffix: Azure Storage
description: Usare i token di firma di accesso condiviso, la crittografia e HTTPS per proteggere i dati dell'applicazione nel cloud.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc
ms.openlocfilehash: b5ca24a68b271c08ea7cd4196d5b8659eb0262d2
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "85367377"
---
# <a name="secure-access-to-application-data"></a>Proteggere l'accesso ai dati delle applicazioni

Questa è la terza di una serie di esercitazioni. Si apprende come proteggere l'accesso all'account di archiviazione. 

Nella terza parte della serie si apprenderà come:

> [!div class="checklist"]
> * Usare i token di firma di accesso condiviso per accedere a immagini di anteprima
> * Attivare la crittografia lato server
> * Abilitare il trasporto solo HTTPS

[Archiviazione BLOB di Azure](../common/storage-introduction.md#blob-storage) è un servizio affidabile per l'archiviazione di file per le applicazioni. Questa esercitazione estende l'[argomento precedente][previous-tutorial] per illustrare come proteggere l'accesso all'account di archiviazione da un'applicazione Web. Dopo il completamento le immagini vengono crittografate e l'app Web usa i token di firma di accesso condiviso sicuri per accedere alle immagini di anteprima.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario aver completato l'esercitazione di archiviazione precedente: [Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi][previous-tutorial].

## <a name="set-container-public-access"></a>Impostare l'accesso pubblico al contenitore

In questa parte della serie di esercitazioni si usano i token di firma di accesso condiviso per l'accesso alle anteprime. In questo passaggio si imposta l'accesso pubblico del contenitore *thumbnails* su `off`.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurare i token di firma di accesso condiviso per le anteprime

Nella prima parte di questa serie di esercitazioni l'applicazione Web visualizzava immagini di un contenitore pubblico. In questa parte della serie si usano token di firma di accesso condiviso per recuperare le immagini di anteprima. I token di firma di accesso condiviso consentono di garantire l'accesso limitato a un contenitore o un BLOB in base all'IP, al protocollo, all'intervallo di tempo o ai diritti concessi. Per altre informazioni sulla firma di accesso condiviso, vedere [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso](../common/storage-sas-overview.md).

In questo esempio il repository di codice sorgente usa il ramo `sasTokens`, che dispone di un esempio di codice aggiornato. Eliminare la distribuzione di GitHub esistente con [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Quindi configurare la distribuzione GitHub all'app Web con il comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Nel comando seguente `<web-app>` è il nome dell'app Web.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

Il ramo `sasTokens` del repository aggiorna il file `StorageHelper.cs`. Sostituisce l'attività `GetThumbNailUrls` con l'esempio di codice riportato di seguito. L'attività aggiornata recupera gli URL delle anteprime usando [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) per specificare l'ora di inizio, l'ora di scadenza e le autorizzazioni per il token di firma di accesso condiviso. Dopo la distribuzione l'app Web recupera le anteprime con un URL tramite un token di firma di accesso condiviso. L'attività aggiornata viene visualizzata nell'esempio seguente:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

Nell'attività precedente vengono usate le classi, le proprietà e i metodi seguenti:

| Classe | Proprietà | Metodi |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Nome](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Query](/dotnet/api/system.uribuilder.query) |  |
|[Elenco](/dotnet/api/system.collections.generic.list-1) | | [Aggiungere](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Crittografia di Archiviazione di Azure

La [crittografia di Archiviazione di Azure](../common/storage-service-encryption.md) consente di proteggere e salvaguardare i dati crittografando i dati inattivi e gestendo la crittografia e la decrittografia. Tutti i dati sono crittografati tramite la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide tra quelle disponibili.

È possibile scegliere di affidare a Microsoft la gestione delle chiavi di crittografia oppure usare chiavi personalizzate con chiavi gestite dal cliente con Azure Key Vault. Per altre informazioni, vedere [Usare chiavi gestite dal cliente con Azure Key Vault per gestire la crittografia di Archiviazione di Azure](../common/encryption-customer-managed-keys.md).

La crittografia di Archiviazione di Azure crittografa automaticamente i dati in tutti i livelli di prestazioni (Standard e Premium), tutti i modelli di distribuzione (distribuzione classica e Azure Resource Manager) e tutti i servizi di Archiviazione di Azure (BLOB, code, tabelle e file).

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
