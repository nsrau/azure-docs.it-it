---
title: Informazioni di riferimento su host.json per Funzioni di Azure 2.x
description: Documentazione di riferimento per il file host.json di Funzioni di Azure con il runtime v2.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 222ca8781ae9532f10ed7d113b93eac78c6a3bba
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129059"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>Informazioni di riferimento su host.json per Funzioni di Azure 2.x  

> [!div class="op_single_selector" title1="Selezionare la versione del runtime di funzioni di Azure in uso: "]
> * [Versione 1](functions-host-json-v1.md)
> * [Versione 2](functions-host-json.md)

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. Questo articolo elenca le impostazioni disponibili per il runtime v2.  

> [!NOTE]
> Questo articolo riguarda Funzioni di Azure 2.x.  Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

Altre opzioni di configurazione di app per le funzioni sono gestite nelle [impostazioni dell'app](functions-app-settings.md).

Alcune impostazioni host.json vengono usate solo l'esecuzione in locale nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>File di esempio host.json

I file di esempio *host.json* seguenti hanno tutte le possibili opzioni specificate.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
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
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20
            }
        }
    },
    "managedDependency": {
        "enabled": true
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

Le sezioni seguenti di questo articolo illustrano ogni proprietà di livello superiore. Tutte sono facoltative se non diversamente specificato.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Questa impostazione è un elemento figlio di [logging](#logging).

Controlla le [funzionalità di campionamento in Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Il campionamento di log potrebbe non consentire di visualizzare alcune esecuzioni nel pannello monitoraggio di Application Insights.

|Proprietà  |Default | DESCRIZIONE |
|---------|---------|---------| 
|isEnabled|true|Abilita o disabilita il campionamento.| 
|maxTelemetryItemsPerSecond|20|La soglia oltre la quale viene avviato il campionamento.| 
|EnableLiveMetrics |true|Abilita la raccolta di metriche attive.|
|EnableDependencyTracking|true|Abilita il rilevamento delle dipendenze.|
|EnablePerformanceCountersCollection|true|Abilita la raccolta dei contatori delle prestazioni Kudu.|

## <a name="cosmosdb"></a>cosmosDb

L'impostazione di configurazione è reperibile in [Trigger e associazioni di Cosmos DB](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

L'impostazione di configurazione è reperibile nelle [associazioni per Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di Hub eventi](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Estensioni

Proprietà che restituisce un oggetto che contiene tutte le impostazioni di associazione specifiche, ad esempio [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

I bundle di estensione consentono di aggiungere un set compatibile di estensioni di associazione di funzioni all'app per le funzioni. Per altre informazioni, vedere [bundle di estensione per lo sviluppo locale](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Un elenco di funzioni eseguite dall'host di processo. Una matrice vuota indica l’esecuzione di tutte le funzioni. Deve essere utilizzato solo in caso di [esecuzione in locale](functions-run-local.md). In app per le funzioni in Azure è necessario invece seguire i passaggi descritti in [Come disabilitare le funzioni in Funzioni di Azure](disable-function.md) per disabilitare le funzioni specifiche invece di usare questa impostazione.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la durata del timeout per tutte le funzioni. Segue il formato stringa TimeSpan. In un piano di consumo serverless l'intervallo valido va da 1 secondo a 10 minuti e il valore predefinito è 5 minuti.  
In un piano dedicato (servizio app) non esiste alcun limite globale e il valore predefinito è 30 minuti. Il valore `-1` indica un'esecuzione non vincolata.

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

|Proprietà  |Default | DESCRIZIONE |
|---------|---------|---------| 
|enabled|true|Indica se la funzionalità è abilitata. | 
|healthCheckInterval|10 secondi|Intervallo di tempo tra i controlli dell'integrità periodici in background. | 
|healthCheckWindow|2 minuti|Finestra temporale scorrevole usata in combinazione con l'impostazione `healthCheckThreshold`.| 
|healthCheckThreshold|6|Numero massimo di volte in cui il controllo dell'integrità può non riuscire prima che venga avviato un riciclo host.| 
|counterThreshold|0,80|Soglia a partire dalla quale un contatore delle prestazioni verrà considerato non integro.| 

## <a name="http"></a>http

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni HTTP](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>registrazione

Controlla i comportamenti di registrazione dell'app per le funzioni, tra cui Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Proprietà  |Default | DESCRIZIONE |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definisce il livello di registrazione dei file abilitato.  Le opzioni sono `never`, `always`, `debugOnly`. |
|logLevel|N/D|Oggetto che definisce il filtro delle categorie di log per le funzioni nell'app. La versione 2.x segue il layout di ASP.NET Core per il filtro delle categorie di log. Ciò consente di filtrare la registrazione per funzioni specifiche. Per altre informazioni, vedere [Filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) nella documentazione di ASP.NET Core. |
|console|N/D| Impostazione di registrazione nella [console](#console). |
|applicationInsights|N/D| Impostazione di [ApplicationInsights](#applicationinsights). |

## <a name="console"></a>console

Questa impostazione è un elemento figlio di [logging](#logging). Controlla la registrazione nella console quando non è attiva la modalità di debug.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Proprietà  |Default | DESCRIZIONE |
|---------|---------|---------| 
|isEnabled|false|Abilita o disabilita la registrazione nella console.| 

## <a name="manageddependency"></a>managedDependency

La dipendenza gestita è una funzionalità attualmente supportata solo con le funzioni basate su PowerShell. Consente la gestione automatica delle dipendenze da parte del servizio. Quando la proprietà `enabled` è impostata su `true`, il file di `requirements.psd1` viene elaborato. Le dipendenze vengono aggiornate quando vengono rilasciate versioni secondarie. Per altre informazioni, vedere [dipendenza gestita](functions-reference-powershell.md#dependency-management) nell'articolo di PowerShell.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di Archiviazione code](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni del bus di servizio](functions-bindings-service-bus.md#host-json).

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

|Proprietà  |Default | DESCRIZIONE |
|---------|---------|---------| 
|lockPeriod|00:00:15|Il periodo per cui vengono eseguiti blocchi a livello di funzione. I blocchi si rinnovano automaticamente.| 
|listenerLockPeriod|00:01:00|Il periodo per cui vengono acquisiti blocchi di listener.| 
|listenerLockRecoveryPollingInterval|00:01:00|L'intervallo di tempo utilizzato per il ripristino di blocco listener se non è stato possibile acquisire un blocco di listener all'avvio.| 
|lockAcquisitionTimeout|00:01:00|La quantità massima di tempo per cui il runtime tenterà di acquisire un blocco.| 
|lockAcquisitionPollingInterval|N/D|L'intervallo tra i tentativi di acquisizione di un blocco.| 

## <a name="version"></a>version

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
