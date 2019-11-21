---
title: Informazioni di riferimento su host.json per Funzioni di Azure 1.x
description: Documentazione di riferimento per il file host.json di Funzioni di Azure con il runtime v1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 99a571483086343d4e7d6188b2f401abc616c1bb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230597"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Informazioni di riferimento su host.json per Funzioni di Azure 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Versione 1](functions-host-json-v1.md)
> * [Versione 2](functions-host-json.md)

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. Questo articolo elenca le impostazioni disponibili per il runtime v1. Lo schema JSON è disponibile all'indirizzo http://json.schemastore.org/host.

> [!NOTE]
> Questo articolo riguarda Funzioni di Azure 1.x.  Per informazioni di riferimento su host.json in Funzioni 2.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 2.x](functions-host-json.md).

Altre opzioni di configurazione di app per le funzioni sono gestite nelle [impostazioni dell'app](functions-app-settings.md).

Alcune impostazioni host.json vengono usate solo l'esecuzione in locale nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>File di esempio host.json

I file di esempio *host.json* seguenti hanno tutte le possibili opzioni specificate.


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
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
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
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
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

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Configuration settings for the [Azure Cosmos DB trigger and bindings](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Proprietà  |Predefinito | Description |
|---------|---------|---------|
|GatewayMode|Gateway|La modalità di connessione usata dalla funzione durante la connessione al servizio di Azure Cosmos DB. Le opzioni sono `Direct` e `Gateway`|
|Protocol|Https|Il protocollo di connessione usato dalla funzione durante la connessione al servizio di Azure Cosmos DB.  Leggere [qui per una spiegazione di entrambe le modalità](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|N/D|Prefisso di lease da usare in tutte le funzioni in un'app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Impostazioni di configurazione per i [trigger e le associazioni di Hub eventi](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Un elenco di funzioni eseguite dall'host di processo. Una matrice vuota indica l’esecuzione di tutte le funzioni. Deve essere utilizzato solo in caso di [esecuzione in locale](functions-run-local.md). In app per le funzioni in Azure è necessario invece seguire i passaggi descritti in [Come disabilitare le funzioni in Funzioni di Azure](disable-function.md) per disabilitare le funzioni specifiche invece di usare questa impostazione.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la durata del timeout per tutte le funzioni. In un piano di consumo serverless l'intervallo valido va da 1 secondo a 10 minuti e il valore predefinito è 5 minuti. In an App Service plan, there is no overall limit and the default is _null_, which indicates no timeout.

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

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|enabled|true|Indica se la funzionalità è abilitata. | 
|healthCheckInterval|10 secondi|Intervallo di tempo tra i controlli dell'integrità periodici in background. | 
|healthCheckWindow|2 minuti|Finestra temporale scorrevole usata in combinazione con l'impostazione `healthCheckThreshold`.| 
|healthCheckThreshold|6|Numero massimo di volte in cui il controllo dell'integrità può non riuscire prima che venga avviato un riciclo host.| 
|counterThreshold|0,80|Soglia a partire dalla quale un contatore delle prestazioni verrà considerato non integro.| 

## <a name="http"></a>http

Impostazione di configurazione per i [trigger e le associazioni http](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Quando è abilitata, questa impostazione determina la pipeline di elaborazione della richiesta per il controllo periodico delle prestazioni dei contatori del sistema, ad esempio connessioni/thread/processi/memoria/CPU e così via. Se uno di questi contatori supera una soglia massima predefinita (80%), le richieste verranno rifiutate con una risposta 429 "Occupato" fino a quando i contatori non tornano a livelli normali.|
|maxConcurrentRequests|unbounded (`-1`)|Il numero massimo di funzioni HTTP che verrà eseguito in parallelo. Ciò consente di controllare la concorrenza e pertanto di semplificare la gestione dell'uso delle risorse. Ad esempio, potrebbe essere presente una funzione HTTP che usa una quantità di risorse di sistema (memoria/CPU/socket) tale da creare problemi quando la concorrenza è troppo elevata. Oppure potrebbe essere presente una funzione che invia richieste a un servizio di terze parti e tali chiamate devono essere a frequenza limitata. In questi casi potrebbe risultare utile l'applicazione di una limitazione.|
|maxOutstandingRequests|unbounded (`-1`)|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo. Questo limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". Ciò consente ai chiamanti di usare strategie di ripetizione dei tentativi basate sul tempo e di controllare la latenza massima delle richieste. Questa impostazione controlla solo l'accodamento che si verifica all'interno del percorso di esecuzione dell'host dello script. Altre code, ad esempio la coda di richieste ASP.NET, saranno valide e non interessate da questa impostazione.|
|routePrefix|api|Il prefisso della route che si applica a tutte le route. Utilizzare una stringa vuota per rimuovere il prefisso predefinito. |

## <a name="id"></a>id

ID univoco per l'host di processo. Può essere una GUID con lettera minuscola con trattini rimossi. Necessaria durante l'esecuzione locale. Durante l'esecuzione in Azure, è consigliabile non impostare un valore ID. Un ID viene generato automaticamente in Azure quando viene omesso `id`. 

Se si condivide un account di archiviazione tra più app per le funzioni, assicurarsi che ogni app abbia un valore diverso per `id`. È possibile omettere la proprietà `id` o impostare manualmente la proprietà `id` di ogni app per le funzioni su un valore diverso. Il trigger timer usa un blocco dell'archiviazione per garantire che vi sia una sola istanza del timer quando un'app per le funzioni viene scalata orizzontalmente a più istanze. Se due app per le funzioni condividono la stessa proprietà `id` e ognuna usa un trigger timer, verrà eseguito solo un timer.

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

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|categoryFilter|N/D|Specifica il filtro per categoria| 
|defaultLevel|Informazioni|Per tutte le categorie non è state specificate nella matrice `categoryLevels`, inviare i log a questo livello e oltre per Application Insights.| 
|categoryLevels|N/D|Una matrice di categorie che specifica il livello di log minimo per l'invio ad Application Insights per ogni categoria. La categoria specificata qui controlla tutte le categorie che iniziano con lo stesso valore, e i valori più lunghi hanno la precedenza. Nel file di esempio precedente *host.json*, tutte le categorie che iniziano "Host.Aggregator" eseguono il log al livello `Information`. Tutte le altre categorie che iniziano con "Host", ad esempio "Host.Executor", eseguono il log al livello `Error`.| 

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

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|maxPollingInterval|60000|L'intervallo massimo, in millisecondi, tra i polling di coda.| 
|visibilityTimeout|0|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo.| 
|batchSize|16|Il numero di messaggi in coda che il runtime di Funzioni recupera simultaneamente e di processi in parallelo. Quando il numero elaborato viene ridotto a `newBatchThreshold`, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è `batchSize` più `newBatchThreshold`. Questo limite si applica separatamente a ogni funzione attivata dalla coda. <br><br>Se si vuole evitare l'esecuzione in parallelo per i messaggi ricevuti su una coda, è possibile impostare `batchSize` su 1. Tuttavia, questa impostazione elimina solo la concorrenza se l'app per le funzioni viene eseguita su una singola macchina virtuale (VM). Se l'app per le funzioni scala orizzontalmente più macchine virtuali, ogni macchina virtuale potrebbe eseguire un'istanza di ogni funzione attivata dalla coda.<br><br>Il valore massimo per `batchSize` è 32. | 
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.| 
|newBatchThreshold|batchSize/2|Ogni volta che il numero di messaggi elaborati simultaneamente viene ridotto a questo numero, il runtime recupera un altro batch.| 

## <a name="sendgrid"></a>SendGrid

Configuration setting for the [SendGrind output binding](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|from|N/D|Indirizzo di posta elettronica del mittente in tutte le funzioni.| 

## <a name="servicebus"></a>serviceBus

Impostazione di configurazione per i [trigger e le associazioni dei bus di servizio](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|maxConcurrentCalls|16|Il numero massimo di chiamate simultanee al callback che il message pump deve avviare. Per impostazione predefinita, il runtime di Funzioni elabora più messaggi contemporaneamente. Per fare in modo che il runtime elabori un solo messaggio della coda o dell'argomento alla volta, impostare `maxConcurrentCalls` su 1. | 
|prefetchCount|N/D|Il valore predefinito di PrefetchCount che verrà utilizzato per il MessageReceiver sottostante.| 
|autoRenewTimeout|00:05:00|La durata massima entro il quale il blocco del messaggio verrà rinnovato automaticamente.| 

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

|Proprietà  |Predefinito | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|Il periodo per cui vengono eseguiti blocchi a livello di funzione. I blocchi si rinnovano automaticamente.| 
|listenerLockPeriod|00:01:00|Il periodo per cui vengono acquisiti blocchi di listener.| 
|listenerLockRecoveryPollingInterval|00:01:00|L'intervallo di tempo utilizzato per il ripristino di blocco listener se non è stato possibile acquisire un blocco di listener all'avvio.| 
|lockAcquisitionTimeout|00:01:00|La quantità massima di tempo per cui il runtime tenterà di acquisire un blocco.| 
|lockAcquisitionPollingInterval|N/D|L'intervallo tra i tentativi di acquisizione di un blocco.| 

## <a name="tracing"></a>tracing

*Versione 1.x*

Le impostazioni di configurazione per i log creati usando un oggetto `TraceWriter`. Vedere [registrazione C#](functions-reference-csharp.md#logging) e [registrazione Node.js](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Proprietà  |Predefinito | Description |
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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni su come aggiornare il file host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Vedere le impostazioni globali in variabili di ambiente](functions-app-settings.md)
