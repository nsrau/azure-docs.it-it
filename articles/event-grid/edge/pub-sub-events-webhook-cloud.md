---
title: Pubblicare, sottoscrivere gli eventi nel cloud - Azure Event Grid IoT Edge Documenti Microsoft
description: Pubblicare, sottoscrivere eventi nel cloud usando Webhook con Griglia di eventi in Edge IoT
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844591"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Esercitazione: Pubblicare, sottoscrivere eventi nel cloudTutorial: Publish, subscribe to events in cloud

Questo articolo illustra tutti i passaggi necessari per pubblicare e sottoscrivere eventi usando Griglia di eventi in Edge IoT.This article walks through all the steps needed to publish and subscribe to events using Event Grid on IoT Edge. Questa esercitazione usa e Funzione di Azure come gestore eventi. Per altri tipi di destinazione, vedere [gestori eventi](event-handlers.md).

Vedere Concetti relativi alla [griglia](concepts.md) di eventi per comprendere cosa sono un argomento della griglia di eventi e una sottoscrizione prima di procedere.

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione è necessario disporre degli elementi seguenti:

* Sottoscrizione di **Azure:** creare un [account gratuito,](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub IoT di Azure e dispositivo Edge IoT:** seguire i passaggi della guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows,](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Creare una funzione di Azure nel portale di AzureCreate an Azure function in the Azure portal

Seguire i passaggi descritti [nell'esercitazione](../../azure-functions/functions-create-first-azure-function.md) per creare una funzione di Azure.Follow the steps outlined in the tutorial to create an Azure function. 

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

Nella nuova funzione selezionare **Ottieni URL funzione** in alto a destra, selezionare default ( Tasto**funzione**), quindi scegliere **Copia**. Si userà il valore dell'URL della funzione più avanti nell'esercitazione.

> [!NOTE]
> Fare riferimento alla documentazione di Funzioni di [Azure](../../azure-functions/functions-overview.md) per altri esempi ed esercitazioni sulla reazione agli eventi e ai trigger di evento EventGrid.

## <a name="create-a-topic"></a>Creare un argomento

In qualità di editore di un evento, devi creare un argomento della griglia degli eventi. Argomento si riferisce a un punto finale in cui gli editori possono inviare eventi.

1. Creare topic2.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Eseguire il comando seguente per creare l'argomento. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

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

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, i sottoscrittori dovranno creare una sottoscrizione griglia di eventi su un argomento di interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Creare subscription2.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

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
   > **EndpointType** specifica che il sottoscrittore è un Webhook.  **EndpointUrl** specifica l'URL in cui il sottoscrittore è in ascolto di eventi. Questo URL corrisponde all'esempio di funzione di Azure configurato in precedenza.
2. Eseguire il comando seguente per creare la sottoscrizione. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

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

1. Creare event2.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

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

È possibile visualizzare l'evento recapitato nel portale di Azure nell'opzione **Monitor** della funzione.

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Eliminare la funzione di Azure creata nel portale di Azure.Delete the Azure function created in the Azure portal.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un argomento della griglia di eventi, una sottoscrizione e gli eventi pubblicati. Dopo aver conosciuto i passaggi di base, vedere gli articoli seguenti:Now that you know the basic steps, see the following articles:

* Per risolvere i problemi relativi all'uso di Griglia di eventi di Azure in Edge Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
* Crea/aggiorna sottoscrizione con [filtri](advanced-filtering.md).
* Impostare la persistenza del modulo Griglia di eventi in linux o [WindowsSet](persist-state-windows.md) up persistence of Event Grid module on [linux](persist-state-linux.md) or Windows
* Seguire la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
* Inoltrare gli eventi a Griglia di eventi di Azure nel cloud seguendo questa [esercitazione](forward-events-event-grid-cloud.md)
* [Monitorare argomenti e sottoscrizioni sul bordo](monitor-topics-subscriptions.md)
