---
title: Usare Griglia di eventi di Azure con gli eventi nello schema CloudEvents
description: Descrive come impostare lo schema CloudEvents per gli eventi in Griglia di eventi di Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: babanisa
ms.openlocfilehash: a2cccbb4feaa7b6f3f51ac7204af4a3e1efc6349
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625594"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Usare lo schema CloudEvents con Griglia di eventi

Oltre allo [schema di eventi predefinito](event-schema.md), Griglia di eventi di Azure supporta in modo nativo gli eventi nello [schema JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) è una [specifica standard aperta](https://github.com/cloudevents/spec/blob/master/spec.md) per descrivere i dati degli eventi in modo comune.

CloudEvents semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo degli eventi basati sul cloud. Questo schema consente strumenti uniformi, modi standard per il routing e la gestione degli eventi e modi universali per la deserializzazione dello schema di eventi esterni. Con uno schema comune, è possibile integrare più facilmente il lavoro tra le piattaforme.

CloudEvents viene compilato da diversi [collaboratori](https://github.com/cloudevents/spec/blob/master/community/contributors.md), tra cui Microsoft, tramite [Cloud Native Compute Foundation](https://www.cncf.io/). È attualmente disponibile come versione 0.1.

Questo articolo descrive come usare lo schema CloudEvents con Griglia di eventi.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Schema CloudEvent

Di seguito è riportato un esempio di un evento di archiviazione BLOB di Azure nel formato CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
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
```

In CloudEvents v0.1 sono disponibili le proprietà seguenti:

| CloudEvents        | type     | Valore JSON di esempio             | DESCRIZIONE                                                        | Mapping di Griglia di eventi
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | string   | "com.example.someevent"          | Tipo di occorrenza che si è verificata                                   | eventType
| eventTypeVersion   | string   | "1.0"                            | Versione di eventType (facoltativa)                            | dataVersion
| cloudEventsVersion | string   | "0.1"                            | Versione della specifica CloudEvents usata dall'evento        | *passata*
| una sezione source             | URI      | "/mycontext"                     | Descrive il producer di eventi                                       | topic#subject
| eventID            | string   | "1234-1234-1234"                 | ID dell'evento                                                    | id
| eventTime          | Timestamp| "2018-04-05T17:31:00Z"           | Timestamp del momento in cui l'evento si è verificato (facoltativo)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | Collegamento allo schema rispettato dall'attributo dati (facoltativo) | *non usato*
| contentType        | string   | "application/json"               | Descrive il formato di codifica dei dati (facoltativo)                       | *non usato*
| Estensioni         | Mappa      | { "extA": "vA", "extB", "vB" }  | Metadati aggiuntivi (facoltativi)                                 | *non usato*
| data               | Oggetto   | { "objA": "vA", "objB", "vB" }  | Payload dell'evento (facoltativo)                                       | data

Per altre informazioni, vedere la [specifica di CloudEvents](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Configurare Griglia di eventi per CloudEvents

Griglia di eventi di Azure offre attualmente il supporto in anteprima per l'input e l'output in formato JSON di CloudEvents in **Stati Uniti centro-occidentali**, **Stati Uniti centrali** ed **Europa settentrionale**.

È possibile usare Griglia di eventi sia per l'input che per l'output degli eventi nello schema CloudEvents. È possibile usare CloudEvents per gli eventi di sistema, ad esempio gli eventi di archiviazione BLOB, gli eventi dell'hub IoT e gli eventi personalizzati. Può anche trasformare tali eventi in transito in entrambe le direzioni.


| Schema di input       | Schema di output.
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato di Griglia di eventi  | Formato CloudEvents
| Formato CloudEvents | Formato di Griglia di eventi
| Formato di Griglia di eventi  | Formato di Griglia di eventi

Per tutti gli schemi di eventi, Griglia di eventi richiede la convalida quando si esegue la pubblicazione in un argomento di Griglia di eventi e quando si crea una sottoscrizione di eventi. Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

### <a name="input-schema"></a>Schema di input

Per impostare lo schema di input in un argomento personalizzato su CloudEvents, usare il parametro seguente nell'interfaccia della riga di comando di Azure quando si crea l'argomento `--input-schema cloudeventv01schema`. L'argomento personalizzato prevede ora eventi in ingresso in formato CloudEvents v0.1.

Per creare un argomento di Griglia di eventi, usare:

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

La versione corrente di CloudEvents non supporta l'invio in batch di eventi. Per pubblicare eventi con lo schema CloudEvent in un argomento, pubblicare singolarmente ogni evento.

### <a name="output-schema"></a>Schema di output.

Per impostare lo schema di output in una sottoscrizione di eventi su CloudEvents, usare il parametro seguente nell'interfaccia della riga di comando di Azure quando si crea la sottoscrizione di eventi `--event-delivery-schema cloudeventv01schema`. Gli eventi per questa sottoscrizione di eventi vengono ora recapitati in formato CloudEvents v0.1.

Per creare una sottoscrizione di eventi, usare:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

La versione corrente di CloudEvents non supporta l'invio in batch di eventi. Una sottoscrizione di eventi configurata per lo schema CloudEvent riceve singolarmente ogni evento. Attualmente non è possibile usare un trigger di Griglia di eventi per un'app di Funzioni di Azure quando l'evento viene recapitato nello schema CloudEvents. È necessario usare un trigger HTTP.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Microsoft invita gli utenti a testare, commentare e [contribuire](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) a CloudEvents.
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
