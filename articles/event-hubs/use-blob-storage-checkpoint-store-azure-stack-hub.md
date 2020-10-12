---
title: Usare l'archiviazione BLOB come archivio di checkpoint nell'hub Azure Stack (anteprima)
description: Questo articolo descrive come usare l'archiviazione BLOB come archivio di checkpoint in hub eventi nell'hub Azure Stack (anteprima).
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039226"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Usare l'archiviazione BLOB come archivio di checkpoint-Hub eventi nell'hub Azure Stack (anteprima)
Se si usa l'archiviazione BLOB di Azure come archivio di checkpoint in un ambiente che supporta una versione diversa di storage BLOB SDK rispetto a quelle disponibili in genere in Azure, sarà necessario usare il codice per modificare la versione dell'API del servizio di archiviazione nella versione specifica supportata da tale ambiente. Ad esempio, se si esegue [Hub eventi in un hub Azure stack versione 2002](/azure-stack/user/event-hubs-overview), la versione più recente disponibile per il servizio di archiviazione è la versione 2017-11-09. In questo caso, è necessario usare il codice per fare riferimento alla versione dell'API del servizio di archiviazione a 2017-11-09. Per un esempio su come definire come destinazione una versione specifica dell'API di archiviazione, vedere questi esempi su GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) o  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python [sincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> Hub eventi nell'hub Azure Stack è attualmente disponibile in [Anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ed è gratuito. 

Se si esegue il ricevitore di hub eventi che usa l'archiviazione BLOB come archivio di checkpoint senza avere come destinazione la versione supportata da Azure Stack Hub, verrà visualizzato il messaggio di errore seguente:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Messaggio di errore di esempio in Python
Per Python, un errore di `azure.core.exceptions.HttpResponseError` viene passato al gestore degli errori `on_error(partition_context, error)` di `EventHubConsumerClient.receive()` . Tuttavia, il metodo `receive()` non genera un'eccezione. `print(error)` stamperà le seguenti informazioni sull'eccezione:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Il logger registrerà due avvisi come quelli seguenti:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente informazioni sul partizionamento e il Checkpoint: [bilanciamento del carico delle partizioni tra più istanze dell'applicazione](event-processor-balance-partition-load.md)
