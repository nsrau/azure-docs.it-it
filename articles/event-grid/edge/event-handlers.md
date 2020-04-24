---
title: Gestori eventi e destinazioni-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: Gestori di eventi e destinazioni in griglia di eventi sul perimetro
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
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Gestori di eventi e destinazioni in griglia di eventi sul perimetro

Un gestore eventi è il punto in cui l'evento viene sottoposto a un'ulteriore azione o per l'elaborazione dell'evento. Con la griglia di eventi sul modulo Edge, il gestore eventi può trovarsi nello stesso dispositivo perimetrale, in un altro dispositivo o nel cloud. È possibile usare qualsiasi webhook per gestire eventi o inviare eventi a uno dei gestori nativi come griglia di eventi di Azure.

Questo articolo fornisce informazioni su come configurare ognuno di essi.

## <a name="webhook"></a>WebHook

Per pubblicare in un endpoint di Webhook, impostare `endpointType` su `WebHook` e specificare:

* endpointUrl: URL dell'endpoint del webhook

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

Per pubblicare in un endpoint cloud di griglia di eventi di Azure `endpointType` , `eventGrid` impostare su e fornire:

* endpointUrl: URL dell'argomento della griglia di eventi nel cloud
* sasKey: chiave SAS dell'argomento della griglia di eventi
* topicName: nome per contrassegnare tutti gli eventi in uscita in griglia di eventi. Il nome dell'argomento è utile quando si esegue la pubblicazione in un argomento di dominio di griglia di eventi.

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

## <a name="iot-edge-hub"></a>Hub IoT Edge

Per pubblicare in un modulo Hub Edge, impostare `endpointType` su `edgeHub` e fornire:

* outputName: output in cui il modulo di griglia di eventi instraderà gli eventi che corrispondono a questa sottoscrizione a edgeHub. Ad esempio, gli eventi che corrispondono alla sottoscrizione seguente verranno scritti in/messages/modules/eventgridmodule/outputs/sampleSub4.

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

Per pubblicare in un hub eventi, impostare `endpointType` su `eventHub` e specificare:

* connectionString: stringa di connessione per l'hub eventi specifico di destinazione generato tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. È possibile generare una stringa di connessione specifica dell'entità passando all'hub eventi specifico in cui si vuole eseguire la pubblicazione nel portale di Azure e facendo clic su **criteri di accesso condivisi** per generare una nuova stringa connecection specifica dell'entità.

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

Per pubblicare in una coda del `endpointType` bus di servizio, impostare `serviceBusQueue` su e specificare:

* connectionString: stringa di connessione per la coda del bus di servizio specifica di destinazione generata tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. Per generare una stringa di connessione specifica dell'entità, passare alla coda del bus di servizio specifica in cui si vuole eseguire la pubblicazione nel portale di Azure e fare clic su **criteri di accesso condiviso** per generare una nuova stringa connecection specifica dell'entità.

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

Per pubblicare in un argomento del `endpointType` bus di servizio, impostare `serviceBusTopic` su e specificare:

* connectionString: stringa di connessione per l'argomento del bus di servizio specifico di destinazione generato tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. Per generare una stringa di connessione specifica dell'entità, passare all'argomento del bus di servizio specifico in cui si vuole eseguire la pubblicazione nel portale di Azure e fare clic su **criteri di accesso condiviso** per generare una nuova stringa connecection specifica dell'entità.

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

Per pubblicare in una coda di archiviazione, impostare `endpointType` su `storageQueue` e specificare:

* QueueName: nome della coda di archiviazione in cui si sta eseguendo la pubblicazione.
* connectionString: stringa di connessione per l'account di archiviazione in cui si trova la coda di archiviazione.

    >[!NOTE]
    > Gli hub eventi unline, le code del bus di servizio e gli argomenti del bus di servizio, la stringa di connessione usata per le code di archiviazione non è specifica dell'entità. Ma deve essere invece la stringa di connessione per l'account di archiviazione.

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