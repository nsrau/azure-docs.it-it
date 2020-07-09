---
title: Schema CloudEvents v 1.0 con griglia di eventi di Azure
description: Questo articolo descrive come usare lo schema CloudEvents per gli eventi in Griglia di eventi di Azure. Il servizio supporta gli eventi nell'implementazione JSON di CloudEvents.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1c6085c0397f23d44cf11d43a3e1da71f0e728bb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103076"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Schema CloudEvents v 1.0 con griglia di eventi di Azure

Oltre allo [schema di eventi predefinito](event-schema.md), Griglia di eventi di Azure supporta in modo nativo gli eventi nell'[implementazione JSON di CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e nel [binding del protocollo HTTP](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) è una [specifica aperta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) per la descrizione dei dati degli eventi.

CloudEvents semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo degli eventi basati sul cloud. Questo schema consente strumenti uniformi, modi standard per il routing e la gestione degli eventi e modi universali per la deserializzazione dello schema di eventi esterni. Con uno schema comune, è possibile integrare più facilmente il lavoro tra le piattaforme.

CloudEvents viene compilato da diversi [collaboratori](https://github.com/cloudevents/spec/blob/master/community/contributors.md), tra cui Microsoft, tramite [Cloud Native Computing Foundation](https://www.cncf.io/). È attualmente disponibile come versione 1.0.

Questo articolo descrive lo schema CloudEvents con griglia di eventi.

## <a name="sample-event-using-cloudevents-schema"></a>Evento di esempio con schema CloudEvents

Di seguito è riportato un esempio di un evento di archiviazione BLOB di Azure nel formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Una descrizione dettagliata dei campi disponibili, dei relativi tipi e delle definizioni in CloudEvents v1.0 è [disponibile qui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

I valori delle intestazioni per gli eventi recapitati nello schema CloudEvents e nello schema Griglia di eventi sono gli stessi, ad eccezione di `content-type`. Per lo schema CloudEvents, tale valore intestazione è `"content-type":"application/cloudevents+json; charset=utf-8"`. Per lo schema Griglia di eventi, tale valore intestazione è `"content-type":"application/json; charset=utf-8"`.

## <a name="event-grid-for-cloudevents"></a>Griglia di eventi per CloudEvents

È possibile usare Griglia di eventi sia per l'input che per l'output degli eventi nello schema CloudEvents. È possibile usare CloudEvents per gli eventi di sistema, ad esempio gli eventi di archiviazione BLOB, gli eventi dell'hub IoT e gli eventi personalizzati. Può anche trasformare tali eventi in transito in entrambe le direzioni.


| Schema di input       | Schema di output.
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato di Griglia di eventi  | Formato CloudEvents
| Formato di Griglia di eventi  | Formato di Griglia di eventi

Per tutti gli schemi di eventi, Griglia di eventi richiede la convalida quando si esegue la pubblicazione in un argomento di Griglia di eventi e quando si crea una sottoscrizione di eventi. Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

## <a name="next-steps"></a>Passaggi successivi
Vedere [come usare lo schema CloudEvents v 1.0 con griglia di eventi](cloudevents-schema.md).  
