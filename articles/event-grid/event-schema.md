---
title: Schema di eventi di Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi con Griglia di eventi di Azure."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Schema di eventi di Griglia di eventi di Azure

Questo articolo descrive le proprietà e lo schema presenti per tutti gli eventi. Gli eventi sono costituiti da un set di cinque proprietà di tipo stringa obbligatorie e un oggetto data obbligatorio. Le proprietà sono comuni a tutti gli eventi di tutti gli autori. L'oggetto data contiene le proprietà specifiche per ogni editore. Per gli argomenti di sistema, le proprietà sono specifiche del provider di risorse, ad esempio Archiviazione di Azure o Hub eventi di Azure.

Gli eventi vengono inviati a Griglia di eventi di Azure in una matrice, che può contenere più oggetti evento. Se c'è un unico evento, la matrice ha una lunghezza pari a 1. La matrice può avere una dimensione totale massima di 1 MB. Per ogni evento nella matrice il limite è 64 KB.

## <a name="event-schema"></a>Schema di eventi

L'esempio seguente illustra le proprietà usate da tutti gli autori di eventi:

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

Ad esempio, lo schema pubblicato per un evento di archiviazione BLOB di Azure è:

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

Per informazioni sulle proprietà nell'oggetto dati, vedere l'origine dell'evento:

* [Sottoscrizioni di Azure (operazioni di gestione)](event-schema-subscriptions.md)
* [Archiviazione BLOB](event-schema-blob-storage.md)
* [Hub eventi](event-schema-event-hubs.md)
* [Gruppi di risorse (operazioni di gestione)](event-schema-resource-groups.md)

Per gli argomenti personalizzati, l'autore dell'evento stabilisce l'oggetto dati. I dati di livello principale devono contenere gli stessi campi degli eventi standard definiti dalle risorse. Quando si pubblicano eventi in argomenti personalizzati, prendere in considerazione la modellazione dell'oggetto degli eventi in modo da supportare le operazioni di instradamento e filtro.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
