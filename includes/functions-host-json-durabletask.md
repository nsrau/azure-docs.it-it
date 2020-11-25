---
title: includere file
description: includere file
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6a862a051d0040ac99746d81f10ae63d5af7545f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013726"
---
Impostazioni di configurazione per [Funzioni permanenti](../articles/azure-functions/durable/durable-functions-overview.md).

> [!NOTE]
> Tutte le versioni principali di Durable Functions sono supportate in tutte le versioni del runtime di Funzioni di Azure. Tuttavia, lo schema della configurazione host.json è leggermente diverso a seconda della versione del runtime di Funzioni di Azure e della versione dell'estensione Durable Functions in uso. Gli esempi seguenti riguardano Funzioni di Azure 2.0 e 3.0. In entrambi gli esempi se si usa Funzioni di Azure 1.0, le impostazioni disponibili sono le stesse, ma la sezione "durableTask" del file host.json deve trovarsi nella radice della configurazione host.json invece che in un campo sotto "extension".

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
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
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**. Per altre informazioni, vedere [Hub attività](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|hubName|DurableFunctionsHub|I nomi alternativi dell'[hub attività](../articles/azure-functions/durable/durable-functions-task-hubs.md) possono essere usati per separare le applicazioni di Durable Functions, anche se usano lo stesso back-end di archiviazione.|
|controlQueueBatchSize|32|Numero di messaggi di cui eseguire il pull dalla coda di controllo contemporaneamente.|
|controlQueueBufferThreshold|256|Numero di messaggi della coda di controllo che possono essere memorizzati nel buffer in memoria contemporaneamente. Dopo tale soglia il dispatcher attenderà prima di rimuovere dalla coda eventuali altri messaggi.|
|partitionCount |4|Numero di partizioni per la coda di controllo. Può essere un numero intero positivo compreso tra 1 e 16.|
|controlQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda di controllo.|
|workItemQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda degli elementi di lavoro.|
|maxConcurrentActivityFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni di attività che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxConcurrentOrchestratorFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni dell'agente di orchestrazione che possono essere elaborate contemporaneamente in una singola istanza host.|
|maxQueuePollingInterval|30 secondi|Intervallo massimo di polling della coda di controllo e degli elementi di lavoro nel formato *hh:mm:ss*. Valori più elevati possono comportare un incremento della latenza nell'elaborazione dei messaggi. Valori inferiori possono comportare un incremento dei costi di archiviazione a causa dell'aumento del numero di transazione di archiviazione.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nome dell'impostazione dell'app che include la stringa di connessione di Archiviazione di Azure usata per gestire le risorse di Archiviazione di Azure sottostanti.|
|trackingStoreConnectionStringName||Nome di una stringa di connessione da usare per le tabelle Cronologia e Istanze. Se non è specificato, viene usata la connessione `connectionStringName` (Durable 2.x) o `azureStorageConnectionStringName` (Durable 1.x).|
|trackingStoreNamePrefix||Prefisso da usare per le tabelle Cronologia e Istanze quando si specifica `trackingStoreConnectionStringName`. Se non è impostato, il valore predefinito del prefisso sarà `DurableTask`. Se `trackingStoreConnectionStringName` non è specificato, come prefisso delle tabelle Cronologia e Istanze verrà usato il valore `hubName` ed eventuali impostazioni di `trackingStoreNamePrefix` verranno ignorate.|
|traceInputsAndOutputs |false|Valore che indica se tenere traccia degli input e degli output di chiamate di funzione. Quando si tiene traccia degli eventi di esecuzione delle funzioni, il comportamento predefinito prevede di includere il numero di byte degli input e output serializzati per le chiamate di funzione. Con questo comportamento vengono offerte informazioni minime sull'aspetto di input e output senza aumentare il numero di log o esporre inavvertitamente informazioni riservate. Se questa proprietà viene impostata su true, per impostazione predefinita viene registrato l'intero contenuto degli input e output della funzione.|
|logReplayEvents|false|Un valore che indica se scrivere eventi di riproduzione di orchestrazione in Application Insights.|
|eventGridTopicEndpoint ||URL di un endpoint di un argomento personalizzato di Griglia di eventi di Azure. Se questa proprietà è impostata, gli eventi di notifica del ciclo di vita dell'orchestrazione vengono pubblicati in questo endpoint. Questa proprietà supporta la risoluzione delle impostazioni dell'app.|
|eventGridKeySettingName ||Nome dell'impostazione dell'app che contiene la chiave usata per l'autenticazione con l'argomento personalizzato di Griglia di eventi di Azure in `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Il numero di tentativi se la pubblicazione nell'argomento di Griglia di eventi ha esito negativo.|
|eventGridPublishRetryInterval|5 minuti|Intervallo fra i tentativi di pubblicazione in Griglia di eventi nel formato *hh:mm:ss*.|
|eventGridPublishEventTypes||Elenco dei tipi di evento da pubblicare in Griglia di eventi. Se non è specificato, verranno pubblicati tutti i tipi di evento. I valori consentiti includono `Started`, `Completed`, `Failed`, `Terminated`.|
|useAppLease|true|Se è impostato su `true`, le app richiederanno l'acquisizione di un lease di BLOB a livello di app prima di elaborare i messaggi dell'hub attività. Per altre informazioni vedere la documentazione su [ripristino di emergenza e distribuzione geografica](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md). Disponibile a partire dalla versione v2.3.0.
|useLegacyPartitionManagement|true|Se è impostato su `false`, usa un algoritmo di gestione delle partizioni che riduce la possibilità di eseguire funzioni duplicate quando si aumenta il numero di istanze.  Disponibile a partire dalla versione v2.3.0. In una versione futura il valore predefinito verrà modificato in `false`.|
|useGracefulShutdown|false|(Anteprima) Abilita l'arresto in modalità normale per ridurre la possibilità di arresti dell'host che non riescono a completare le esecuzioni di funzioni in-process.|

Molte di queste impostazioni vengono usate per ottimizzare le prestazioni. Per altre informazioni, vedere [Prestazioni e scalabilità](../articles/azure-functions/durable/durable-functions-perf-and-scale.md).