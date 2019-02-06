---
title: Usare Griglia di eventi di Azure con gli eventi nello schema CloudEvents
description: Descrive come impostare lo schema CloudEvents per gli eventi in Griglia di eventi di Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 81f9db7e90e2cef85d41561cc677db3d6422ef45
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079869"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Usare lo schema CloudEvents con Griglia di eventi

Oltre allo [schema di eventi predefinito](event-schema.md), Griglia di eventi di Azure supporta in modo nativo gli eventi nello [schema JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) è una [specifica aperta](https://github.com/cloudevents/spec/blob/master/spec.md) per la descrizione dei dati degli eventi.

CloudEvents semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo degli eventi basati sul cloud. Questo schema consente strumenti uniformi, modi standard per il routing e la gestione degli eventi e modi universali per la deserializzazione dello schema di eventi esterni. Con uno schema comune, è possibile integrare più facilmente il lavoro tra le piattaforme.

CloudEvents viene compilato da diversi [collaboratori](https://github.com/cloudevents/spec/blob/master/community/contributors.md), tra cui Microsoft, tramite [Cloud Native Computing Foundation](https://www.cncf.io/). È attualmente disponibile come versione 0.1.

Questo articolo descrive come usare lo schema CloudEvents con Griglia di eventi.

## <a name="install-preview-feature"></a>Installare la funzionalità di anteprima

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

| CloudEvents        | Type     | Valore JSON di esempio             | DESCRIZIONE                                                        | Mapping di Griglia di eventi
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

I valori delle intestazioni per gli eventi recapitati nello schema CloudEvents e nello schema Griglia di eventi sono gli stessi, ad eccezione di `content-type`. Per lo schema CloudEvents, tale valore intestazione è `"content-type":"application/cloudevents+json; charset=utf-8"`. Per lo schema Griglia di eventi, tale valore intestazione è `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Configurare Griglia di eventi per CloudEvents

È possibile usare Griglia di eventi sia per l'input che per l'output degli eventi nello schema CloudEvents. È possibile usare CloudEvents per gli eventi di sistema, ad esempio gli eventi di archiviazione BLOB, gli eventi dell'hub IoT e gli eventi personalizzati. Può anche trasformare tali eventi in transito in entrambe le direzioni.


| Schema di input       | Schema di output.
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato di Griglia di eventi  | Formato CloudEvents
| Formato CloudEvents | Formato di Griglia di eventi
| Formato di Griglia di eventi  | Formato di Griglia di eventi

Per tutti gli schemi di eventi, Griglia di eventi richiede la convalida quando si esegue la pubblicazione in un argomento di Griglia di eventi e quando si crea una sottoscrizione di eventi. Per altre informazioni, vedere [Event Grid security and authentication](security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

### <a name="input-schema"></a>Schema di input

Si imposta lo schema di input per un argomento personalizzato quando si crea l'argomento personalizzato.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Per PowerShell, usare:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

La versione corrente di CloudEvents non supporta l'invio in batch di eventi. Per pubblicare eventi con lo schema CloudEvent in un argomento, pubblicare singolarmente ogni evento.

### <a name="output-schema"></a>Schema di output.

Si imposta lo schema di output quando si crea la sottoscrizione dell'evento.

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Per PowerShell, usare:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

La versione corrente di CloudEvents non supporta l'invio in batch di eventi. Una sottoscrizione di eventi configurata per lo schema CloudEvent riceve singolarmente ogni evento. Attualmente non è possibile usare un trigger di Griglia di eventi per un'app di Funzioni di Azure quando l'evento viene recapitato nello schema CloudEvents. Usare un trigger HTTP. Per esempi di implementazione di un trigger HTTP che riceve gli eventi nello schema CloudEvents, vedere [Usare un trigger HTTP come trigger Griglia di eventi](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Microsoft invita gli utenti a testare, commentare e [contribuire](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) a CloudEvents.
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
