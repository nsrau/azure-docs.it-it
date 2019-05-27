---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131700"
---
Impostazioni di configurazione per [Funzioni permanenti](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**. Per altre informazioni, vedere [Hub attività](../articles/azure-functions/durable-functions-task-hubs.md).

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|hubName|DurableFunctionsHub|I nomi alternativi dell'[hub attività](../articles/azure-functions/durable-functions-task-hubs.md) possono essere usati per separare le applicazioni di Durable Functions, anche se usano lo stesso back-end di archiviazione.|
|controlQueueBatchSize|32|Numero di messaggi di cui eseguire il pull dalla coda di controllo contemporaneamente.|
|partitionCount |4|Numero di partizioni per la coda di controllo. Può essere un numero intero positivo compreso tra 1 e 16.|
|controlQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda di controllo.|
|workItemQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda degli elementi di lavoro.|
|maxConcurrentActivityFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni di attività che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxConcurrentOrchestratorFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni dell'agente di orchestrazione che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxQueuePollingInterval|30 secondi|Il massimo controllo e l'intervallo di polling della coda di elementi di lavoro nel *hh: mm:* formato. I valori più alti possono causare latenze di elaborazione dei messaggi superiore. I valori più bassi possono comportare costi di archiviazione più elevati a causa di transazioni di archiviazione maggiore.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nome dell'impostazione dell'app che include la stringa di connessione di Archiviazione di Azure usata per gestire le risorse di Archiviazione di Azure sottostanti.|
|trackingStoreConnectionStringName||Il nome di una stringa di connessione da utilizzare per le tabelle di cronologia e le istanze. Se non specificato, il `azureStorageConnectionStringName` connessione viene usata.|
|trackingStoreNamePrefix||Il prefisso da utilizzare per la cronologia e le istanze tabelle quando `trackingStoreConnectionStringName` è specificato. Se non impostato, il valore del prefisso predefinito sarà `DurableTask`. Se `trackingStoreConnectionStringName` non viene specificato, userà le tabelle di cronologia e le istanze di `hubName` valore come il prefisso e tutte le impostazioni per `trackingStoreNamePrefix` verranno ignorati.|
|traceInputsAndOutputs |false|Valore che indica se tenere traccia degli input e degli output di chiamate di funzione. Quando si tiene traccia degli eventi di esecuzione delle funzioni, il comportamento predefinito prevede di includere il numero di byte degli input e output serializzati per le chiamate di funzione. Questo comportamento garantisce informazioni minime sugli input e output aspetto senza aumentare le dimensioni il log o esporre involontariamente informazioni riservate. Se questa proprietà viene impostata su true, per impostazione predefinita viene registrato l'intero contenuto degli input e output della funzione.|
|logReplayEvents|false|Un valore che indica se scrivere eventi di riproduzione di orchestrazione in Application Insights.|
|eventGridTopicEndpoint ||URL di un endpoint di un argomento personalizzato di Griglia di eventi di Azure. Quando questa proprietà è impostata, gli eventi di notifica del ciclo di vita di orchestrazione vengono pubblicati per questo endpoint. Questa proprietà supporta la risoluzione delle impostazioni dell'app.|
|eventGridKeySettingName ||Nome dell'impostazione dell'app che contiene la chiave usata per l'autenticazione con l'argomento personalizzato di Griglia di eventi di Azure in `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Il numero di tentativi se la pubblicazione nell'argomento di Griglia di eventi ha esito negativo.|
|eventGridPublishRetryInterval|5 minuti|Intervallo fra i tentativi di pubblicazione in Griglia di eventi nel formato *hh:mm:ss*.|
|eventGridPublishEventTypes||Elenco di tipi di evento per la pubblicazione in griglia di eventi. Se non specificato, verranno pubblicati tutti i tipi di eventi. Consentiti i valori includono `Started`, `Completed`, `Failed`, `Terminated`.|

Molte di queste impostazioni sono per ottimizzare le prestazioni. Per altre informazioni, vedere [Prestazioni e scalabilità](../articles/azure-functions/durable-functions-perf-and-scale.md).