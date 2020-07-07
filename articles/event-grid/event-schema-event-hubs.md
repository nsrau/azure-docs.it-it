---
title: Hub eventi di Azure come origine di griglia di eventi
description: Descrive le proprietà disponibili per gli eventi degli hub eventi con Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81393347"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Hub eventi di Azure come origine di griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi degli hub eventi.Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Hub eventi genera il tipo di evento **Microsoft.EventHub.CaptureFileCreated** quando viene creato un file di acquisizione.

### <a name="example-event"></a>Evento di esempio

Questo evento di esempio mostra lo schema di un evento di hub eventi generato quando la funzionalità di acquisizione archivia un file: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
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
| data | object | Dati dell'evento dell'hub eventi. |
| dataVersion | string | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Type | Description |
| -------- | ---- | ----------- |
| fileUrl | string | Percorso del file di acquisizione. |
| fileType | string | Tipo di file del file di acquisizione. |
| partitionId | string | ID della partizione. |
| sizeInBytes | integer | Dimensioni del file. |
| eventCount | integer | Numero di eventi nel file. |
| firstSequenceNumber | integer | Numero di sequenza minore nella coda. |
| lastSequenceNumber | integer | Ultimo numero di sequenza nella coda. |
| firstEnqueueTime | string | Primo orario nella coda. |
| lastEnqueueTime | string | Ultimo orario nella coda. |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure

|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per informazioni sulla gestione degli eventi dell'hub eventi, vedere [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md).