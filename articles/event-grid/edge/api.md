---
title: API REST - Azure Event Grid IoT Edge Documenti Microsoft
description: API REST su Griglia di eventi su Edge IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841830"
---
# <a name="rest-api"></a>API REST
Questo articolo descrive le API REST di Griglia di eventi di Azure in IoT Edge

## <a name="common-api-behavior"></a>Comportamento API comune

### <a name="base-url"></a>URL di base
Griglia di eventi in IoT Edge ha le seguenti API esposte su HTTP (porta 5888) e HTTPS (porta 4438).

* URL di base per HTTP:http://eventgridmodule:5888
* URL di base per HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Stringa di query della richiestaRequest query string
Tutte le richieste API richiedono il seguente parametro della stringa di query:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Tipo di contenuto della richiesta
Tutte le richieste API devono avere un **Content-Type**.

In caso di **EventGridSchema** o **CustomSchema**, il valore di Content-Type può essere uno dei valori seguenti:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Nel caso di **CloudEventSchemaV1_0** in modalità strutturata, il valore di Content-Type può essere uno dei seguenti valori:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

In caso di **CloudEventSchemaV1_0** in modalità binaria, fare riferimento alla [documentazione](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) per i dettagli.

### <a name="error-response"></a>Risposta di errore
Tutte le API restituiscono un errore con il seguente payload:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Gestire gli argomenti

### <a name="put-topic-create--update"></a>Mettere argomento (creare/ aggiornare)

**Richiesta**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Carico utile**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Risposta**: HTTP 200

**Carico utile**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Ottenere l'argomentoGet topic

**Richiesta**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200

**Carico utile**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Ottieni tutti gli argomenti

**Richiesta**:``` GET /topics?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200

**Carico utile**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Elimina argomento

**Richiesta**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200, payload vuoto

## <a name="manage-event-subscriptions"></a>Gestire le sottoscrizioni di eventi
Negli esempi di `EndpointType=Webhook;`questa sezione viene utilizzato . Gli esempi `EndpointType=EdgeHub / EndpointType=EventGrid` json per sono nella sezione successiva. 

### <a name="put-event-subscription-create--update"></a>Inserisci sottoscrizione all'evento (creazione/aggiornamento)

**Richiesta**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Carico utile**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Risposta**: HTTP 200

**Carico utile**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Ottenere la sottoscrizione di eventiGet event subscription

**Richiesta**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200

**Carico utile**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Ottenere sottoscrizioni di eventiGet event subscriptions

**Richiesta**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200

**Carico utile**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Elimina sottoscrizione evento

**Richiesta**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Risposta**: HTTP 200, nessun payload


## <a name="publish-events-api"></a>API degli eventi di pubblicazionePublish events API

### <a name="send-batch-of-events-in-event-grid-schema"></a>Invia batch di eventi (nello schema Griglia di eventi)

**Richiesta**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Risposta**: HTTP 200, payload vuoto


**Descrizioni dei campi del payload**
- ```Id```è obbligatorio. Può essere qualsiasi valore stringa popolato dal chiamante. Griglia di eventi NON esegue alcun rilevamento duplicati o applica alcuna semantica a questo campo.
- ```Topic```è facoltativo, ma se specificato deve corrispondere al topic_name dall'URL della richiesta
- ```Subject```è obbligatorio, può essere qualsiasi valore stringa
- ```EventType```è obbligatorio, può essere qualsiasi valore stringa
- ```EventTime```è obbligatorio, non è convalidato, ma deve essere un DateTime corretto.
- ```DataVersion``` è obbligatorio
- ```MetadataVersion```è facoltativo, se specificato DEVE essere una stringa con il valore```"1"```
- ```Data```è facoltativo e può essere qualsiasi token JSON (numero, stringa, booleano, matrice, oggetto)

### <a name="send-batch-of-events-in-custom-schema"></a>Invia batch di eventi (nello schema personalizzato)

**Richiesta**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Risposta**: HTTP 200, payload vuoto


**Restrizioni del carico utile**
- DEVE essere una matrice di eventi.
- Ogni voce di matrice DEVE essere un oggetto JSON.
- Nessun altro vincolo (diverso dalla dimensione del payload).

## <a name="examples"></a>Esempi

### <a name="set-up-topic-with-eventgrid-schema"></a>Impostare l'argomento con lo schema EventGridSet up topic with EventGrid schema
Imposta un argomento per richiedere la pubblicazione di eventi in **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Impostare l'argomento con uno schema personalizzatoSet up topic with custom schema
Imposta un argomento per richiedere la `customschema`pubblicazione di eventi in .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Configurare l'argomento con lo schema degli eventi cloudSet up topic with cloud event schema
Imposta un argomento per richiedere la `cloudeventschema`pubblicazione di eventi in .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Configurare WebHook come destinazione, eventi da recapitare in eventgridschema
Utilizzare questo tipo di destinazione per inviare eventi a qualsiasi altro modulo (che ospita un endpoint HTTP) o a qualsiasi endpoint indirizzabile HTTP nella rete/Internet.Use this destination type to send events to any other module (that hosts an HTTP endpoint) or to any HTTP addressable endpoint on the network/internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Vincoli `endpointUrl` sull'attributo:
- Deve essere non null.
- Deve essere un URL assoluto.
- Se outbound__webhook__httpsOnly è impostata su true nelle impostazioni di EventGridModule, deve essere solo HTTPS.
- Se outbound__webhook__httpsOnly impostato su false, può essere HTTP o HTTPS.

Vincoli `eventDeliverySchema` sulla proprietà:
- Deve corrispondere allo schema di input dell'argomento di sottoscrizione.
- Può essere Null. Per impostazione predefinita, viene fatto lo schema di input dell'argomento.

### <a name="set-up-iot-edge-as-destination"></a>Configurare IoT Edge come destinazione

Utilizzare questa destinazione per inviare eventi all'Hub Edge IoT ed essere sottoposti al sottosistema di routing/filtro/inoltro dell'hub perimetrale dell'hub perimetrale.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Configurare Event Grid Cloud come destinazione

Usare questa destinazione per inviare eventi a Griglia di eventi nel cloud (Azure). Prima di creare una sottoscrizione di eventi sul perimetro, è necessario configurare un argomento utente nel cloud a cui devono essere inviati gli eventi.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl:
- Deve essere non null.
- Deve essere un URL assoluto.
- Il `/api/events` percorso deve essere definito nel percorso dell'URL della richiesta.
- Deve essere `api-version=2018-01-01` presente nella stringa di query.
- Se outbound__eventgrid__httpsOnly è impostata su true nelle impostazioni EventGridModule (true per impostazione predefinita), deve essere solo HTTPS.
- Se outbound__eventgrid__httpsOnly è impostato su false, può essere HTTP o HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames è impostato su false (false per impostazione predefinita), deve essere destinato a uno dei seguenti endpoint:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve essere diverso da null.

NomeArgomento:
- Se Subscription.EventDeliverySchema è impostato su EventGridSchema, il valore di questo campo viene inserito nel campo Topic di ogni evento prima di essere inoltrato alla griglia di eventi nel cloud.
- Se Subscription.EventDeliverySchema è impostata su CustomEventSchema, questa proprietà viene ignorata e il payload dell'evento personalizzato viene inoltrato esattamente come è stato ricevuto.

## <a name="set-up-event-hubs-as-a-destination"></a>Configurare gli hub eventi come destinazione

Per pubblicare in un `endpointType` hub `eventHub` eventi, impostare l'opzione su e fornire:

* connectionString: stringa di connessione per l'hub eventi specifico di destinazione generato tramite criteri di accesso condiviso.connectionString: Connection string for the specific Event Hub you're targeting generated via a Shared Access Policy.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'utilizzo di una stringa di connessione dello spazio dei nomi non funzionerà. È possibile generare una stringa di connessione specifica dell'entità passando all'hub eventi specifico in cui si vuole pubblicare nel portale di Azure e facendo clic su **Criteri di accesso condiviso** per generare una nuova stringa di connessione specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configurare le code del bus di servizio come destinazione

Per pubblicare in una coda `endpointType` `serviceBusQueue` del bus di servizio, impostare l'opzione su e fornire:

* connectionString: stringa di connessione per la coda del bus di servizio specifica di destinazione generata tramite criteri di accesso condiviso.connectionString: Connection string for the specific Service Bus Queue you're targeting generated via a Shared Access Policy.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'utilizzo di una stringa di connessione dello spazio dei nomi non funzionerà. Generare una stringa di connessione specifica dell'entità passando alla coda del bus di servizio specifica in cui si vuole pubblicare nel portale di Azure e facendo clic su **Criteri di accesso condiviso** per generare una nuova stringa di connessione specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configurare gli argomenti del bus di servizio come destinazioneSet up Bus Topics as a destination

Per pubblicare in un argomento `endpointType` `serviceBusTopic` del bus di servizio, impostare l'opzione su e fornire:

* connectionString: stringa di connessione per l'argomento del bus di servizio specifico di destinazione generato tramite criteri di accesso condiviso.connectionString: Connection string for the specific Service Bus Topic you're targeting generated via a Shared Access Policy.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'utilizzo di una stringa di connessione dello spazio dei nomi non funzionerà. Generare una stringa di connessione specifica dell'entità passando all'argomento del bus di servizio specifico in cui si vuole pubblicare nel portale di Azure e facendo clic su **Criteri di accesso condiviso** per generare una nuova stringa di connessione specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Configurare le code di archiviazione come destinazioneSet up Storage Queues as a destination

Per pubblicare in una `endpointType` coda `storageQueue` di archiviazione, impostare l'opzione su e fornire:

* queueName: nome della coda di archiviazione in cui si sta pubblicando.
* connectionString: stringa di connessione per l'account di archiviazione in cui si trova la coda di archiviazione.connectionString: Connection string for the Storage Account the Storage Queue is in.

    >[!NOTE]
    > Hub eventi unline, code del bus di servizio e argomenti del bus di servizio, la stringa di connessione utilizzata per le code di archiviazione non è specifica dell'entità. Al contrario, deve fare la stringa di connessione per l'account di archiviazione.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```