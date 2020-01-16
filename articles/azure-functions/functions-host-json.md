---
title: Informazioni di riferimento su host.json per Funzioni di Azure 2.x
description: Documentazione di riferimento per il file host.json di Funzioni di Azure con il runtime v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 782998e49b9af3bf4d2ae5a561faaca399c6809f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978806"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>informazioni di riferimento su host. JSON per funzioni di Azure 2. x e versioni successive 

> [!div class="op_single_selector" title1="Selezionare la versione del runtime di funzioni di Azure in uso: "]
> * [Versione 1](functions-host-json-v1.md)
> * [Versione 2](functions-host-json.md)

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. Questo articolo elenca le impostazioni disponibili a partire dalla versione 2. x del runtime di funzioni di Azure.  

> [!NOTE]
> Questo articolo è per funzioni di Azure 2. x e versioni successive.  Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

Altre opzioni di configurazione di app per le funzioni sono gestite nelle [impostazioni dell'app](functions-app-settings.md).

Alcune impostazioni host.json vengono usate solo l'esecuzione in locale nel file [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>File di esempio host.json

Nel file *host. JSON* di esempio seguente sono state specificate tutte le opzioni possibili, escluse quelle che sono solo per uso interno.

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
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 1.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 0.1,
              "movingAverageRatio": 1.0
            },
            "samplingExcludedTypes" : "Dependency;Event",
            "samplingIncludedTypes" : "PageView;Trace",
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
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

Controlla le opzioni per Application Insights, incluse le [Opzioni di campionamento](./functions-monitoring.md#configure-sampling).

Per la struttura JSON completa, vedere il [file host. JSON di esempio](#sample-hostjson-file)precedente.

> [!NOTE]
> Il campionamento di log potrebbe non consentire di visualizzare alcune esecuzioni nel pannello monitoraggio di Application Insights. Per evitare il campionamento dei log, aggiungere `samplingExcludedTypes: "Request"` al valore `applicationInsights`.

| Proprietà | Predefinito | Description |
| --------- | --------- | --------- | 
| samplingSettings | N/D | Vedere [applicationInsights. samplingSettings](#applicationinsightssamplingsettings). |
| samplingExcludedTypes | Null | Elenco delimitato da punti e virgola di tipi che non si desidera campionare. I tipi riconosciuti sono: dipendenza, evento, eccezione, pageview, richiesta, traccia. Tutte le istanze dei tipi specificati vengono trasmesse. i tipi non specificati vengono campionati. |
| samplingIncludedTypes | Null | Elenco delimitato da punti e virgola di tipi che si desidera campionare. un elenco vuoto implica tutti i tipi. Digitare elencato in `samplingExcludedTypes` tipi di override elencati di seguito. I tipi riconosciuti sono: dipendenza, evento, eccezione, pageview, richiesta, traccia. Tutte le istanze dei tipi specificati vengono trasmesse. i tipi non specificati vengono campionati. |
| enableLiveMetrics | true | Abilita la raccolta di metriche attive. |
| enableDependencyTracking | true | Abilita il rilevamento delle dipendenze. |
| enablePerformanceCountersCollection | true | Abilita la raccolta dei contatori delle prestazioni Kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Solo per uso interno. |
| httpAutoCollectionOptions | N/D | Vedere [applicationInsights. httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfiguration | N/D | Vedere [applicationInsights. snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights. samplingSettings

|Proprietà | Predefinito | Description |
| --------- | --------- | --------- | 
| isEnabled | true | Abilita o disabilita il campionamento. | 
| maxTelemetryItemsPerSecond | 20 | Numero di destinazione degli elementi di telemetria registrati al secondo in ogni host server. Se l'app viene eseguita in molti host, ridurre questo valore in modo che rimanga entro la frequenza di destinazione complessiva del traffico. | 
| evaluationInterval | 01:00:00 | Intervallo in corrispondenza del quale viene rivalutata la frequenza corrente dei dati di telemetria. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi. |
| initialSamplingPercentage| 1.0 | Percentuale di campionamento iniziale applicata all'inizio del processo di campionamento per variare in modo dinamico la percentuale. Non ridurre il valore durante il debug. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Quando viene modificato il valore della percentuale di campionamento, questa proprietà determina il periodo di tempo in cui Application Insights può generare nuovamente la percentuale di campionamento per acquisire più dati. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Quando viene modificato il valore della percentuale di campionamento, questa proprietà determina il periodo di tempo in cui Application Insights può abbassare di nuovo la percentuale di campionamento per acquisire meno dati. |
| minSamplingPercentage | 0,1 | Poiché la percentuale di campionamento varia, questa proprietà determina la percentuale minima di campionamento consentita. |
| maxSamplingPercentage | 0,1 | Poiché la percentuale di campionamento varia, questa proprietà determina la percentuale massima di campionamento consentita. |
| movingAverageRatio | 1.0 | Nel calcolo della media mobile, peso assegnato al valore più recente. Usare un valore uguale o inferiore a 1. I valori più bassi rendono l'algoritmo meno reattivo alle modifiche improvvise. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights. httpAutoCollectionOptions

|Proprietà | Predefinito | Description |
| --------- | --------- | --------- | 
| enableHttpTriggerExtendedInfoCollection | true | Abilita o Disabilita le informazioni di richiesta HTTP estese per i trigger HTTP: intestazioni di correlazione delle richieste in ingresso, supporto delle chiavi multistrumentazione, metodo HTTP, percorso e risposta. |
| enableW3CDistributedTracing | true | Abilita o Disabilita il supporto del protocollo di traccia distribuita W3C (e attiva lo schema di correlazione legacy). Abilitata per impostazione predefinita se `enableHttpTriggerExtendedInfoCollection` è true. Se `enableHttpTriggerExtendedInfoCollection` è false, questo flag si applica solo alle richieste in uscita e non alle richieste in ingresso. |
| enableResponseHeaderInjection | true | Abilita o Disabilita l'inserimento di intestazioni di correlazione di più componenti in risposte. L'abilitazione dell'inserimento consente Application Insights di creare una mappa dell'applicazione a quando vengono utilizzate più chiavi di strumentazione. Abilitata per impostazione predefinita se `enableHttpTriggerExtendedInfoCollection` è true. Questa impostazione non è applicabile se `enableHttpTriggerExtendedInfoCollection` è false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights. snapshotConfiguration

Per altre informazioni sugli snapshot, vedere [eseguire il debug di snapshot sulle eccezioni nelle app .NET](/azure/azure-monitor/app/snapshot-debugger) e [risolvere i problemi di abilitazione Application Insights snapshot debugger o la visualizzazione di snapshot](/azure/azure-monitor/app/snapshot-debugger-troubleshoot).

|Proprietà | Predefinito | Description |
| --------- | --------- | --------- | 
| agentEndpoint | Null | Endpoint utilizzato per la connessione al servizio Application Insights Snapshot Debugger. Se null, viene utilizzato un endpoint predefinito. |
| captureSnapshotMemoryWeight | 0,5 | Il peso assegnato alle dimensioni della memoria del processo corrente quando viene verificato se è disponibile memoria sufficiente per creare uno snapshot. Il valore previsto è maggiore di 0, frazione corretta (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | Limite al numero di richieste non riuscite per richiedere snapshot prima che il processore di telemetria sia disabilitato.|
| handleUntrackedExceptions | true | Abilita o Disabilita il rilevamento di eccezioni non rilevate da Application Insights telemetria. |
| isEnabled | true | Abilita o Disabilita la raccolta di snapshot | 
| isEnabledInDeveloperMode | false | Abilita o Disabilita la raccolta di snapshot è abilitata in modalità sviluppatore. |
| isEnabledWhenProfiling | true | Abilita o Disabilita la creazione di snapshot anche se il Application Insights Profiler raccoglie una sessione di profilatura dettagliata. |
| isExceptionSnappointsEnabled | false | Abilita o Disabilita l'applicazione di filtri alle eccezioni. |
| isLowPrioritySnapshotUploader | true | Determina se eseguire il processo SnapshotUploader alla priorità normale. |
| maximumCollectionPlanSize | 50 | Il numero massimo di problemi che è possibile rilevare in qualsiasi momento in un intervallo compreso tra 1 e 9999. |
| maximumSnapshotsRequired | 3 | Numero massimo di snapshot raccolti per un singolo problema, in un intervallo compreso tra 1 e 999. Un problema può essere considerato come una singola istruzione throw nell'applicazione. Quando il numero di snapshot raccolti per un problema raggiunge questo valore, non verranno raccolti altri snapshot per tale problema finché non vengono reimpostati i contatori dei problemi (vedere `problemCounterResetInterval`) e viene raggiunto di nuovo il limite di `thresholdForSnapshotting`. |
| problemCounterResetInterval | 24:00:00 | Con quale frequenza è possibile reimpostare i contatori dei problemi in un intervallo compreso tra un minuto e sette giorni. Quando viene raggiunto questo intervallo, tutti i conteggi dei problemi vengono reimpostati su zero. I problemi esistenti che hanno già raggiunto la soglia per la creazione di snapshot, ma che non hanno ancora generato il numero di snapshot in `maximumSnapshotsRequired`, restano attivi. |
| provideAnonymousTelemetry | true | Determina se inviare dati di telemetria sull'utilizzo e sugli errori anonimi a Microsoft. Questa telemetria può essere usata se si contatta Microsoft per risolvere i problemi relativi al Snapshot Debugger. Viene usato anche per monitorare i modelli di utilizzo. |
| reconnectInterval | 00:15:00 | Frequenza della riconnessione all'endpoint Snapshot Debugger. L'intervallo consentito è di un minuto a un giorno. |
| shadowCopyFolder | Null | Specifica la cartella da usare per la copia shadow dei file binari. Se non è impostato, le cartelle specificate dalle seguenti variabili di ambiente vengono tentate in ordine: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | true | Se è true, solo un'istanza di SnapshotUploader raccoglierà e caricherà gli snapshot per più app che condividono il InstrumentationKey. Se è impostato su false, SnapshotUploader sarà univoco per ogni tupla (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se elaborare o meno gli snapshot in un thread con priorità di i/o basso. La creazione di uno snapshot è un'operazione rapida ma, per caricare uno snapshot nel servizio Snapshot Debugger, è necessario prima scriverlo su disco come un minidump. Questo problema si verifica nel processo SnapshotUploader. L'impostazione di questo valore su true usa i/o con priorità bassa per scrivere il minidump, che non sarà in competizione con l'applicazione per le risorse. Impostando questo valore su false si accelera la creazione di minidump a scapito del rallentamento dell'applicazione. |
| snapshotsPerDayLimit | 30 | Numero massimo di snapshot consentiti in un giorno (24 ore). Questo limite viene anche applicato sul lato del servizio Application Insights. I caricamenti sono limitati a 50 al giorno per ogni applicazione, ovvero per chiave di strumentazione. Questo valore consente di evitare la creazione di snapshot aggiuntivi che verranno rifiutati durante il caricamento. Il valore zero rimuove completamente il limite, operazione non consigliata. |
| snapshotsPerTenMinutesLimit | 1 | Numero massimo di snapshot consentiti in 10 minuti. Anche se non esiste un limite superiore per questo valore, prestare attenzione ad aumentarlo sui carichi di lavoro di produzione perché potrebbe influito sulle prestazioni dell'applicazione. La creazione di uno snapshot è veloce, ma la creazione di un minidump dello snapshot e il suo caricamento nel servizio Snapshot Debugger sono un'operazione molto più lenta che concorrerà all'applicazione per le risorse (CPU e I/O). |
| tempFolder | Null | Specifica la cartella in cui scrivere i minidump e i file di log del caricatore. Se non è impostato, viene usato *%Temp%\dumps* . |
| thresholdForSnapshotting | 1 | Il numero di volte in cui Application Insights deve visualizzare un'eccezione prima di chiedere gli snapshot. |
| uploaderProxy | Null | Esegue l'override del server proxy utilizzato nel processo di caricamento dello snapshot. Potrebbe essere necessario usare questa impostazione se l'applicazione si connette a Internet tramite un server proxy. Il Snapshot Collector viene eseguito all'interno del processo dell'applicazione e utilizzerà le stesse impostazioni del proxy. Tuttavia, il caricatore di snapshot viene eseguito come processo separato e potrebbe essere necessario configurare manualmente il server proxy. Se questo valore è null, Snapshot Collector tenterà di rilevare automaticamente l'indirizzo del proxy esaminando System .NET. WebRequest. DefaultWebProxy e passando il valore al caricatore dello snapshot. Se questo valore non è null, il rilevamento automatico non viene usato e il server proxy specificato qui verrà usato nell'Uploader dello snapshot. |

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

Nel piano Premium, l'intervallo valido è compreso tra 1 secondo e 60 minuti e il valore predefinito è 30 minuti.

In un piano dedicato (servizio app) non esiste alcun limite globale e il valore predefinito è 30 minuti. Il valore `-1` indica un'esecuzione non vincolata, ma è consigliabile mantenere un limite superiore fisso.

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
|Enabled|true|Indica se la funzionalità è abilitata. | 
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

|Proprietà  |Predefinito | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definisce il livello di registrazione dei file abilitato.  Le opzioni sono `never`, `always`, `debugOnly`. |
|logLevel|N/D|Oggetto che definisce il filtro delle categorie di log per le funzioni nell'app. Le versioni 2. x e successive seguono il layout ASP.NET Core per i filtri delle categorie di log. Questa impostazione consente di filtrare la registrazione per specifiche funzioni. Per altre informazioni, vedere [Filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) nella documentazione di ASP.NET Core. |
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

|Proprietà  |Predefinito | Description |
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

|Proprietà  |Predefinito | Description |
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
