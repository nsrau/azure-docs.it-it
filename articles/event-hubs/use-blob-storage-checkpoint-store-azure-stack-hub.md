---
title: Usare l'archiviazione BLOB come archivio di checkpoint nell'hub di Azure Stack (anteprima)Use Blob Storage as checkpoint store on Azure Stack Hub (preview)
description: Questo articolo descrive come usare l'archivio BLOB come archivio di checkpoint negli hub eventi nell'hub di Azure Stack (anteprima).
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.date: 03/18/2020
ms.author: spelluru
ms.openlocfilehash: 1f0e4dea44007ef82cb4b700ff0be4a5579541d8
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398922"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Usare l'archiviazione BLOB come archivio di checkpoint : Hub eventi nell'hub di Azure Stack (anteprima)Use Blob Storage as checkpoint store - Event Hubs on Azure Stack Hub (preview)
Se si usa Archiviazione BLOB di Azure come archivio di checkpoint in un ambiente che supporta una versione diversa di Storage Blob SDK rispetto a quelle in genere disponibili in Azure, è necessario usare il codice per modificare la versione dell'API del servizio di archiviazione alla versione specifica supportata da tale ambiente. Ad esempio, se si esegue Hub eventi in un Hub di Azure Stack versione 2002, la versione più alta disponibile per il servizio di archiviazione è la versione 2017-11-09.For example, if you're running [Event Hubs on an Azure Stack Hub version 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), the highest available version for the Storage service is version 2017-11-09. In questo caso, è necessario usare il codice per la versione dell'API del servizio di archiviazione a 2017-11-09.In this case, you need to use code to target the Storage service API version to 2017-11-09. Per un esempio su come scegliere come destinazione una versione dell'API di archiviazione specifica, vedere questi esempi in GitHub:For an example on how to target a specific Storage API version, see these samples on GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) o [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts), 
- Python - [Sincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/event_processor_blob_storage_example_with_storage_api_version.py), [Asincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

> [!IMPORTANT]
> Gli hub eventi nell'hub di Azure Stack sono attualmente in [anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ed è gratuito. 

Se si esegue il ricevitore Hub eventi che usa l'archivio BLOB come archivio di checkpoint senza assegnare la versione supportata dall'hub di Azure Stack, verrà visualizzato il seguente messaggio di errore:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Esempio di messaggio di errore in PythonSample error message in Python
Per Python, un `azure.core.exceptions.HttpResponseError` errore di viene `on_error(partition_context, error)` `EventHubConsumerClient.receive()`passato al gestore degli errori di . Tuttavia, `receive()` il metodo non genera un'eccezione. `print(error)`stamperà le seguenti informazioni sull'eccezione:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Il logger registrerà due avvisi simili ai seguenti:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente informazioni sul partizionamento e il checkpoint: Bilanciare il [carico della partizione tra più istanze dell'applicazioneSee](event-processor-balance-partition-load.md) the following article learn about partitioning and checkpointing: Balance partition load across multiple instances of your application
