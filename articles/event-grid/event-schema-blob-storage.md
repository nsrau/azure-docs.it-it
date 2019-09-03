---
title: Schema di eventi di archiviazione BLOB per Griglia di eventi di Azure
description: Descrive le proprietà disponibili per gli eventi di archiviazione BLOB con Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bd85353aa37cf182a807d99cdc9fb63ead00edeb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232422"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schema di eventi di Griglia di eventi di Azure per l'archiviazione BLOB

Questo articolo illustra le proprietà e lo schema per gli eventi di archiviazione BLOB. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

Per un elenco di esercitazioni e script di esempio, vedere l'[origine eventi di archiviazione](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Elenco di eventi per le API REST BLOB

Questi eventi vengono attivati quando un client crea, sostituisce o Elimina un BLOB chiamando API REST BLOB.

 |Nome evento |Descrizione|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Attivato quando si crea o si sostituisce un BLOB. <br>In particolare, questo evento viene generato quando i client usano `PutBlob`le `PutBlockList`operazioni, `CopyBlob` o disponibili nell'API REST BLOB.   |
 |**Microsoft.Storage.BlobDeleted** |Attivato quando viene eliminato un BLOB. <br>In particolare, questo evento viene generato quando i client chiamano `DeleteBlob` l'operazione disponibile nell'API REST BLOB. |

> [!NOTE]
> Per assicurarsi che l'evento **Microsoft. storage. BlobCreated** venga attivato solo quando viene eseguito il commit completo di un BLOB in blocchi, filtrare l'evento per le `CopyBlob`chiamate `PutBlob`all'API `PutBlockList` Rest, e. Queste chiamate API attivano l'evento **Microsoft. storage. BlobCreated** solo dopo che è stato eseguito il commit completo dei dati in un BLOB in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Elenco degli eventi per Azure Data Lake Storage le API REST di generazione 2

Questi eventi vengono attivati se si Abilita uno spazio dei nomi gerarchico nell'account di archiviazione e i client chiamano Azure Data Lake Storage Gen2 API REST.

> [!NOTE]
> Questi eventi sono disponibili in anteprima pubblica e sono disponibili solo nelle aree **Stati Uniti occidentali 2** e **Stati Uniti centro-occidentali** .

 |Nome evento|DESCRIZIONE|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Attivato quando si crea o si sostituisce un BLOB. <br>In particolare, questo evento viene generato quando i client usano `CreateFile` le `FlushWithClose` operazioni e disponibili nell'API REST di Azure Data Lake storage Gen2. |
 |**Microsoft.Storage.BlobDeleted** |Attivato quando viene eliminato un BLOB. <br>In particolare, questo evento viene attivato anche quando i client chiamano `DeleteFile` l'operazione disponibile nell'API REST di Azure Data Lake storage Gen2. |
 |**Microsoft.Storage.BlobRenamed**|Attivato quando un BLOB viene rinominato. <br>In particolare, questo evento viene generato quando i client usano `RenameFile` l'operazione disponibile nell'API REST di Azure Data Lake storage Gen2.|
 |**Microsoft.Storage.DirectoryCreated**|Attivato quando viene creata una directory. <br>In particolare, questo evento viene generato quando i client usano `CreateDirectory` l'operazione disponibile nell'API REST di Azure Data Lake storage Gen2.|
 |**Microsoft.Storage.DirectoryRenamed**|Attivato quando una directory viene rinominata. <br>In particolare, questo evento viene generato quando i client usano `RenameDirectory` l'operazione disponibile nell'API REST di Azure Data Lake storage Gen2.|
 |**Microsoft.Storage.DirectoryDeleted**|Attivato quando una directory viene eliminata. <br>In particolare, questo evento viene generato quando i client usano `DeleteDirectory` l'operazione disponibile nell'API REST di Azure Data Lake storage Gen2.|

> [!NOTE]
> Per assicurarsi che l'evento **Microsoft. storage. BlobCreated** venga attivato solo quando viene eseguito il commit completo di un BLOB in blocchi, filtrare l'evento per la `FlushWithClose` chiamata all'API REST. Questa chiamata API attiva l'evento **Microsoft. storage. BlobCreated** solo dopo che è stato eseguito il commit completo dei dati in un BLOB in blocchi. Per informazioni su come creare un filtro, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Contenuto di una risposta di evento

Quando viene attivato un evento, il servizio griglia di eventi invia i dati relativi all'evento all'endpoint di sottoscrizione.

Questa sezione contiene un esempio dell'aspetto dei dati per ogni evento di archiviazione BLOB.

### <a name="microsoftstorageblobcreated-event"></a>Evento Microsoft. storage. BlobCreated

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Evento Microsoft. storage. BlobCreated (Data Lake Storage Gen2)

Se per l'account di archiviazione BLOB è presente uno spazio dei nomi gerarchico, i dati sono simili a quelli dell'esempio precedente, fatta eccezione per le modifiche seguenti:

* La `dataVersion` chiave è impostata su un valore di `2`.

* La `data.api` chiave viene impostata sulla stringa `CreateFile` o `FlushWithClose`.

* La `contentOffset` chiave viene inclusa nel set di dati.

> [!NOTE]
> Se le applicazioni usano `PutBlockList` l'operazione per caricare un nuovo BLOB nell'account, i dati non conterranno tali modifiche.

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

### <a name="microsoftstorageblobdeleted-event"></a>Evento Microsoft. storage. BlobDeleted

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Evento Microsoft. storage. BlobDeleted (Data Lake Storage Gen2)

Se per l'account di archiviazione BLOB è presente uno spazio dei nomi gerarchico, i dati sono simili a quelli dell'esempio precedente, fatta eccezione per le modifiche seguenti:

* La `dataVersion` chiave è impostata su un valore di `2`.

* La `data.api` chiave viene impostata sulla stringa `DeleteFile`.

* La `url` chiave contiene il percorso `dfs.core.windows.net`.

> [!NOTE]
> Se le applicazioni usano `DeleteBlob` l'operazione per eliminare un BLOB dall'account, i dati non conterranno tali modifiche.

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

### <a name="microsoftstorageblobrenamed-event"></a>Evento Microsoft. storage. BlobRenamed

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

### <a name="microsoftstoragedirectorycreated-event"></a>Evento Microsoft. storage. DirectoryCreated

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Evento Microsoft. storage. DirectoryRenamed

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Evento Microsoft. storage. DirectoryDeleted

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
| clientRequestId | string | ID richiesta fornito dal client per l'operazione dell'API di archiviazione. Questo ID può essere usato per la correlazione ai log di diagnostica di archiviazione di Azure usando il campo "client-Request-ID" nei log e può essere specificato nelle richieste client usando l'intestazione "x-MS-client-Request-ID". Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| requestId | string | ID di richiesta generato dal servizio per l'operazione API di archiviazione. Può essere usato per la correlazione ai log di diagnostica di Archiviazione di Azure usando il campo "request-id-header" nei log e viene restituito dall'avvio di una chiamata API nell'intestazione 'x-ms-request-id'. Vedere [Log Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format) (Formato del log). |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | numero intero | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. I valori validi sono "BlockBlob" o "PageBlob". |
| contentOffset | number | Offset in byte di un'operazione di scrittura eseguita nel punto in cui l'applicazione che ha generato l'evento ha completato la scrittura nel file. <br>Viene visualizzato solo per gli eventi generati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico.|
| destinationUrl |string | URL del file che presenterà al termine dell'operazione. Se, ad esempio, un file viene rinominato, la `destinationUrl` proprietà contiene l'URL del nuovo nome file. <br>Viene visualizzato solo per gli eventi generati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico.|
| sourceUrl |string | URL del file esistente prima dell'operazione. Se, ad esempio, un file viene rinominato, `sourceUrl` contiene l'URL del nome file originale prima dell'operazione di ridenominazione. <br>Viene visualizzato solo per gli eventi generati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico. |
| url | string | Percorso del BLOB. <br>Se il client usa un'API REST BLOB, l'URL ha questa struttura:  *\<storage-account-name\>.\<BLOB.Core.Windows.NET/nome-\>contenitore/\<-nome-file\>* . <br>Se il client usa un'API REST di data Lake storage, l'URL ha questa struttura:  *\<storage-account-name\>./\<\>\<DFS.Core.Windows.NET/nome file-System-Name \>* . |
| recursive | string | `True`per eseguire l'operazione su tutte le directory figlio; in `False`caso contrario,. <br>Viene visualizzato solo per gli eventi generati negli account di archiviazione BLOB con uno spazio dei nomi gerarchico. |
| sequencer | string | Valore stringa opaca che rappresenta la sequenza logica di eventi per qualsiasi nome di BLOB specifico.  Gli utenti possono usare il confronto tra stringhe standard per comprendere la sequenza relativa di due eventi sullo stesso nome di BLOB. |
| storageDiagnostics | object | Dati di diagnostica occasionalmente inclusi dal servizio Archiviazione di Azure. Quando è presente, questa proprietà deve essere ignorata dai consumer di eventi. |
|Proprietà|Type|DESCRIZIONE|
|-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di archiviazione BLOB, vedere [Indirizzare eventi di archiviazione BLOB - Interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
