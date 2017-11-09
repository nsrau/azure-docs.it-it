---
title: Schema di eventi di Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi con Griglia di eventi di Azure."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/20/2017
ms.author: babanisa
ms.openlocfilehash: e251cbfe7c4d8dfbd492817a8fa7af48e6b379df
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-grid-event-schema"></a>Schema di eventi di Griglia di eventi di Azure

Questo articolo illustra le proprietà e lo schema per gli eventi. Gli eventi sono costituiti da un set di cinque proprietà di tipo stringa obbligatorie e un oggetto data obbligatorio. Le proprietà sono comuni a tutti gli eventi di tutti gli autori. L'oggetto data contiene le proprietà specifiche per ogni editore. Per gli argomenti di sistema, le proprietà sono specifiche del provider di risorse, ad esempio Archiviazione di Azure o Hub eventi di Azure.

Gli eventi vengono inviati a Griglia di eventi di Azure in una matrice, che può contenere più oggetti evento. Se c'è un unico evento, la matrice ha una lunghezza pari a 1. La matrice può avere una dimensione totale massima di 1 MB. Per ogni evento nella matrice il limite è 64 KB.
 
## <a name="event-properties"></a>Proprietà degli eventi

Tutti gli eventi contengono gli stessi dati di livello principale indicati di seguito:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento specifici del provider di risorse. |

## <a name="available-event-sources"></a>Origini evento disponibili

Le origini evento seguenti pubblicano gli eventi per l'utilizzo tramite Griglia di eventi:

* Gruppi di risorse (operazioni di gestione)
* Sottoscrizioni di Azure (operazioni di gestione)
* Hub eventi
* Argomenti personalizzati

## <a name="azure-subscriptions"></a>Sottoscrizioni Azure

Le sottoscrizioni di Azure possono ora generare eventi di gestione da Azure Resource Manager, ad esempio quando viene creata una VM o viene eliminato un account di archiviazione.

### <a name="available-event-types"></a>Tipi di evento disponibili

- **Microsoft.Resources.ResourceWriteSuccess**: generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito positivo.  
- **Microsoft.Resources.ResourceWriteFailure**: generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito negativo.  
- **Microsoft.Resources.ResourceWriteCancel**: generato quando un'operazione di creazione o aggiornamento di una risorsa viene annullata.  
- **Microsoft.Resources.ResourceDeleteSuccess**: generato quando un'operazione di eliminazione di una risorsa ha esito positivo.  
- **Microsoft.Resources.ResourceDeleteFailure**: generato quando un'operazione di eliminazione di una risorsa ha esito negativo.  
- **Microsoft.Resources.ResourceDeleteCancel**: generato quando un'operazione di eliminazione di una risorsa viene annullata. Ciò si verifica quando viene annullata la distribuzione di un modello.

### <a name="example-event-schema"></a>Schema di eventi di esempio

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Gruppi di risorse

I gruppi di risorse possono ora generare eventi di gestione da Azure Resource Manager, ad esempio quando viene creata una VM o viene eliminato un account di archiviazione.

### <a name="available-event-types"></a>Tipi di evento disponibili

- **Microsoft.Resources.ResourceWriteSuccess**: generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito positivo.  
- **Microsoft.Resources.ResourceWriteFailure**: generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito negativo.  
- **Microsoft.Resources.ResourceWriteCancel**: generato quando un'operazione di creazione o aggiornamento di una risorsa viene annullata.  
- **Microsoft.Resources.ResourceDeleteSuccess**: generato quando un'operazione di eliminazione di una risorsa ha esito positivo.  
- **Microsoft.Resources.ResourceDeleteFailure**: generato quando un'operazione di eliminazione di una risorsa ha esito negativo.  
- **Microsoft.Resources.ResourceDeleteCancel**: generato quando un'operazione di eliminazione di una risorsa viene annullata. Ciò si verifica quando viene annullata la distribuzione di un modello.

### <a name="example-event"></a>Evento di esempio

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Hub eventi

Gli eventi di Hub eventi vengono attualmente generati solo quando un file viene automaticamente inviato al servizio di archiviazione usando la funzionalità di acquisizione.

### <a name="available-event-types"></a>Tipi di evento disponibili

- **Microsoft.EventHub.CaptureFileCreated**: generato quando viene creato un file di acquisizione.

### <a name="example-event"></a>Evento di esempio

Questo evento di esempio mostra lo schema di un evento di Hub eventi generato quando la funzionalità di acquisizione archivia un file: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Archivio BLOB di Azure

### <a name="available-event-types"></a>Tipi di evento disponibili

- **Microsoft.Storage.BlobCreated**: generato quando viene creato un BLOB.
- **Microsoft.Storage.BlobDeleted**: generato quando viene eliminato un BLOB.

### <a name="example-event"></a>Evento di esempio

Questo evento di esempio mostra lo schema di un evento di archiviazione generato quando viene creato un BLOB: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>Argomenti personalizzati

Il payload di dati degli eventi personalizzati è definito dall'utente e può essere qualsiasi oggetto JSON ben formattato. I dati di livello principale devono contenere gli stessi campi degli eventi standard definiti dalle risorse. Quando si pubblicano eventi in argomenti personalizzati, prendere in considerazione la modellazione dell'oggetto degli eventi in modo da supportare le operazioni di instradamento e filtro.

### <a name="example-event"></a>Evento di esempio

L'esempio seguente mostra un evento per un argomento personalizzato:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
