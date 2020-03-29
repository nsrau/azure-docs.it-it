---
title: Inoltrare gli eventi edge al cloud griglia eventi - Azure Event Grid IoT Edge Documenti Microsoft
description: Inoltrare eventi perimetrali al cloud della griglia di eventi
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844718"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Esercitazione: Inoltrare gli eventi al cloud della griglia di eventiTutorial: Forward events to Event Grid cloud

Questo articolo illustra tutti i passaggi necessari per inoltrare gli eventi Edge a Griglia di eventi nel cloud di Azure.This article walks through all the steps needed to forward edge events to Event Grid in the Azure cloud. È possibile eseguire questa operazione per i motivi seguenti:You might want to do it for the following reasons:

* Reagisci agli eventi Edge nel cloud.
* Inoltrare gli eventi alla griglia di eventi nel cloud e usare hub eventi di Azure o code di Archiviazione di Azure per memorizzare nel buffer gli eventi prima di elaborarli nel cloud.

 Per completare questa esercitazione, è necessario conoscere i concetti di Griglia di eventi in [Edge](concepts.md) e [Azure.](../concepts.md) Per altri tipi di destinazione, vedere [gestori eventi](event-handlers.md). 

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione è necessario disporre degli elementi seguenti:

* Sottoscrizione di **Azure:** creare un [account gratuito,](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub IoT di Azure e dispositivo Edge IoT:** seguire i passaggi della guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows,](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Creare l'argomento della griglia di eventi e la sottoscrizione nel cloudCreate event grid topic and subscription in cloud

Creare un argomento della griglia di eventi e una sottoscrizione nel cloud seguendo [questa esercitazione.](../custom-event-quickstart-portal.md) `topicURL`Annotare `sasKey`, `topicName` , e dell'argomento appena creato che verrà utilizzato più avanti nell'esercitazione.

Ad esempio, se è `testegcloudtopic` stato creato un argomento denominato Stati Uniti occidentali, i valori saranno simili ai seguenti:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **NomeArgomento**:`testegcloudtopic`
* **SasKey**: Disponibile in **AccessKey** dell'argomento. Utilizzare **key1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Creare un argomento della griglia di eventi sul bordo

1. Creare topic3.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Eseguire il comando seguente per creare l'argomento. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Output di esempio:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Creare una sottoscrizione griglia di eventi sul perimetroCreate Event Grid subscription at the edge

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Creare subscription3.json con il contenuto seguente. Consulta la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **EndpointUrl** specifica che l'URL dell'argomento Griglia di eventi nel cloud. **SasKey** fa riferimento alla chiave dell'argomento cloud della griglia di eventi. Il valore in **topicName** verrà utilizzato per visualizzare tutti gli eventi in uscita in Griglia di eventi. Ciò può essere utile quando si pubblica in un argomento di dominio Griglia di eventi. Per ulteriori informazioni sull'argomento del dominio Griglia di eventi, vedere [Domini di](../event-domains.md) eventi

    Ad esempio,
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Eseguire il comando seguente per creare la sottoscrizione. Deve essere restituito il codice di stato HTTP 200 OK.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Deve essere restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Output di esempio:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Pubblicare un evento sul bordo

1. Creare event3.json con il contenuto seguente. Vedere la [documentazione dell'API](api.md) per informazioni dettagliate sul payload.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Eseguire il comando seguente:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Verificare l'evento edge nel cloudVerify edge event in cloud

Per informazioni sulla visualizzazione degli eventi recapitati dall'argomento cloud, vedere [l'esercitazione](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Eliminare anche gli argomenti e le sottoscrizioni creati nel cloud (Griglia di eventi di Azure).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato pubblicato un evento sul perimetro e inoltrato a Griglia di eventi nel cloud di Azure.In this tutorial, you published an event on the edge and forwarded to Event Grid in the Azure cloud. Ora che si conoscono i passaggi di base per l'inoltro a Griglia di eventi nel cloud:Now that you know the basic steps to forward to Event Grid in cloud:

* Per risolvere i problemi relativi all'uso di Griglia di eventi di Azure in Edge Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
* Inoltrare gli eventi a IoTHub seguendo questa [esercitazione](forward-events-iothub.md)
* Inoltrare gli eventi a Webhook nel cloud seguendo questa [esercitazione](pub-sub-events-webhook-cloud.md)
* [Monitorare argomenti e sottoscrizioni sul bordo](monitor-topics-subscriptions.md)
