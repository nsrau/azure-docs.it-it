---
title: Riferimento host.json per Funzioni di Azure
description: Documentazione di riferimento per il file host.json di Funzioni di Azure.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 704a41ec840e2a252a1bbb5c20688f722bd0cdfd
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887037"
---
# <a name="hostjson-reference-for-azure-functions"></a>Riferimento host.json per Funzioni di Azure

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. In questo articolo sono elencate le impostazioni disponibili. Lo schema JSON è disponibile all'indirizzo http://json.schemastore.org/host.

> [!NOTE]
> Esistono differenze significative in *host.json* tra le versioni v1 e v2 del Runtime di Funzioni di Azure. Per un app per le funzioni che ha come destinazione il runtime v2, è necessaria `"version": "2.0"`.

Altre opzioni di configurazione di app per le funzioni sono gestite nelle [impostazioni dell'app](functions-app-settings.md).

Alcune impostazioni host.json vengono usate solo l'esecuzione in locale nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>File di esempio host.json

I file di esempio *host.json* seguenti hanno tutte le possibili opzioni specificate.

### <a name="version-2x"></a>Versione 2.x

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "eventHubs": {
          "maxBatchSize": 64,
          "prefetchCount": 256,
          "batchCheckpointFrequency": 1
        },
        "http": {
            "routePrefix": "api",
            "maxConcurrentRequests": 100,
            "maxOutstandingRequests": 30
        },
        "queues": {
            "visibilityTimeout": "00:00:10",
            "maxDequeueCount": 3
        },
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        },
        "serviceBus": {
          "maxConcurrentCalls": 16,
          "prefetchCount": 100,
          "autoRenewTimeout": "00:05:00"
        }
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "sampling": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

### <a name="version-1x"></a>Versione 1.x

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Le sezioni seguenti di questo articolo illustrano ogni proprietà di livello superiore. Tutte sono facoltative se non diversamente specificato.

## <a name="aggregator"></a>aggregator

Specifica il numero di chiamate di funzione che vengono aggregate quando [si esegue il calcolo della metrica per Application Insights](functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Proprietà |Predefinito  | DESCRIZIONE |
|---------|---------|---------| 
|batchSize|1000|Numero massimo di richieste da aggregare.| 
|flushTimeout|00:00:30|Intervallo massimo da aggregare.| 

Le chiamate di funzione vengono aggregate quando il primo dei due limiti viene raggiunto.

## <a name="applicationinsights"></a>applicationInsights

Controlla le [funzionalità di campionamento in Application Insights](functions-monitoring.md#configure-sampling). Nella versione 2.x questa impostazione è un elemento figlio della [registrazione](#log).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|isEnabled|true|Abilita o disabilita il campionamento.| 
|maxTelemetryItemsPerSecond|5|La soglia oltre la quale viene avviato il campionamento.| 

## <a name="durabletask"></a>durableTask

Impostazioni di configurazione per [Funzioni permanenti](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**. Per altre informazioni, vedere [Hub attività](durable-functions-task-hubs.md).

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------|
|HubName|DurableFunctionsHub|I nomi alternativi dell'[hub attività](durable-functions-task-hubs.md) possono essere usati per separare le applicazioni Durable Functions, anche se usano lo stesso back-end di archiviazione.|
|ControlQueueBatchSize|32|Numero di messaggi di cui eseguire il pull dalla coda di controllo contemporaneamente.|
|PartitionCount |4|Numero di partizioni per la coda di controllo. Può essere un numero intero positivo compreso tra 1 e 16.|
|ControlQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda di controllo.|
|WorkItemQueueVisibilityTimeout |5 minuti|Timeout di visibilità dei messaggi rimossi dalla coda degli elementi di lavoro.|
|MaxConcurrentActivityFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni di attività che possono essere elaborate contemporaneamente in una singola istanza host.|
|MaxConcurrentOrchestratorFunctions |10 volte il numero di processori sul computer corrente|Numero massimo di funzioni di attività che possono essere elaborate contemporaneamente in una singola istanza host.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|Nome dell'impostazione dell'app che include la stringa di connessione di Archiviazione di Azure usata per gestire le risorse di Archiviazione di Azure sottostanti.|
|TraceInputsAndOutputs |false|Valore che indica se tenere traccia degli input e degli output di chiamate di funzione. Quando si tiene traccia degli eventi di esecuzione delle funzioni, il comportamento predefinito prevede di includere il numero di byte degli input e output serializzati per le chiamate di funzione. In questo modo vengono offerte informazioni minime sull'aspetto di input e output senza aumentare il numero di registri o esporre inavvertitamente informazioni riservate ai registri stessi. Se questa proprietà viene impostata su true, per impostazione predefinita viene registrato l'intero contenuto degli input e output della funzione.|
|LogReplayEvents|false|Un valore che indica se scrivere eventi di riproduzione di orchestrazione in Application Insights.|
|EventGridTopicEndpoint ||URL di un endpoint di un argomento personalizzato di Griglia di eventi di Azure. Se questa proprietà è impostata, gli eventi di notifica del ciclo di vita dell'orchestrazione vengono pubblicati in questo endpoint. Questa proprietà supporta la risoluzione delle impostazioni dell'app.|
|EventGridKeySettingName ||Nome dell'impostazione dell'app che contiene la chiave usata per l'autenticazione con l'argomento personalizzato di Griglia di eventi di Azure in `EventGridTopicEndpoint`.|
|EventGridPublishRetryCount|0|Il numero di tentativi se la pubblicazione nell'argomento di Griglia di eventi ha esito negativo.|
|EventGridPublishRetryInterval|5 minuti|Intervallo fra i tentativi di pubblicazione in Griglia di eventi nel formato *hh:mm:ss*.|

Molti di questi elementi vengono usati per ottimizzare le prestazioni. Per altre informazioni, vedere [Prestazioni e scalabilità](durable-functions-perf-and-scale.md).

## <a name="eventhub"></a>eventHub

Impostazioni di configurazione per i [trigger e le associazioni di Hub eventi](functions-bindings-event-hubs.md). Nella versione 2.x questo è un elemento figlio delle [estensioni](#extensions).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="extensions"></a>Estensioni

*Solo versione 2.x.*

Proprietà che restituisce un oggetto che contiene tutte le impostazioni di associazione specifiche, ad esempio [http](#http) e [eventHub](#eventhub).

## <a name="functions"></a>functions

Un elenco di funzioni eseguite dall'host di processo. Una matrice vuota indica l’esecuzione di tutte le funzioni. Deve essere utilizzato solo in caso di [esecuzione in locale](functions-run-local.md). In app per le funzioni in Azure è necessario invece seguire i passaggi descritti in [Come disabilitare le funzioni in Funzioni di Azure](disable-function.md) per disabilitare le funzioni specifiche invece di usare questa impostazione.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la durata del timeout per tutte le funzioni. In un piano di consumo serverless l'intervallo valido va da 1 secondo a 10 minuti e il valore predefinito è 5 minuti. In un piano di servizio app non è previsto alcun limite complessivo e il valore predefinito varia a seconda della versione del runtime. Nella versione 2.x il valore predefinito per un piano di servizio app è di 30 minuti. Nella versione 1.x il valore è *null*, che indica nessun timeout.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Impostazioni di configurazione per il [monitoraggio integrità host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|Enabled|true|Indica se la funzionalità è abilitata. | 
|healthCheckInterval|10 secondi|Intervallo di tempo tra i controlli dell'integrità periodici in background. | 
|healthCheckWindow|2 minuti|Finestra temporale scorrevole usata in combinazione con l'impostazione `healthCheckThreshold`.| 
|healthCheckThreshold|6|Numero massimo di volte in cui il controllo dell'integrità può non riuscire prima che venga avviato un riciclo host.| 
|counterThreshold|0,80|Soglia a partire dalla quale un contatore delle prestazioni verrà considerato non integro.| 

## <a name="http"></a>http

Impostazione di configurazione per i [trigger e le associazioni http](functions-bindings-http-webhook.md). Nella versione 2.x questo è un elemento figlio delle [estensioni](#extensions).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Solo versione 1.x.*

ID univoco per l'host di processo. Può essere una GUID con lettera minuscola con trattini rimossi. Necessaria durante l'esecuzione locale. Durante l'esecuzione in Azure, è consigliabile non impostare un valore ID. Un ID viene generato automaticamente in Azure quando viene omesso `id`. Quando si usa la versione 2.x del runtime non è possibile impostare un ID di app per le funzioni personalizzato.

Se si condivide un account di archiviazione tra più app per le funzioni, assicurarsi che ogni app abbia un valore diverso per `id`. È possibile omettere la proprietà `id` o impostare manualmente la proprietà `id` di ogni app per le funzioni su un valore diverso. Il trigger timer usa un blocco dell'archiviazione per garantire che vi sia una sola istanza del timer quando un'app per le funzioni viene scalata orizzontalmente a più istanze. Se due app per le funzioni condividono la stessa proprietà `id` e ognuna usa un trigger timer, verrà eseguito solo un timer.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

*Solo versione 1.x; per la versione 2.x usare [registrazione](#logging).*

Controlla le operazioni di filtro per i log scritti da un [oggetto ILogger](functions-monitoring.md#write-logs-in-c-functions) o [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|categoryFilter|n/d|Specifica il filtro per categoria| 
|defaultLevel|Informazioni|Per tutte le categorie non è state specificate nella matrice `categoryLevels`, inviare i log a questo livello e oltre per Application Insights.| 
|categoryLevels|n/d|Una matrice di categorie che specifica il livello di log minimo per l'invio ad Application Insights per ogni categoria. La categoria specificata qui controlla tutte le categorie che iniziano con lo stesso valore, e i valori più lunghi hanno la precedenza. Nel file di esempio precedente *host.json*, tutte le categorie che iniziano "Host.Aggregator" eseguono il log al livello `Information`. Tutte le altre categorie che iniziano con "Host", ad esempio "Host.Executor", eseguono il log al livello `Error`.| 

## <a name="logging"></a>registrazione

*Solo versione 1.x; per la versione 2.x usare [logger](#logger).*

Controlla i comportamenti di registrazione dell'app per le funzioni, tra cui Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "applicationInsights": {
        ...
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------|
|fileLoggingMode|Informazioni|Invia ad Application Insights i log di questo livello e di livelli superiori. |
|logLevel|n/d|Oggetto che definisce il filtro delle categorie di log per le funzioni nell'app. La versione 2.x segue il layout di ASP.NET Core per il filtro delle categorie di log. Ciò consente di filtrare la registrazione per funzioni specifiche. Per altre informazioni, vedere [Filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) nella documentazione di ASP.NET Core. |
|applicationInsights|n/d| Impostazione di [ApplicationInsights](#applicationinsights). |

## <a name="queues"></a>queues

Impostazione di configurazione per i [trigger e le associazioni per code di archiviazione](functions-bindings-storage-queue.md). Nella versione 2.x questo è un elemento figlio delle [estensioni](#extensions).

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="servicebus"></a>serviceBus

Impostazione di configurazione per i [trigger e le associazioni dei bus di servizio](functions-bindings-service-bus.md). Nella versione 2.x questo è un elemento figlio delle [estensioni](#extensions).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Impostazioni di configurazione per il comportamento di blocco Singleton. Per ulteriori informazioni, vedere il [problema GitHub sul supporto singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|lockPeriod|00:00:15|Il periodo per cui vengono eseguiti blocchi a livello di funzione. I blocchi si rinnovano automaticamente.| 
|listenerLockPeriod|00:01:00|Il periodo per cui vengono acquisiti blocchi di listener.| 
|listenerLockRecoveryPollingInterval|00:01:00|L'intervallo di tempo utilizzato per il ripristino di blocco listener se non è stato possibile acquisire un blocco di listener all'avvio.| 
|lockAcquisitionTimeout|00:01:00|La quantità massima di tempo per cui il runtime tenterà di acquisire un blocco.| 
|lockAcquisitionPollingInterval|n/d|L'intervallo tra i tentativi di acquisizione di un blocco.| 

## <a name="tracing"></a>tracing

*Versione 1.x*

Le impostazioni di configurazione per i log creati usando un oggetto `TraceWriter`. Vedere [registrazione C#](functions-reference-csharp.md#logging) e [registrazione Node.js](functions-reference-node.md#writing-trace-output-to-the-console). Nella versione 2.x tutto il comportamento del log viene controllato dalla [registrazione](#logging).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|consoleLevel|info|Il livello di traccia per la registrazione della console. Le opzioni sono: `off`, `error`, `warning`, `info` e `verbose`.|
|fileLoggingMode|debugOnly|Il livello di traccia per la registrazione di file. Le opzioni sono `never`, `always`, `debugOnly`.| 

## <a name="version"></a>version

*Versione 2.x*

Per un app per le funzioni che ha come destinazione il runtime v2, è necessaria la stringa di versione `"version": "2.0"`.

## <a name="watchdirectories"></a>watchDirectories

Un set di [directory codice condivise](functions-reference-csharp.md#watched-directories) da monitorare per le modifiche.  Assicura che quando viene modificato il codice in tali directory, le modifiche vengono prelevate dalle funzioni.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su come aggiornare il file host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Vedere le impostazioni globali in variabili di ambiente](functions-app-settings.md)
