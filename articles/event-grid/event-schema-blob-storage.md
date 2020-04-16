---
title: Archiviazione BLOB di Azure come origine griglia di eventiAzure Blob Storage as Event Grid source
description: Descrive le proprietà disponibili per gli eventi di archiviazione BLOB con Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: cfc6e4790b67137b423cc90d93874d4914f81251
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393383"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>Archiviazione BLOB di Azure come origine Griglia di eventiAzure Blob Storage as an Event Grid source

Questo articolo illustra le proprietà e lo schema per gli eventi di archiviazione BLOB.Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Fornisce inoltre un elenco di guide introduttive ed esercitazioni per usare Archiviazione BLOB di Azure come origine eventi.


>[!NOTE]
> Solo gli account di archiviazione di tipo **StorageV2 (uso generale v2),** **BlockBlobStorage**e **BlobStorage** supportano l'integrazione degli eventi. Il tipo **Archiviazione (utilizzo generico V1)***non* supporta l'integrazione con Griglia di eventi.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="list-of-events-for-blob-rest-apis"></a>Elenco di eventi per le API REST BLOB

Questi eventi vengono attivati quando un client crea, sostituisce o elimina un BLOB chiamando le API REST BLOB.

 |Nome evento |Descrizione|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Generato quando un BLOB viene creato o sostituito. <br>In particolare, questo evento viene `PutBlob`attivato `PutBlockList`quando `CopyBlob` i client utilizzano le operazioni , , o disponibili nell'API REST BLOB.   |
 |**Microsoft.Storage.BlobDeleted** |Attivato quando un BLOB viene eliminato. <br>In particolare, questo evento viene `DeleteBlob` attivato quando i client chiamano l'operazione disponibile nell'API REST BLOB. |

> [!NOTE]
> Se si desidera assicurarsi che l'evento **Microsoft.Storage.BlobCreated** venga attivato solo quando viene `CopyBlob` `PutBlob`eseguito `PutBlockList` il commit completo di un BLOB di blocchi, filtrare l'evento per le chiamate all'API REST e , e . Queste chiamate API attivano l'evento **Microsoft.Storage.BlobCreated** solo dopo il commit completo dei dati in un BLOB di blocchi. Per informazioni su come creare un filtro, vedere [Filtrare gli eventi per Griglia di eventi.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Elenco degli eventi per le API REST di Azure Data Lake Storage Gen 2

Questi eventi vengono attivati se si abilita uno spazio dei nomi gerarchico nell'account di archiviazione e i client chiamano le API REST di Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Lake Storage Gen2, vedere [Introduzione ad Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)

|Nome evento|Descrizione|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | Generato quando un BLOB viene creato o sostituito. <br>In particolare, questo evento viene `CreateFile` attivato `FlushWithClose` quando i client usano le operazioni e disponibili nell'API REST di Azure Data Lake Storage Gen2.Specifically, this event is triggered when clients use the e operations that are available in the Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobDeleted** |Attivato quando un BLOB viene eliminato. <br>In particolare, questo evento viene attivato `DeleteFile` anche quando i client chiamano l'operazione disponibile nell'API REST di Azure Data Lake Storage Gen2.Specifically, This event is also triggered when clients call the operation that is available in the Azure Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobRenamed**|Generato quando un BLOB viene rinominato. <br>In particolare, questo evento viene `RenameFile` attivato quando i client usano l'operazione disponibile nell'API REST di Azure Data Lake Storage Gen2.Specifically, this event is triggered when clients use the operation that is available in the Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryCreated**|Generato quando viene creata una directory. <br>In particolare, questo evento viene `CreateDirectory` attivato quando i client usano l'operazione disponibile nell'API REST di Azure Data Lake Storage Gen2.Specifically, this event is triggered when clients use the operation that is available in the Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryRinominato**|Generato quando una directory viene rinominata. <br>In particolare, questo evento viene `RenameDirectory` attivato quando i client usano l'operazione disponibile nell'API REST di Azure Data Lake Storage Gen2.Specifically, this event is triggered when clients use the operation that is available in the Azure Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryDeleted**|Attivato quando viene eliminata una directory. <br>In particolare, questo evento viene `DeleteDirectory` attivato quando i client usano l'operazione disponibile nell'API REST di Azure Data Lake Storage Gen2.Specifically, this event is triggered when clients use the operation that is available in the Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Se si vuole assicurarsi che l'evento **Microsoft.Storage.BlobCreated** venga attivato solo quando viene `FlushWithClose` eseguito il commit completo di un BLOB di blocchi, filtrare l'evento per la chiamata all'API REST. Questa chiamata API attiva l'evento **Microsoft.Storage.BlobCreated** solo dopo il commit completo dei dati in un BLOB in blocchi. Per informazioni su come creare un filtro, vedere [Filtrare gli eventi per Griglia di eventi.](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)

<a id="example-event" />

### <a name="the-contents-of-an-event-response"></a>Il contenuto di una risposta all'evento

Quando viene attivato un evento, il servizio Griglia di eventi invia i dati relativi a tale evento all'endpoint di sottoscrizione.

Questa sezione contiene un esempio dell'aspetto dei dati per ogni evento di archiviazione BLOB.

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft.Storage.BlobCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobCreated (Data Lake Storage Gen2)

Se l'account di archiviazione BLOB ha uno spazio dei nomi gerarchico, i dati sono simili all'esempio precedente, ad eccezione di queste modifiche:If the blob storage account has a hierarchical namespace, the data looks similar to the previous example with the exception of these changes:

* La `dataVersion` chiave è impostata `2`su un valore di .

* La `data.api` chiave è impostata sulla stringa `CreateFile` o `FlushWithClose`su .

* La `contentOffset` chiave è inclusa nel set di dati.

> [!NOTE]
> Se le `PutBlockList` applicazioni usano l'operazione per caricare un nuovo BLOB nell'account, i dati non conterranno queste modifiche.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft.Storage.BlobDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobDeleted (Data Lake Storage Gen2)

Se l'account di archiviazione BLOB ha uno spazio dei nomi gerarchico, i dati sono simili all'esempio precedente, ad eccezione di queste modifiche:If the blob storage account has a hierarchical namespace, the data looks similar to the previous example with the exception of these changes:

* La `dataVersion` chiave è impostata `2`su un valore di .

* La `data.api` chiave è impostata sulla stringa `DeleteFile`.

* La `url` chiave contiene `dfs.core.windows.net`il percorso .

> [!NOTE]
> Se le `DeleteBlob` applicazioni usano l'operazione per eliminare un BLOB dall'account, i dati non conterranno queste modifiche.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft.Storage.BlobRenamed

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft.Storage.DirectoryCreated

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed (evento)

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft.Storage.DirectoryDeleted

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| api | string | L'operazione che ha attivato l'evento. |
| clientRequestId | string | un ID di richiesta fornito dal client per l'operazione dell'API di archiviazione. Questo ID può essere usato per correlare i log di diagnostica di Archiviazione di Azure usando il campo "client-request-id" nei log e può essere fornito nelle richieste client usando l'intestazione "x-ms-client-request-id". Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| requestId | string | ID di richiesta generato dal servizio per l'operazione API di archiviazione. Può essere usato per la correlazione ai log di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei log e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | integer | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. I valori validi sono "BlockBlob" o "PageBlob". |
| contentOffset | d'acquisto | Offset in byte di un'operazione di scrittura eseguita nel punto in cui l'applicazione che attiva eventi ha completato la scrittura nel file. <br>Viene visualizzato solo per gli eventi attivati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico.|
| DestinazioneUrl |string | URL del file che esisterà dopo il completamento dell'operazione. Ad esempio, se un file `destinationUrl` viene rinominato, la proprietà contiene l'URL del nuovo nome file. <br>Viene visualizzato solo per gli eventi attivati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico.|
| sourceUrl (informazioni in base all' |string | URL del file esistente prima dell'operazione. Ad esempio, se un file `sourceUrl` viene rinominato, il contiene l'URL del nome del file originale prima dell'operazione di ridenominazione. <br>Viene visualizzato solo per gli eventi attivati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico. |
| url | string | Percorso del BLOB. <br>Se il client usa un'API REST BLOB, l'URL ha questa struttura: * \<\>storage-account-name\<.blob.core.windows.net/ nome-contenitore nome-contenitore\>/\<.\>* <br>Se il client usa un'API REST di Data Lake Storage, l'URL ha questa struttura: * \<\>storage-account-name\<.dfs.core.windows.net/ nome-file-system\>/\<nome-file.\>* |
| ricorsiva | string | `True`per eseguire l'operazione su tutte le directory figlio; in `False`caso contrario. <br>Viene visualizzato solo per gli eventi attivati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico. |
| sequencer | string | Valore stringa opaca che rappresenta la sequenza logica di eventi per qualsiasi nome di BLOB specifico.  Gli utenti possono usare il confronto tra stringhe standard per comprendere la sequenza relativa di due eventi sullo stesso nome di BLOB. |
| storageDiagnostics | object | Dati di diagnostica occasionalmente inclusi dal servizio Archiviazione di Azure. Quando è presente, questa proprietà deve essere ignorata dai consumer di eventi. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni ed es.
|Titolo  |Descrizione  |
|---------|---------|
| [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con l'interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare PowerShell per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Guida introduttiva: creare e instradare eventi di archiviazione BLOB con il portale di Azure](blob-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Interfaccia della riga di comando di Azure: sottoscrivere eventi per un account di archiviazione BLOB](./scripts/event-grid-cli-blob.md) | Script di esempio che esegue la sottoscrizione a un evento per un account di archiviazione BLOB. Invia l'evento a un webhook. |
| [PowerShell: sottoscrivere eventi per un account di archiviazione BLOB](./scripts/event-grid-powershell-blob.md) | Script di esempio che esegue la sottoscrizione a un evento per un account di archiviazione BLOB. Invia l'evento a un webhook. |
| [Modello di Resource Manager: creare un archivio e una sottoscrizione BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per quell'account di archiviazione. Invia eventi a un webhook. |
| [Panoramica: reazione a eventi di Archiviazione BLOB di Azure](../storage/blobs/storage-blob-event-overview.md) | Panoramica dell'integrazione dell'archivio BLOB con Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di archiviazione BLOB, vedere [Indirizzare eventi di archiviazione BLOB - Interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
