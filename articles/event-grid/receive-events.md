---
title: Ricevere eventi dalla griglia di eventi di Azure in un endpoint HTTP
description: Viene descritto come convalidare un endpoint HTTP, quindi ricevere e deserializzare gli eventi dalla griglia di eventi di Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728623"
---
# <a name="receive-events-to-an-http-endpoint"></a>Ricevere eventi in un endpoint HTTP

Questo articolo descrive come [convalidare un endpoint HTTP](security-authentication.md#webhook-event-delivery) per ricevere eventi dalla sottoscrizione di un evento e quindi ricevere e deserializzare gli eventi. Questo articolo usa una funzione di Azure a scopo dimostrativo, ma gli stessi concetti si applicano indipendentemente da dove è ospitata l'applicazione.

> [!NOTE]
> Si consiglia **vivamente** l'uso di un [Trigger griglia di eventi](../azure-functions/functions-bindings-event-grid.md) quando si attiva una funzione di Azure con la griglia di eventi. L'uso di un trigger generico WebHook qui è dimostrativo.

## <a name="prerequisites"></a>Prerequisiti

È necessaria un'app per le funzioni con una funzione attivata tramite HTTP.

## <a name="add-dependencies"></a>Aggiungere le dipendenze

Se si sviluppa in .NET, [aggiungere una dipendenza](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) alla funzione per il [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) `Microsoft.Azure.EventGrid`. Gli esempi di questo articolo richiedono la versione 1.4.0 o successiva.

Gli SDK per le altre lingue sono disponibili tramite il riferimento agli [SDK di pubblicazione](./sdk-overview.md#data-plane-sdks). Questi pacchetti dispongono di modelli per i tipi di evento nativo, ad esempio `EventGridEvent`, `StorageBlobCreatedEventData` e `EventHubCaptureFileCreatedEventData`.

Fare clic sul collegamento "Visualizza file" nella funzione di Azure (riquadro più a destra nel portale delle funzioni di Azure) e creare un file denominato project.json. Aggiungere il contenuto seguente al file `project.json` e salvarlo:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Aggiunta del pacchetto NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Convalida degli endpoint

La prima cosa da fare è gestire gli eventi `Microsoft.EventGrid.SubscriptionValidationEvent`. Ogni volta che qualcuno sottoscrive un evento, Griglia di eventi invia un evento di convalida all'endpoint con un `validationCode` nel payload dei dati. L'endpoint è tenuto a ripeterlo nel corpo della risposta per [dimostrare che l'endpoint è valido e di proprietà dell'utente](security-authentication.md#webhook-event-delivery). Se si usa un [Trigger griglia di eventi di Azure](../azure-functions/functions-bindings-event-grid.md) anziché una funzione attivata da WebHook, la convalida dell'endpoint viene gestita dall'utente. Se si usa un servizio API di terze parti, ad esempio [Zapier](https://zapier.com) o [IFTTT](https://ifttt.com/), potrebbe non essere possibile ripetere a livello di programmazione il codice di convalida. Per questi servizi, è possibile convalidare manualmente la sottoscrizione usando un URL di convalida che viene inviato quando si verifica l'evento di convalida della sottoscrizione. Copiare l'URL nella proprietà `validationUrl` e inviare una richiesta GET tramite un client REST o un Web browser.

In C# la funzione `DeserializeEventGridEvents()` deserializza gli eventi di Griglia di eventi. Deserializza i dati dell'evento nel tipo appropriato, ad esempio StorageBlobCreatedEventData. Usare la classe `Microsoft.Azure.EventGrid.EventTypes` per ottenere i nomi e tipi di evento supportati.

Per ripetere a livello di programmazione il codice di convalida, usare il codice seguente. È possibile trovare esempi correlati nell'[Esempio di consumer di Griglia di eventi](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Test della risposta di convalida

Testare la funzione di risposta della convalida incollando l'evento di esempio nel campo di test per la funzione:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Quando si fa clic su Run (Esegui), l'Output deve essere 200 OK e `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` nel corpo:

![risposta di convalida](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Gestire gli eventi di archiviazione BLOB

Verrà ora estesa la funzione per gestire `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Testare la gestione di un evento creato da un BLOB

Verificare la nuova funzionalità della funzione inserendo un [evento di archiviazione BLOB](./event-schema-blob-storage.md#example-event) nel campo di test ed eseguendo:

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
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Si dovrebbe vedere l'output dell'URL BLOB nel log delle funzioni:

![Log di output](./media/receive-events/blob-event-response.png)

È anche possibile verificarlo creando un account di archiviazione BLOB o un account di archiviazione General Purpose V2 (GPv2), [aggiungendo una sottoscrizione evento](../storage/blobs/storage-blob-event-quickstart.md) e impostando l'endpoint sull'URL della funzione:

![URL della funzione](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Gestione di eventi personalizzati

Infine estendiamo la funzione ancora una volta in modo che possa gestire anche gli eventi personalizzati. 

In C# l'SDK supporta il mapping di un nome tipo di evento per il tipo di dati evento. Usare la funzione `AddOrUpdateCustomEventMapping()` per eseguire il mapping dell'evento personalizzato.

Aggiungere un controllo per l'evento `Contoso.Items.ItemReceived`. Il codice finale avrà un aspetto simile al seguente:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Test della gestione degli eventi personalizzati

Infine verificare che la funzione possa ora gestire il tipo di evento personalizzato:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

È anche possibile testare questa funzionalità in tempo reale [inviando un evento personalizzato con CURL dal portale](./custom-event-quickstart-portal.md) o [registrando un argomento personalizzato](./post-to-custom-topic.md) usando qualsiasi servizio o applicazione in grado di REGISTRARE un endpoint, ad esempio [Postman](https://www.getpostman.com/). Creare un argomento personalizzato e una sottoscrizione di eventi con l'endpoint impostato come URL della funzione.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare gli [SDK Griglia di eventi per la gestione e la pubblicazione](./sdk-overview.md)
* Informazioni su come [registrare un argomento personalizzato](./post-to-custom-topic.md)
* Provare una delle esercitazioni dettagliate della griglia di eventi di Azure e delle funzioni, ad esempio il [ridimensionamento delle immagini caricate nell'archiviazione BLOB](resize-images-on-storage-blob-upload-event.md)
