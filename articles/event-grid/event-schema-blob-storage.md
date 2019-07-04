---
title: Schema di eventi di archiviazione BLOB per Griglia di eventi di Azure
description: Descrive le proprietà disponibili per gli eventi di archiviazione BLOB con Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445773"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schema di eventi di Griglia di eventi di Azure per l'archiviazione BLOB

Questo articolo illustra le proprietà e lo schema per gli eventi di archiviazione BLOB. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco di esercitazioni e script di esempio, vedere l'[origine eventi di archiviazione](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Elenco di eventi per le API REST Blob

Questi eventi vengono generati quando un client consente di creare, sostituisce o elimina un blob mediante la chiamata API REST Blob.

 |Nome evento |Descrizione|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Attivazione eseguita quando un blob viene creato o sostituito. <br>In particolare, questo evento viene generato quando i client usano i `PutBlob`, `PutBlockList`, o `CopyBlob` operazioni disponibili nell'API REST Blob.   |
 |**Microsoft.Storage.BlobDeleted** |Attivato quando viene eliminato un blob. <br>In particolare, questo evento viene generato quando i client chiamano il `DeleteBlob` operazione che è disponibile nell'API REST Blob. |

> [!NOTE]
> Se si desidera assicurarsi che il **Microsoft.Storage.BlobCreated** evento viene attivato solo quando un Blob in blocchi viene completamente eseguito il commit, filtrare l'evento per il `CopyBlob`, `PutBlob`, e `PutBlockList` chiama l'API REST. Questi trigger di chiamate API di **Microsoft.Storage.BlobCreated** evento solo dopo che i dati si impegnerà a un Blob in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi di griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Elenco degli eventi per le API REST di Azure Data Lake Storage generazione 2

Questi eventi vengono generati se si abilita un spazio dei nomi gerarchico dell'account di archiviazione e i client chiamano API REST di Azure Data Lake Storage Gen2.

> [!NOTE]
> Questi eventi sono in anteprima pubblica e sono disponibili solo le **Stati Uniti occidentali 2** e **Occidentali** aree.

 |Nome evento|Descrizione|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Attivazione eseguita quando un blob viene creato o sostituito. <br>In particolare, questo evento viene generato quando i client usano i `CreateFile` e `FlushWithClose` operazioni disponibili nell'API REST di Azure Data Lake archiviazione Gen2. |
 |**Microsoft.Storage.BlobDeleted** |Attivato quando viene eliminato un blob. <br>In particolare, questo evento viene generato anche quando i client chiamano il `DeleteFile` operazione che è disponibile nell'API REST di Azure Data Lake archiviazione Gen2. |
 |**Microsoft.Storage.BlobRenamed**|Attivazione eseguita quando un blob è stato rinominato. <br>In particolare, questo evento viene generato quando i client usano il `RenameFile` operazione che è disponibile nell'API REST di Azure Data Lake archiviazione Gen2.|
 |**Microsoft.Storage.DirectoryCreated**|Attivato quando viene creata una directory. <br>In particolare, questo evento viene generato quando i client usano il `CreateDirectory` operazione che è disponibile nell'API REST di Azure Data Lake archiviazione Gen2.|
 |**Microsoft.Storage.DirectoryRenamed**|Attivato quando una directory viene rinominata. <br>In particolare, questo evento viene generato quando i client usano il `RenameDirectory` operazione che è disponibile nell'API REST di Azure Data Lake archiviazione Gen2.|
 |**Microsoft.Storage.DirectoryDeleted**|Attivato quando viene eliminata una directory. <br>In particolare, questo evento viene generato quando i client usano il `DeleteDirectory` operazione che è disponibile nell'API REST di Azure Data Lake archiviazione Gen2.|

> [!NOTE]
> Se si desidera assicurarsi che il **Microsoft.Storage.BlobCreated** evento viene attivato solo quando un Blob in blocchi viene completamente eseguito il commit, filtrare l'evento per il `FlushWithClose` chiamata all'API REST. I trigger di chiamare questa API il **Microsoft.Storage.BlobCreated** evento solo dopo che i dati si impegnerà a un Blob in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi di griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Il contenuto di una risposta di eventi

Quando si verifica un evento, il servizio griglia di eventi invia i dati su tale evento all'endpoint di sottoscrizione.

In questa sezione contiene un esempio di tali dati aspetto per ogni evento di archiviazione blob.

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobCreated (Gen2 di archivio Data Lake)

Se l'account di archiviazione blob è uno spazio dei nomi gerarchico, i dati saranno simili all'esempio precedente, fatta eccezione per queste modifiche:

* Il `dataVersion` chiave è impostata su un valore di `2`.

* Il `data.api` chiave è impostata sulla stringa `CreateFile` o `FlushWithClose`.

* Il `contentOffset` key è incluso nel set di dati.

> [!NOTE]
> Se le applicazioni usano il `PutBlockList` operazione per caricare un nuovo blob nell'account, i dati non contenga queste modifiche.

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft.Storage.BlobDeleted (Gen2 di archivio Data Lake)

Se l'account di archiviazione blob è uno spazio dei nomi gerarchico, i dati saranno simili all'esempio precedente, fatta eccezione per queste modifiche:

* Il `dataVersion` chiave è impostata su un valore di `2`.

* Il `data.api` chiave è impostata sulla stringa `DeleteFile`.

* Il `url` chiave contiene il percorso `dfs.core.windows.net`.

> [!NOTE]
> Se le applicazioni usano il `DeleteBlob` operazione per eliminare un blob dall'account, i dati non contenga queste modifiche.

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft.Storage.DirectoryRenamed

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

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| topic | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | DESCRIZIONE |
| -------- | ---- | ----------- |
| api | string | L'operazione che ha attivato l'evento. |
| clientRequestId | string | un id richiesta generato dal client per l'operazione API di archiviazione. Questo id può essere utilizzato per correlare per i log di diagnostica di archiviazione di Azure usando il campo "client-request-id" nei log e può essere fornito nelle richieste client usando l'intestazione "x-ms-client-request-id". Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| requestId | string | ID di richiesta generato dal servizio per l'operazione API di archiviazione. Può essere usato per la correlazione ai log di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei log e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | numero intero | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. I valori validi sono "BlockBlob" o "PageBlob". |
| contentOffset | number | Offset in byte di un'operazione di scrittura eseguite in corrispondenza del punto in cui l'applicazione di attivazione di eventi completato la scrittura del file. <br>Viene visualizzata solo per gli eventi generati per gli account di archiviazione blob con uno spazio dei nomi gerarchico.|
| destinationUrl |string | L'url del file che sarà disponibili dopo il completamento dell'operazione. Ad esempio, se un file viene rinominato, il `destinationUrl` proprietà contiene l'url del nome del nuovo file. <br>Viene visualizzata solo per gli eventi generati per gli account di archiviazione blob con uno spazio dei nomi gerarchico.|
| sourceUrl |string | L'url del file che esiste prima dell'operazione. Ad esempio, se un file viene rinominato, il `sourceUrl` contiene l'url del nome del file originale prima dell'operazione di ridenominazione. <br>Viene visualizzata solo per gli eventi generati per gli account di archiviazione blob con uno spazio dei nomi gerarchico. |
| url | string | Percorso del BLOB. <br>Se il client utilizza un'API REST Blob, quindi l'url ha questa struttura:  *\<storage-account-name\>.blob.core.windows.net/\<-nome del contenitore\>/\<-nome del file \>* . <br>Se il client utilizza un'API REST di Data Lake Storage, quindi l'url ha questa struttura:  *\<storage-account-name\>.dfs.core.windows.net/\<--nome del file system\> / \<file-name\>* .
|
| recursive| string| `True` Per eseguire l'operazione per tutte le directory figlio; in caso contrario `False`. <br>Viene visualizzata solo per gli eventi generati per gli account di archiviazione blob con uno spazio dei nomi gerarchico. |
| sequencer | string | Valore stringa opaca che rappresenta la sequenza logica di eventi per qualsiasi nome di BLOB specifico.  Gli utenti possono usare il confronto tra stringhe standard per comprendere la sequenza relativa di due eventi sullo stesso nome di BLOB. |
| storageDiagnostics | object | Dati di diagnostica occasionalmente inclusi dal servizio Archiviazione di Azure. Quando è presente, questa proprietà deve essere ignorata dai consumer di eventi. |

|Proprietà|Type|Descrizione|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di archiviazione BLOB, vedere [Indirizzare eventi di archiviazione BLOB - Interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
