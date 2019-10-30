---
title: Inviare eventi Edge al cloud di griglia di eventi-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Inviare eventi Edge al cloud di griglia di eventi
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 502a495bad4115daf9f0f4ffed276a307adf1fc4
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100644"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Esercitazione: eseguire la trasmissione di eventi al cloud di griglia di eventi

Questo articolo illustra tutti i passaggi necessari per l'invio di eventi Edge a griglia di eventi nel cloud di Azure. Potrebbe essere necessario eseguire questa operazione per i motivi seguenti:

* Reagire agli eventi Edge nel cloud.
* Consente di inviare eventi a griglia di eventi nel cloud e di usare hub eventi di Azure o le code di archiviazione di Azure per memorizzare nel buffer gli eventi prima di elaborarli nel cloud.

Per completare questa esercitazione, è necessario conoscere i concetti relativi a griglia di eventi in [Edge](concepts.md) e [Azure](../concepts.md).

## <a name="prerequisites"></a>Prerequisiti 
Per completare questa esercitazione, è necessario:

* **Sottoscrizione di Azure** : creare un [account gratuito](https://azure.microsoft.com/free) se non ne è già disponibile uno. 
* **Hub Azure e dispositivo IOT Edge** : seguire la procedura descritta nella Guida introduttiva per i dispositivi [Linux](../../iot-edge/quickstart-linux.md) o [Windows](../../iot-edge/quickstart.md) se non ne è già presente uno.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Creare un argomento e una sottoscrizione di griglia di eventi nel cloud

Creare un argomento e una sottoscrizione di griglia di eventi nel cloud seguendo [questa esercitazione](../custom-event-quickstart-portal.md). Prendere nota di `topicURL`, `sasKey`e `topicName` dell'argomento appena creato che verrà usato più avanti nell'esercitazione.

Se, ad esempio, è stato creato un argomento denominato `testegcloudtopic` negli Stati Uniti occidentali, i valori avranno un aspetto simile al seguente:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **SasKey**: disponibile in **AccessKey** dell'argomento. Usare **Key1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Creare un argomento di griglia di eventi al perimetro

1. Creare topic3. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Eseguire il comando seguente per creare l'argomento. Viene restituito il codice di stato HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Eseguire il comando seguente per verificare che l'argomento sia stato creato correttamente. Viene restituito il codice di stato HTTP 200 OK.

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Creare una sottoscrizione di griglia di eventi al perimetro


1. Creare subscription3. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

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
   > **EndpointUrl** specifica che l'URL dell'argomento della griglia di eventi nel cloud. **SasKey** fa riferimento alla chiave dell'argomento cloud di griglia di eventi. Il valore in **topicName** verrà usato per contrassegnare tutti gli eventi in uscita in griglia di eventi. Questa operazione può essere utile quando si esegue la pubblicazione in un argomento di dominio di griglia di eventi. Per ulteriori informazioni sull'argomento relativo al dominio di griglia di eventi, vedere [domini eventi](../event-domains.md)

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

2. Eseguire il comando seguente per creare la sottoscrizione. Viene restituito il codice di stato HTTP 200 OK.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Eseguire il comando seguente per verificare che la sottoscrizione sia stata creata correttamente. Viene restituito il codice di stato HTTP 200 OK.

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

## <a name="publish-an-event-at-the-edge"></a>Pubblicare un evento alla periferia

1. Creare event3. JSON con il contenuto seguente. Per informazioni dettagliate sul payload, vedere la [documentazione dell'API](api.md) .

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

## <a name="verify-edge-event-in-cloud"></a>Verificare l'evento Edge nel cloud

Per informazioni sulla visualizzazione degli eventi forniti dall'argomento cloud, vedere l' [esercitazione](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Risorse di pulizia

* Eseguire il comando seguente per eliminare l'argomento e tutte le relative sottoscrizioni

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Eliminare anche l'argomento e le sottoscrizioni create nel cloud (griglia di eventi di Azure).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato pubblicato un evento sul perimetro ed è stato inviato a griglia di eventi nel cloud di Azure. Ora che si conoscono i passaggi di base per l'invio a griglia di eventi nel cloud:

* Per risolvere i problemi relativi all'uso di griglia di eventi di Azure in IoT Edge, vedere [Guida alla risoluzione dei problemi](troubleshoot.md).
* Eseguire il provisioning di eventi in IoTHub seguendo questa [esercitazione](forward-events-iothub.md)
* Inviare gli eventi al webhook nel cloud seguendo questa [esercitazione](pub-sub-events-webhook-cloud.md)
