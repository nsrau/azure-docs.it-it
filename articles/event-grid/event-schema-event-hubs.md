---
title: Schema di eventi degli hub eventi di Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi degli hub eventi con Griglia di eventi di Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: tomfitz
ms.openlocfilehash: 80959ee589a1cfcf317a98c3bafd7f92c796fc2d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schema di eventi di Griglia di eventi di Azure per gli hub eventi

Questo articolo illustra le proprietà e lo schema per gli eventi degli hub eventi. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

### <a name="available-event-types"></a>Tipi di evento disponibili

Hub eventi genera il tipo di evento **Microsoft.EventHub.CaptureFileCreated** quando viene creato un file di acquisizione.

## <a name="example-event"></a>Evento di esempio

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
        }
    }
]
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
| data | object | Dati dell'evento dell'hub eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| fileUrl | string | Percorso del file di acquisizione. |
| fileType | string | Tipo di file del file di acquisizione. |
| partitionId | string | ID della partizione. |
| sizeInBytes | numero intero | Dimensioni del file. |
| eventCount | numero intero | Numero di eventi nel file. |
| firstSequenceNumber | numero intero | Numero di sequenza minore nella coda. |
| lastSequenceNumber | numero intero | Ultimo numero di sequenza nella coda. |
| firstEnqueueTime | string | Primo orario nella coda. |
| lastEnqueueTime | string | Ultimo orario nella coda. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
* Per informazioni sulla gestione degli eventi dell'hub eventi, vedere [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md).