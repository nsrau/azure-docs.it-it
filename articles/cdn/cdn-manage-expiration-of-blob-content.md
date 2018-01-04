---
title: Gestire la scadenza del servizio Archiviazione BLOB di Azure nella rete per la distribuzione di contenuti di Azure | Microsoft Docs
description: Informazioni sulle opzioni per il controllo della durata per i BLOB nel caching della rete CDN di Azure.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: 6f82ae396a17f903a522c716f73a5f7d2de660e7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-content-delivery-network"></a>Gestire la scadenza del servizio Archiviazione BLOB di Azure nella rete per la distribuzione di contenuti di Azure
> [!div class="op_single_selector"]
> * [Contenuto Web di Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Archivio BLOB di Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

Il [servizio di archiviazione BLOB](../storage/common/storage-introduction.md#blob-storage) in Archiviazione di Azure è una delle diverse origini basate su Azure integrate nella rete per la distribuzione di contenuti (CDN) di Azure. Qualsiasi contenuto BLOB accessibile pubblicamente può essere memorizzato nella cache della rete CDN di Azure fino allo scadere della relativa durata (TTL). La durata (TTL) è determinata dall'intestazione `Cache-Control` nella risposta HTTP del server di origine. Questo articolo descrive vari modi in cui è possibile impostare l'intestazione `Cache-Control` in un BLOB in Archiviazione di Azure.

È inoltre possibile controllare le impostazioni della cache dal portale di Azure mediante l'impostazione delle [regole di memorizzazione nella cache della rete CDN](cdn-caching-rules.md). Se si imposta una o più regole di memorizzazione nella cache e il relativo comportamento su **Ignora** o **Ignora cache**, le impostazioni di memorizzazione nella cache fornite in origine e descritte in questo articolo vengono ignorate. Per informazioni sui concetti generali sulla memorizzazione nella cache, vedere [Come funziona la memorizzazione nella cache](cdn-how-caching-works.md).

> [!TIP]
> È possibile scegliere di non impostare alcuna durata (TTL) per un BLOB. In questo caso, la rete CDN di Azure applica automaticamente una durata (TTL) predefinita di sette giorni, a meno che non siano state configurate le regole di memorizzazione nella cache nel portale di Azure. Questa impostazione predefinita di durata (TTL) si applica solo alle ottimizzazioni di distribuzione web generali. Per le ottimizzazioni di file di grandi dimensioni, il valore TTL predefinito è un giorno e per le ottimizzazioni dei flussi dei file multimediali, il valore TTL predefinito è un anno.
> 
> Per altre informazioni sull'uso della rete CDN di Azure per velocizzare l'accesso a BLOB e altri file, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](cdn-overview.md).
> 
> Per altre informazioni su Archiviazione BLOB di Azure, vedere [Introduzione all'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Impostazione delle intestazioni Cache-Control tramite Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) è uno dei modi più rapidi ed efficaci per amministrare i servizi di Azure. Usare il cmdlet `Get-AzureStorageBlob` per ottenere un riferimento al BLOB, quindi impostare la proprietà `.ICloudBlob.Properties.CacheControl`. 

Ad esempio: 

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> È anche possibile usare PowerShell per [gestire i profili e gli endpoint della rete CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Impostazione delle intestazioni Cache-Control tramite .NET
Per impostare un'intestazione `Cache-Control` tramite un codice .NET, usare la [libreria client di Archiviazione di Azure per .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) per impostare la proprietà [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx).

Ad esempio: 

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer container = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob blob = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> Altri esempi di codice .NET sono disponibili in [Esempi di Archiviazione BLOB di Azure per .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Impostazione delle intestazioni Cache-Control tramite altri metodi

### <a name="azure-storage-explorer"></a>Esplora archivi Azure
Con [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/), è possibile visualizzare e modificare le risorse di archiviazione BLOB, incluse le proprietà, come ad esempio la proprietà *CacheControl*. 

Per aggiornare la proprietà *CacheControl* di un BLOB con Azure Storage Explorer:
   1. Selezionare un BLOB e quindi selezionare **Proprietà** nel menu di scelta rapida. 
   2. Scorrere verso il basso fino a visualizzare la proprietà *CacheControl*.
   3. Immettere un valore e quindi fare clic su **Salva**.


![Proprietà di Azure Storage Explorer](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Interfaccia della riga di comando di Azure
Con l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) (CLI), è possibile gestire le risorse BLOB di Azure dalla riga di comando. Per impostare l'intestazione di controllo della cache quando si carica un blob con l'interfaccia della riga di comando di Azure, impostare la proprietà *cacheControl* usando l'opzione `-p`. Il seguente esempio mostra come impostare la durata TTL su un'ora (3600 secondi):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\test.txt myContainer test.txt
```

### <a name="azure-storage-services-rest-api"></a>API REST dei servizi di archiviazione di Azure
È possibile usare l'[API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx) per impostare in modo esplicito la prorpietà *x-ms-blob-cache-control* tramite le operazioni seguenti in una richiesta:
  
   - [Put Blob](https://msdn.microsoft.com/en-us/library/azure/dd179451.aspx)
   - [Elenco Put Block](https://msdn.microsoft.com/en-us/library/azure/dd179467.aspx)
   - [Impostare le proprietà BLOB](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Test dell'intestazione Cache-Control
È possibile verificare facilmente le impostazioni di durata (TTL) del BLOB. Con gli [strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser verificare che il BLOB includa l'intestazione della risposta `Cache-Control`. È anche possibile usare uno strumento come [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/) o [Fiddler](http://www.telerik.com/fiddler) per esaminare le intestazioni della risposta.

## <a name="next-steps"></a>Fasi successive
* [Informazioni su come gestire la scadenza del contenuto di Servizi cloud nella rete CDN di Azure](cdn-manage-expiration-of-cloud-service-content.md)
* [Informazioni sui concetti di memorizzazione nella cache](cdn-how-caching-works.md)

