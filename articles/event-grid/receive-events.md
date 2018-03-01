---
title: Ricevere eventi dalla griglia di eventi di Azure in un endpoint HTTP
description: Viene descritto come convalidare un endpoint HTTP, quindi ricevere e deserializzare gli eventi dalla griglia di eventi di Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: article
ms.date: 02/16/2018
ms.author: babanisa
ms.openlocfilehash: da4d1aff45c39d8b4ce7cdd003dd4af35dcd33b3
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="receive-events-to-an-http-endpoint"></a>Ricevere eventi in un endpoint HTTP

Questo articolo descrive come [convalidare un endpoint HTTP](security-authentication.md#webhook-event-delivery) per ricevere eventi dalla sottoscrizione di un evento e quindi ricevere e deserializzare gli eventi. Questo articolo usa una funzione di Azure a scopo dimostrativo, ma gli stessi concetti si applicano indipendentemente da dove è ospitata l'applicazione.

> [!NOTE]
> Si consiglia **vivamente** l'uso di un [Trigger griglia di eventi](../azure-functions/functions-bindings-event-grid.md) quando si attiva una funzione di Azure con la griglia di eventi. L'uso di un trigger generico WebHook qui è dimostrativo.

## <a name="prerequisites"></a>prerequisiti

* È stata creata un'app per le funzioni con una [funzione attivata tramite HTTP](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Aggiungere le dipendenze

Se si sviluppa in .Net, [aggiungere una dipendenza](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) alla funzione per il `Microsoft.Azure.EventGrid` [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Gli SDK per le altre lingue sono disponibili tramite il riferimento agli [SDK di pubblicazione](./sdk-overview.md#publish-sdks). Questi pacchetti contengono i modelli per i tipi di evento nativo, ad esempio `EventGridEvent`, `StorageBlobCreatedEventData` e `EventHubCaptureFileCreatedEventData`.

A tale scopo, fare clic sul collegamento "View Files" (Visualizza file) nella Funzione di Azure (riquadro più a destra nel portale delle funzioni di Azure) e creare un file denominato project.json. Aggiungere il contenuto seguente al file `project.json` e salvarlo: 
 
 ```json
{ 
  "frameworks": { 
    "net46":{ 
      "dependencies": { 
        "Microsoft.Azure.EventGrid": "1.1.0-preview" 
      } 
    } 
   } 
} 
```

![Aggiunta del pacchetto NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Convalida degli endpoint

La prima cosa si intende fare è gestire gli eventi `Microsoft.EventGrid.SubscriptionValidationEvent`. Ogni volta che viene creata una nuova sottoscrizione evento, la griglia di eventi di Azure invia un evento di convalida all'endpoint con un `validationCode` nel payload dei dati. L'endpoint è tenuto a ripeterlo nel corpo della risposta per [dimostrare che l'endpoint è valido e di proprietà dell'utente](security-authentication.md#webhook-event-delivery). Se si usa un [Trigger griglia di eventi di Azure](../azure-functions/functions-bindings-event-grid.md) anziché una funzione attivata da WebHook, la convalida dell'endpoint viene gestita dall'utente.

Il codice seguente consente di gestire la convalida della sottoscrizione:

```csharp
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 

    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent"; 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }             
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
} 

```

```javascript
var http = require('http');

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

È ora possibile estendere la funzione per gestire `Microsoft.Storage.BlobCreated`:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");  
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }    
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');

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

È inoltre possibile verificarlo in tempo reale creando un account di archiviazione BLOB o un account di archiviazione General Purpose V2 (GPv2), [aggiungendo una sottoscrizione evento](../storage/blobs/storage-blob-event-quickstart.md) e impostando l'endpoint sull'URL della funzione:

![URL della funzione](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Gestione di eventi personalizzati

Infine estendiamo la funzione ancora una volta in modo che possa gestire anche gli eventi personalizzati. Aggiungiamo un controllo per il nostro evento `Contoso.Items.ItemReceived`. Il codice finale avrà un aspetto simile al seguente:

```cs
using System.Net; 
using Newtonsoft.Json; 
using Newtonsoft.Json.Linq; 
using Newtonsoft.Json.Serialization; 
using Microsoft.Azure.EventGrid.Models; 
 
class SubscriptionValidationEventData 
{ 
    public string ValidationCode { get; set; } 
} 
 
class SubscriptionValidationResponseData 
{ 
    public string ValidationResponse { get; set; } 
} 

class ContosoItemReceivedEventData 
{ 
    public string id { get; set; }     
    public string message { get; set; }   
    public string time { get; set; }
} 
 
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log) 
{ 
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty; 
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated"; 
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";
 
 
    string requestContent = await req.Content.ReadAsStringAsync(); 
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent); 
 
    foreach (EventGridEvent eventGridEvent in eventGridEvents) 
    { 
        JObject dataObject = eventGridEvent.Data as JObject; 
       
        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>(); 
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}"); 
            // Do any additional validation (as required) and then return back the below response 
            var responseData = new SubscriptionValidationResponseData(); 
            responseData.ValidationResponse = eventData.ValidationCode; 
            return req.CreateResponse(HttpStatusCode.OK, responseData);    
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>(); 
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}"); 
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase)) 
        { 
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>(); 
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}"); 
        } 
    } 
     
    return req.CreateResponse(HttpStatusCode.OK, response);     
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

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
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Test della gestione degli eventi personalizzati

Infine verificare che la funzione estesa possa ora gestire il tipo di evento personalizzato:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
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
