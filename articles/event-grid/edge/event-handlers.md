---
title: Gestori di eventi e destinazioni - Azure Event Grid IoT Edge Documenti Microsoft
description: Event Handlers and destinations in Event Grid on Edge
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849747"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Event Handlers and destinations in Event Grid on Edge

Un gestore eventi è la posizione in cui l'evento per ulteriori azioni o per elaborare l'evento. Con il modulo Griglia di eventi in Edge, il gestore eventi può trovarsi sullo stesso dispositivo perimetrale, in un altro dispositivo o nel cloud. È possibile usare qualsiasi WebHook per gestire gli eventi o inviare eventi a uno dei gestori nativi come Griglia di eventi di Azure.You can use any WebHook to handle events, or send events to one of the native handlers like Azure Event Grid.

In questo articolo vengono fornite informazioni su come configurare ciascuno di essi.

## <a name="webhook"></a>WebHook

Per pubblicare in un endpoint `endpointType` `WebHook` WebHook, impostare l'oggetto su e fornire:

* endpointUrl: URL dell'endpoint WebHook

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Griglia di eventi di Azure

Per pubblicare in un endpoint cloud `endpointType` `eventGrid` di Griglia di eventi di Azure, impostare l'endpoint su e fornire:To publish to an Azure Event Grid cloud endpoint, set the to and provide:

* endpointUrl: URL dell'argomento della griglia degli eventi nel cloud
* sasKey: chiave di accesso della porta di accesso a la data di accesso a la dall'ora dell'argomento della griglia degli eventiSasKey: Event Grid Topic's S
* topicName: nome per l'elenco di tutti gli eventi in uscita in Griglia di eventi. Il nome dell'argomento è utile quando si pubblica un argomento Dominio griglia eventi.

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

## <a name="iot-edge-hub"></a>IoT Edge Hub

Per pubblicare in un modulo `endpointType` `edgeHub` Hub Edge, impostare l'opzione su e fornire:

* outputName: l'output in cui il modulo Griglia di eventi instrada gli eventi che corrispondono a questa sottoscrizione a edgeHub.outputName: The output on which the Event Grid module will route events that match this subscription to edgeHub. Ad esempio, gli eventi che corrispondono alla sottoscrizione seguente verranno scritti in /messages/modules/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Hub eventi

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

## <a name="service-bus-queues"></a>Code del bus di servizio

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

## <a name="service-bus-topics"></a>Argomenti del bus di servizio

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

## <a name="storage-queues"></a>Code di archiviazione

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