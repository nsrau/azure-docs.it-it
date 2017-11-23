---
title: Schema di eventi di archiviazione BLOB per Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi di archiviazione BLOB con Griglia di eventi di Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: bdc64733b75fd809cf0245986aa96370343c1a34
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Schema di eventi di Griglia di eventi di Azure per l'archiviazione BLOB

Questo articolo illustra le proprietà e lo schema per gli eventi di archiviazione BLOB. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

L'archiviazione BLOB genera i tipi di eventi seguenti:

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | Generato quando viene creato un BLOB. |
| Microsoft.Storage.BlobDeleted | Generato quando viene eliminato un BLOB. |

## <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di un evento creato da un BLOB: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
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
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  }
}]
```

Lo schema di un evento eliminato da una BLOB è simile: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  }
}]
```
 
## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati relativi all'evento di archiviazione BLOB. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| api | string | L'operazione che ha attivato l'evento. |
| clientRequestId | string | Un valore opaco, generato dal client con un limite di caratteri di 1 KB. Quando è abilitata la registrazione analitica dell'archiviazione, viene registrato nel registro analisi. |
| requestId | string | Identificatore univoco per la richiesta. Usarlo per la risoluzione dei problemi della richiesta. |
| eTag | string | Il valore che è possibile usare per eseguire le operazioni in modo condizionale. |
| contentType | string | Il tipo di contenuto specificato per il BLOB. |
| contentLength | numero intero | La dimensione del BLOB in byte. |
| blobType | string | Il tipo di BLOB. |
| URL | string | Percorso del BLOB. |
| sequencer | string | Un valore controllato dall'utente che è possibile usare per tenere traccia delle richieste. |
| storageDiagnostics | object | Informazioni sulla diagnostica dell'archiviazione. |
 
## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per un'introduzione all'uso degli eventi di archiviazione BLOB, vedere [Indirizzare eventi di archiviazione BLOB - Interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
