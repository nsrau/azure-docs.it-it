---
title: Inviare eventi di griglia di eventi a IoTHub-griglia di eventi di Azure IoT Edge | Microsoft Docs
description: Inoltri eventi griglia di eventi a IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 073205b5bdc3f6de80bd7e347469c3f06aeb515b
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73098655"
---
# <a name="tutorial-forward-events-to-iothub"></a>Esercitazione: inviare eventi a IoTHub

Questo articolo illustra tutti i passaggi necessari per inoltrare gli eventi di griglia di eventi ad altri moduli IoT Edge, IoTHub usando le route. Potrebbe essere necessario eseguire questa operazione per i motivi seguenti:

* Continua a usare gli investimenti esistenti già presenti con il routing di edgeHub
* Preferisci indirizzare tutti gli eventi da un dispositivo solo tramite l'hub Internet

Per completare questa esercitazione, è necessario comprendere i concetti seguenti:

- [Concetti relativi a Griglia di eventi](concepts.md)
- [Hub IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione, è necessario:

* **Sottoscrizione di Azure** : creare un [account gratuito](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub Azure e dispositivo IOT Edge** : seguire la procedura descritta nella Guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Creare un argomento

Come server di pubblicazione di un evento, è necessario creare un argomento di griglia di eventi. L'argomento si riferisce a un endpoint in cui i publisher possono inviare eventi a.

1. Creare topic4. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Eseguire il comando seguente per creare l'argomento. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Viene restituito il codice di stato HTTP 200 OK.

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

## <a name="create-event-subscription"></a>Crea sottoscrizione evento

I sottoscrittori possono registrarsi per gli eventi pubblicati in un argomento. Per ricevere qualsiasi evento, sarà necessario creare una sottoscrizione di griglia di eventi in un argomento di interesse.

1. Creare subscription4. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

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
   > Il `endpointType` specifica che il Sottoscrittore è `edgeHub`. Il `outputName` specifica l'output in base al quale il modulo di griglia di eventi instraderà gli eventi che corrispondono a questa sottoscrizione a edgeHub. Ad esempio, gli eventi che corrispondono alla sottoscrizione precedente verranno scritti in `/messages/modules/eventgridmodule/outputs/sampleSub4`.
2. Eseguire il comando seguente per creare la sottoscrizione. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Viene restituito il codice di stato HTTP 200 OK.

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

## <a name="set-up-an-edge-hub-route"></a>Configurare una route dell'hub perimetrale

Aggiornare la route dell'hub perimetrale per inoltrare gli eventi della sottoscrizione di eventi da inoltrare a IoTHub nel modo seguente:

1. Accedere al [portale di Azure](https://ms.portal.azure.com)
1. Passare all' **Hub**Internet delle cose.
1. Selezionare **IOT Edge** dal menu
1. Selezionare l'ID del dispositivo di destinazione dall'elenco di dispositivi.
1. Selezionare **Set Modules** (Configura i moduli).
1. Selezionare **Avanti** e nella sezione route.
1. In Route aggiungere una nuova route

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
   > La route precedente consente di inoltrare gli eventi corrispondenti per la sottoscrizione da inoltrare all'hub Internet delle cose. È possibile usare le funzionalità di [routing dell'Hub Edge](../../iot-edge/module-composition.md) per filtrare ulteriormente e indirizzare gli eventi di griglia di eventi ad altri moduli IOT Edge.

## <a name="setup-iot-hub-route"></a>Configurare la route dell'hub Internet

Vedere l' [esercitazione](../../iot-hub/tutorial-routing.md) relativa al routing dell'hub Internet per la configurazione di una route dall'hub Internet per visualizzare gli eventi inoltrati dal modulo di griglia di eventi. Usare `true` per la query per semplificare l'esercitazione.  



## <a name="publish-an-event"></a>Pubblicare un evento

1. Creare event4. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

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

Per la procedura per visualizzare gli eventi, vedere l' [esercitazione sul routing](../../iot-hub/tutorial-routing.md) dell'hub Internet.

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni al perimetro:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Eliminare tutte le risorse create durante la configurazione del routing IoTHub anche nel cloud.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un argomento di griglia di eventi, una sottoscrizione dell'Hub Edge e eventi pubblicati. Ora che si conoscono i passaggi di base per l'invio a un Hub Edge, vedere gli articoli seguenti:

* Per risolvere i problemi relativi all'uso di griglia di eventi di Azure in IoT Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
* Usare i filtri di route dell' [Hub Edge](../../iot-edge/module-composition.md) per suddividere gli eventi
* Configurare la persistenza del modulo di griglia di eventi in [Linux](persist-state-linux.md) o [Windows](persist-state-windows.md)
* Segui la [documentazione](configure-client-auth.md) per configurare l'autenticazione client
* Inviare eventi a griglia di eventi di Azure nel cloud seguendo questa [esercitazione](forward-events-event-grid-cloud.md)
