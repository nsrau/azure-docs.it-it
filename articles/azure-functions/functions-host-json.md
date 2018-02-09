---
title: Riferimento host.json per Funzioni di Azure
description: Documentazione di riferimento per il file host.json di Funzioni di Azure.
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2017
ms.author: tdykstra
ms.openlocfilehash: 58fc58049e346d60c0882a91bd04485746a15cbd
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="hostjson-reference-for-azure-functions"></a>Riferimento host.json per Funzioni di Azure

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. In questo articolo sono elencate le impostazioni disponibili. Lo schema JSON è riportato all’indirizzo http://json.schemastore.org/host.

Sono disponibili altre opzioni di configurazione globali nelle [impostazioni dell’app](functions-app-settings.md) e nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>File di esempio host.json

Il seguente file di esempio *host.json* contiene tutte le possibili opzioni specificate.

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

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|batchSize|1000|Numero massimo di richieste da aggregare.| 
|flushTimeout|00:00:30|Intervallo massimo da aggregare.| 

Le chiamate di funzione vengono aggregate quando il primo dei due limiti viene raggiunto.

## <a name="applicationinsights"></a>applicationInsights

Controlla le [funzionalità di campionamento in Application Insights](functions-monitoring.md#configure-sampling).

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
|isEnabled|false|Abilita o disabilita il campionamento.| 
|maxTelemetryItemsPerSecond|5|La soglia oltre la quale viene avviato il campionamento.| 

## <a name="eventhub"></a>eventHub

Impostazioni di configurazione per i [trigger e le associazioni di Hub eventi](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Un elenco di funzioni che verrà eseguito dall'host di processo.  Una matrice vuota indica l’esecuzione di tutte le funzioni.  Deve essere utilizzato solo in caso di [esecuzione in locale](functions-run-local.md). Nelle app per le funziono, utilizzare la proprietà *function.json* `disabled` anziché questa proprietà in *host.json*.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la durata del timeout per tutte le funzioni. Nei piani di consumo, l'intervallo valido va da 1 secondo a 10 minuti e il valore predefinito è 5 minuti. Nei piani di servizio app, non è specificato alcun limite e il valore predefinito è null, che indica nessun timeout.

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

Impostazione di configurazione per i [trigger e le associazioni http](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

ID univoco per l'host di processo. Può essere una GUID con lettera minuscola con trattini rimossi. Necessaria durante l'esecuzione locale. Quando è in esecuzione in Funzioni di Azure, viene generato automaticamente un ID se `id` viene omesso.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

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

## <a name="queues"></a>queues

Impostazione di configurazione per i [trigger e le associazioni per code di archiviazione](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|maxPollingInterval|60000|L'intervallo massimo, in millisecondi, tra i polling di coda.| 
|visibilityTimeout|0|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo.| 
|batchSize|16|Il numero di messaggi in coda da recuperare ed elaborare in parallelo. Il valore massimo è 32.| 
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.| 
|newBatchThreshold|batchSize/2|La soglia in corrispondenza della quale viene recuperato un nuovo batch di messaggi.| 

## <a name="servicebus"></a>serviceBus

Impostazione di configurazione per i [trigger e le associazioni dei bus di servizio](functions-bindings-service-bus.md).

[!INCLUDE [functions-host-json-service-bus](../../includes/functions-host-json-service-bus.md)]

## <a name="singleton"></a>singleton

Impostazioni di configurazione per il comportamento di blocco Singleton. Per ulteriori informazioni, vedere il [problema GitHub sul supporto singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
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

Le impostazioni di configurazione per i log creati usando un oggetto `TraceWriter`. Vedere [registrazione C#](functions-reference-csharp.md#logging) e [registrazione Node.js](functions-reference-node.md#writing-trace-output-to-the-console). 

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

## <a name="watchdirectories"></a>watchDirectories

Un set di [directory codice condivise](functions-reference-csharp.md#watched-directories) da monitorare per le modifiche.  Assicura che quando viene modificato il codice in tali directory, le modifiche vengono prelevate dalle funzioni.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="durabletask"></a>durableTask

Nome [hub attività](durable-functions-task-hubs.md) per [Funzioni permanenti](durable-functions-overview.md).

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

I nomi degli hub attività devono iniziare con una lettera e contenere solo lettere e numeri. Se non specificato, il nome dell'hub attività predefinito per un'app per le funzioni è **DurableFunctionsHub**. Per altre informazioni, vedere [Hub attività](durable-functions-task-hubs.md).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su come aggiornare il file host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Vedere le impostazioni globali in variabili di ambiente](functions-app-settings.md)
