---
title: 'Pubblicare, sottoscrivere gli eventi nel cloud: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Pubblicare, sottoscrivere gli eventi nel cloud usando webhook con griglia di eventi in IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 86c0fe8b0846350f74c90dfbd229510d28ce9499
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992223"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Esercitazione: pubblicare, sottoscrivere eventi nel cloud

Questo articolo illustra tutti i passaggi necessari per pubblicare e sottoscrivere gli eventi usando griglia di eventi in IoT Edge.

Vedere [concetti relativi a griglia di eventi](concepts.md) per comprendere l'argomento e la sottoscrizione di griglia di eventi prima di procedere.

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione, è necessario:

* **Sottoscrizione di Azure** : creare un [account gratuito](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub Azure e dispositivo IOT Edge** : seguire la procedura descritta nella Guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Creare una funzione di Azure nella portale di Azure

Seguire i passaggi descritti nell' [esercitazione](../../azure-functions/functions-create-first-azure-function.md) per creare una funzione di Azure. 

Sostituire il frammento di codice con il codice seguente:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

Nella nuova funzione selezionare **Ottieni URL funzione** in alto a destra, selezionare predefinito (**chiave funzione**), quindi selezionare **copia**. Il valore di URL funzione sarà usato più avanti nell'esercitazione.

> [!NOTE]
> Vedere la documentazione di [funzioni di Azure](../../azure-functions/functions-overview.md) per altri esempi ed esercitazioni sulla reazione agli eventi usando i trigger di evento EventGrid.

## <a name="create-a-topic"></a>Creare un argomento

Come server di pubblicazione di un evento, è necessario creare un argomento di griglia di eventi. L'argomento si riferisce a un endpoint in cui i publisher possono inviare eventi a.

1. Creare topic2. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Eseguire il comando seguente per creare l'argomento. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Output di esempio:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Creare una sottoscrizione di eventi

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, i sottoscrittori dovranno creare una sottoscrizione di griglia di eventi in un argomento di interesse.

1. Creare subscription2. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **EndpointType** specifica che il Sottoscrittore è un webhook.  **EndpointUrl** specifica l'URL in cui il Sottoscrittore è in ascolto di eventi. Questo URL corrisponde all'esempio di funzione di Azure configurato in precedenza.
2. Eseguire il comando seguente per creare la sottoscrizione. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Output di esempio:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Pubblicare un evento

1. Creare EVENT2. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Eseguire il comando seguente per pubblicare l'evento

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificare il recapito degli eventi

È possibile visualizzare l'evento recapitato nell'portale di Azure sotto l'opzione **monitoraggio** della funzione.

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Eliminare la funzione di Azure creata nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un argomento di griglia di eventi, una sottoscrizione e gli eventi pubblicati. Ora che si conoscono i passaggi di base, vedere gli articoli seguenti:

* Crea/aggiorna la sottoscrizione con i [filtri](advanced-filtering.md).
* Configurare la persistenza del modulo di griglia di eventi in [Linux](persist-state-linux.md) o [Windows](persist-state-windows.md)
* Segui la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
* Inviare eventi a griglia di eventi di Azure nel cloud seguendo questa [esercitazione](forward-events-event-grid-cloud.md)
