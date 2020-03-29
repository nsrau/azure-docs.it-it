---
title: Inoltrare gli eventi della griglia di eventi a IoTHub - Azure Event Grid IoT Edge Documenti Microsoft
description: Inoltrare gli eventi della griglia di eventi a IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844701"
---
# <a name="tutorial-forward-events-to-iothub"></a>Esercitazione: Inoltrare eventi a IoTHubTutorial: Forward events to IoTHub

Questo articolo illustra tutti i passaggi necessari per inoltrare gli eventi griglia di eventi ad altri moduli IoT Edge, IoTHub usando le route. È possibile eseguire questa operazione per i motivi seguenti:You might want to do it for the following reasons:

* Continuare a utilizzare tutti gli investimenti esistenti già in atto con il routing di edgeHub
* Preferisci instradare tutti gli eventi da un dispositivo solo tramite l'hub IoTPrefer to route all events from a device only via IoT Hub

Per completare questa esercitazione, è necessario comprendere i concetti seguenti:To complete this tutorial, you need to understand the following concepts:

- [Concetti relativi alla griglia degli eventiEvent Grid Concepts](concepts.md)
- [Hub di IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione è necessario disporre degli elementi seguenti:

* Sottoscrizione di **Azure:** creare un [account gratuito,](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub IoT di Azure e dispositivo Edge IoT:** seguire i passaggi della guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows,](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Creare un argomento

In qualità di editore di un evento, devi creare un argomento della griglia degli eventi. L'argomento fa riferimento a un punto finale a cui gli editori possono quindi inviare eventi.

1. Creare topic4.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Eseguire il comando seguente per creare l'argomento. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Output di esempio:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Crea sottoscrizione di eventi

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, sarà necessario creare una sottoscrizione griglia eventi su un argomento di interesse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Creare subscription4.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > Il `endpointType` specifica che il `edgeHub`sottoscrittore è . Specifica `outputName` l'output in cui il modulo Griglia di eventi instrada gli eventi che corrispondono a questa sottoscrizione a edgeHub. Ad esempio, gli eventi che corrispondono `/messages/modules/eventgridmodule/outputs/sampleSub4`alla sottoscrizione precedente verranno scritti in .
2. Eseguire il comando seguente per creare la sottoscrizione. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Output di esempio:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Configurare un percorso hub perimetrale

Aggiornare la route dell'hub perimetrale per inoltrare gli eventi della sottoscrizione di eventi da inoltrare a IoTHub come segue:Update the edge hub's route to forward event subscription's event's event to be forwarded to IoTHub as follows:

1. Accedere al portale di [AzureSign](https://ms.portal.azure.com) in to the Azure portal
1. Passare **all'hub IoT**.
1. Selezionare **Bordo IoT** dal menu
1. Selezionare l'ID del dispositivo di destinazione dall'elenco dei dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).
1. Selezionare **Avanti** e nella sezione percorsi.
1. Nei percorsi, aggiungere un nuovo percorso

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Ad esempio,

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > La route precedente inoltrerà tutti gli eventi corrispondenti per questa sottoscrizione da inoltrare all'hub IoT. È possibile utilizzare le funzionalità di [routing dell'hub Edge](../../iot-edge/module-composition.md) per filtrare ulteriormente e instradare gli eventi della griglia di eventi ad altri moduli IoT Edge.

## <a name="setup-iot-hub-route"></a>Configurare il percorso dell'hub IoT

Vedere [l'esercitazione sul routing dell'hub IoT](../../iot-hub/tutorial-routing.md) per configurare una route dall'hub IoT in modo da poter visualizzare gli eventi inoltrati dal modulo Griglia di eventi. Utilizzare `true` per la query per mantenere semplice l'esercitazione.  



## <a name="publish-an-event"></a>Pubblicare un evento

1. Creare event4.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Eseguire il comando seguente per pubblicare l'evento:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verificare il recapito degli eventi

Vedere [l'esercitazione](../../iot-hub/tutorial-routing.md) sul routing dell'hub IoT per la procedura di visualizzazione degli eventi.

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni sul perimetro:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Eliminare tutte le risorse create durante la configurazione del routing IoTHub anche nel cloud.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un argomento della griglia di eventi, una sottoscrizione hub edge e eventi pubblicati. Dopo aver conosciuto i passaggi di base per l'inoltro a un hub perimetrale, vedere gli articoli seguenti:Now that you know the basic steps to forward to an edge hub, see the following articles:

* Per risolvere i problemi relativi all'uso di Griglia di eventi di Azure in Edge Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
* Usare i filtri route [hub perimetrali](../../iot-edge/module-composition.md) per partizionare gli eventiUse edge hub route filters to partition events
* Impostare la persistenza del modulo Griglia di eventi in linux o [WindowsSet](persist-state-windows.md) up persistence of Event Grid module on [linux](persist-state-linux.md) or Windows
* Seguire la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
* Inoltrare gli eventi a Griglia di eventi di Azure nel cloud seguendo questa [esercitazione](forward-events-event-grid-cloud.md)
* [Monitorare argomenti e sottoscrizioni sul bordo](monitor-topics-subscriptions.md)