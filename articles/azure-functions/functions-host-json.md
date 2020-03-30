---
title: Informazioni di riferimento su host.json per Funzioni di Azure 2.x
description: Documentazione di riferimento per il file host.json di Funzioni di Azure con il runtime v2.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3d98be2dcc351aa88b9e126c883865079e407c2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473371"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>Informazioni di riferimento su host.json per Funzioni di Azure 2.x e versioni successive 

> [!div class="op_single_selector" title1="Selezionare la versione del runtime di Funzioni di Azure in uso: "]
> * [Versione 1](functions-host-json-v1.md)
> * [Versione 2](functions-host-json.md)

Il file di metadati *host.json* contiene le opzioni di configurazione globali che interessano tutte le funzioni dell’app per le funzioni. Questo articolo elenca le impostazioni disponibili a partire dalla versione 2.x del runtime di Funzioni di Azure.This article lists the settings that are available starting with version 2.x of the Azure Functions runtime.  

> [!NOTE]
> Questo articolo è per Funzioni di Azure 2.x e versioni successive.  Per informazioni di riferimento su host.json in Funzioni 1.x, vedere [Informazioni di riferimento su host.json per Funzioni di Azure 1.x](functions-host-json-v1.md).

Altre opzioni di configurazione dell'app per le funzioni vengono gestite nelle [impostazioni dell'app](functions-app-settings.md) (per le app distribuite) o nel file [local.settings.json](functions-run-local.md#local-settings-file) (per lo sviluppo locale).

Le configurazioni in host.json correlate alle associazioni vengono applicate allo stesso modo a ogni funzione nell'app per le funzioni. 

## <a name="sample-hostjson-file"></a>File di esempio host.json

Nel file *host.json* di esempio seguente per la versione 2.x sono state specificate tutte le opzioni possibili (escluse tutte le opzioni disponibili solo per uso interno).

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
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
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

Controlla le opzioni per Application Insights, incluse le opzioni di [campionamento.](./functions-monitoring.md#configure-sampling)

Per la struttura JSON completa, vedere il [file host.json di esempio](#sample-hostjson-file)precedente.

> [!NOTE]
> Il campionamento di log potrebbe non consentire di visualizzare alcune esecuzioni nel pannello monitoraggio di Application Insights. Per evitare il `samplingExcludedTypes: "Request"` campionamento del log, aggiungere al `applicationInsights` valore.

| Proprietà | Predefinito | Descrizione |
| --------- | --------- | --------- | 
| samplingImpostazioni | n/d | Vedere [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | true | Abilita la raccolta delle metriche in tempo reale. |
| enableDependencyTracking (oggetto di codice enableDependencyTracking) | true | Abilita il rilevamento delle dipendenze. |
| enablePerformanceCountersCollection (informazioni in due) | true | Abilita la raccolta dei contatori delle prestazioni Kudu. |
| liveMetricsInitializationDelay | 00:00:15 | Solo per uso interno. |
| HttpAutoCollectionOptions (informazioni in base alle opzioni utente) | n/d | Vedere [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| snapshotConfigurazione | n/d | Vedere [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingSettings

|Proprietà | Predefinito | Descrizione |
| --------- | --------- | --------- | 
| isEnabled | true | Abilita o disabilita il campionamento. | 
| maxTelemetryItemsPerSecond | 20 | Numero di destinazione di elementi di telemetria registrati al secondo in ogni host del server. Se l'app viene eseguita su molti host, ridurre questo valore per rimanere entro la frequenza di traffico complessiva. | 
| valutazioneIntervallo | 01:00:00 | Intervallo in base al quale viene rivalutata la frequenza corrente dei dati di telemetria. La valutazione viene eseguita come media mobile. Potrebbe essere necessario ridurre questo intervallo se la telemetria è responsabile di burst improvvisi. |
| initialSamplingPercentage| 1.0 | La percentuale di campionamento iniziale applicata all'inizio del processo di campionamento per variare dinamicamente la percentuale. Non ridurre il valore durante il debug. |
| campionamentPercentageIncreaseTimeout | 00:00:01 | Quando il valore percentuale di campionamento viene modificato, questa proprietà determina la velocità con cui in seguito Application Insights può aumentare nuovamente la percentuale di campionamento per acquisire più dati. |
| campionamentPercentageDecreaseTimeout | 00:00:01 | Quando il valore percentuale di campionamento viene modificato, questa proprietà determina la velocità con cui in seguito Application Insights può ridurre nuovamente la percentuale di campionamento per acquisire meno dati. |
| minSamplingPercentualE | 0,1 | Poiché la percentuale di campionamento varia, questa proprietà determina la percentuale minima di campionamento consentita. |
| maxSamplingPercentage | 0,1 | Poiché la percentuale di campionamento varia, questa proprietà determina la percentuale massima di campionamento consentita. |
| movingMediaRatio (tasso mobileMediaRatio) | 1.0 | Nel calcolo della media mobile, peso assegnato al valore più recente. Usare un valore uguale o inferiore a 1. I valori più bassi rendono l'algoritmo meno reattivo alle modifiche improvvise. |
| excludedTypes | Null | Elenco delimitato da punti e virgola di tipi che non si desidera campionare. I tipi riconosciuti sono: dipendenza, evento, eccezione, pageview, richiesta, traccia. Tutte le istanze dei tipi specificati vengono trasmesse; i tipi non specificati vengono campionati. |
| includedTipi | Null | Elenco delimitato da punti e virgola di tipi che si desidera campionare; un elenco vuoto implica tutti i tipi. Tipo elencato nei `excludedTypes` tipi di sostituzione elencati di seguito. I tipi riconosciuti sono: dipendenza, evento, eccezione, pageview, richiesta, traccia. Tutte le istanze dei tipi specificati vengono trasmesse; i tipi non specificati vengono campionati. |

### <a name="applicationinsightshttpautocollectionoptions"></a>ApplicationInsights.httpAutoCollectionOptions

|Proprietà | Predefinito | Descrizione |
| --------- | --------- | --------- | 
| EnableHttpTriggerExtendedInfoCollection (informazioni in questo insieme) | true | Abilita o disabilita le informazioni sulle richieste HTTP estese per i trigger HTTP: intestazioni di correlazione delle richieste in ingresso, supporto delle chiavi di strumentazione multipla, metodo HTTP, percorso e risposta. |
| enableW3CDistributedTracing (traccia d'eccezione) | true | Abilita o disabilita il supporto del protocollo di traccia distribuita W3C (e attiva lo schema di correlazione legacy). Abilitato per `enableHttpTriggerExtendedInfoCollection` impostazione predefinita se è true. Se `enableHttpTriggerExtendedInfoCollection` è false, questo flag si applica solo alle richieste in uscita, non alle richieste in ingresso. |
| enableResponseHeaderInjection | true | Abilita o disabilita l'inserimento di intestazioni di correlazione multicomponente nelle risposte. L'abilitazione dell'inserimento consente a Application Insights di costruire una mappa dell'applicazione in cui vengono utilizzate diverse chiavi di strumentazione. Abilitato per `enableHttpTriggerExtendedInfoCollection` impostazione predefinita se è true. Questa impostazione non `enableHttpTriggerExtendedInfoCollection` si applica se è false. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

Per altre informazioni sugli snapshot, vedere Eseguire il debug di [snapshot sulle eccezioni nelle app .NET](/azure/azure-monitor/app/snapshot-debugger) e Risolvere i problemi relativi [all'abilitazione](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)del debugger snapshot di Application Insights o alla visualizzazione degli snapshot.

|Proprietà | Predefinito | Descrizione |
| --------- | --------- | --------- | 
| agentEndpoint | Null | Endpoint utilizzato per connettersi al servizio Debugger snapshot di Application Insights.The endpoint used to connect to the Application Insights Snapshot Debugger service. Se null, viene utilizzato un endpoint predefinito. |
| captureSnapshotMemoryWeight | 0.5 | Il peso assegnato alla dimensione della memoria del processo corrente durante il controllo se la memoria è sufficiente per creare uno snapshot. Il valore previsto è una frazione corretta maggiore di 0 (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit (superatoRequestLimit) | 3 | Limite del numero di richieste non riuscite di richiesta di snapshot prima della disabilitazione del processore di telemetria.|
| handleUntrackedExceptions | true | Abilita o disabilita il rilevamento delle eccezioni che non vengono rilevate dai dati di telemetria di Application Insights.Enables or disables tracking of exceptions that aren't tracked by Application Insights telemetry. |
| isEnabled | true | Abilita o disabilita la raccolta snapshot | 
| isEnabledInDeveloperMode (modalità di gestione dei prodotti) | false | Abilita o disabilita la raccolta snapshot è abilitata in modalità sviluppatore. |
| isEnabledWhenProfiling | true | Abilita o disabilita la creazione di snapshot anche se Application Insights Profiler sta raccogliendo una sessione di profilatura dettagliata. |
| isExceptionSnappointsEnabled | false | Abilita o disabilita il filtraggio delle eccezioni. |
| isLowPrioritySnapshotUploader | true | Determina se eseguire il processo SnapshotUploader con priorità inferiore alla normale. |
| maximumCollectionPlanSize (ancora in base a un'impostazione in | 50 | Il numero massimo di problemi che possiamo monitorare in qualsiasi momento in un intervallo da uno a 9999. |
| maximumSnapshotsRequired | 3 | Numero massimo di snapshot raccolti per un singolo problema, in un intervallo compreso tra uno e 999. Un problema può essere considerato come un'istruzione throw individuale nell'applicazione. Una volta che il numero di snapshot raccolti per un problema raggiunge questo valore, non verranno `problemCounterResetInterval`raccolti `thresholdForSnapshotting` altri snapshot per tale problema fino a quando i contatori del problema non vengono reimpostati (vedere ) e il limite viene raggiunto nuovamente. |
| problemCounterResetInterval (Esempio di server) | 24:00:00 | Frequenza con cui reimpostare i contatori dei problemi in un intervallo da un minuto a sette giorni. Quando viene raggiunto questo intervallo, tutti i conteggi dei problemi vengono reimpostati su zero. I problemi esistenti che hanno già raggiunto la soglia per l'esecuzione `maximumSnapshotsRequired`di snapshot, ma che non hanno ancora generato il numero di snapshot in , rimangono attivi. |
| provideAnonymousTelemetry | true | Determina se inviare a Microsoft l'utilizzo anonimo e i dati di telemetria degli errori a Microsoft. Questi dati di telemetria possono essere utilizzati se si contatta Microsoft per risolvere i problemi relativi al debugger snapshot. Viene inoltre utilizzato per monitorare i modelli di utilizzo. |
| riconnettereIntervallo | 00:15:00 | Frequenza di riconnessione all'endpoint del debugger snapshot. L'intervallo consentito è da un minuto a un giorno. |
| shadowCopyFolder (cartella shadowCopy) | Null | Specifica la cartella da utilizzare per la copia shadow dei file binari. Se non è impostata, le cartelle specificate dalle seguenti variabili di ambiente vengono provate nell'ordine: Fabric_Folder_App_Temp LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess (processo shareUploaderProcess) | true | Se true, solo un'istanza di SnapshotUploader raccoglierà e caricherà gli snapshot per più app che condividono InstrumentationKey. Se impostato su false, snapshotUploader sarà univoco per ogni tupla (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | true | Determina se elaborare o meno gli snapshot in un thread con priorità I/O bassa. La creazione di uno snapshot è un'operazione rapida ma, per caricare uno snapshot nel servizio Snapshot Debugger, deve prima essere scritto su disco come minidump. Ciò avviene nel processo SnapshotUploader. L'impostazione di questo valore su true utilizza i/o con priorità bassa per scrivere il minidump, che non competerà con l'applicazione per le risorse. L'impostazione di questo valore su false velocizza la creazione di minidump a scapito del rallentamento dell'applicazione. |
| snapshotPerDayLimit | 30 | Il numero massimo di snapshot consentiti in un giorno (24 ore). Questo limite viene applicato anche sul lato servizio Application Insights.This limit is also enforced on the Application Insights service side. I caricamenti sono limitati a 50 al giorno per applicazione, ovvero per chiave di strumentazione. Questo valore consente di impedire la creazione di snapshot aggiuntivi che verranno eventualmente rifiutati durante il caricamento. Un valore pari a zero rimuove completamente il limite, operazione non consigliata. |
| snapshotsPerTenMinutesLimit | 1 | Numero massimo di snapshot consentiti in 10 minuti. Anche se non esiste un limite superiore su questo valore, prestare attenzione ad aumentarlo nei carichi di lavoro di produzione perché potrebbe influire sulle prestazioni dell'applicazione. La creazione di uno snapshot è veloce, ma la creazione di un minidump dello snapshot e il caricamento nel servizio Snapshot Debugger è un'operazione molto più lenta che competerà con l'applicazione per le risorse (sia CPU che I/O). |
| TempFolder (cartella TempFolder) | Null | Specifica la cartella in cui scrivere minidump e file di log del caricatore. Se non è impostata, viene utilizzata *la %TEMP%.* |
| thresholdForSnapshotting (sogliaPerl'istantaneazione) | 1 | Numero di volte in cui Application Insights deve visualizzare un'eccezione prima di richiedere snapshot. |
| uploaderProxy | Null | Sostituisce il server proxy utilizzato nel processo Snapshot Uploader. Potrebbe essere necessario utilizzare questa impostazione se l'applicazione si connette a Internet tramite un server proxy. L'agente di raccolta snapshot viene eseguito all'interno del processo dell'applicazione e utilizzerà le stesse impostazioni proxy. Tuttavia, Snapshot Uploader viene eseguito come processo separato e potrebbe essere necessario configurare manualmente il server proxy. Se questo valore è null, Snapshot Collector tenterà di rilevare automaticamente l'indirizzo del proxy esaminando System.Net.WebRequest.DefaultWebProxy e passando il valore a Snapshot Uploader. Se questo valore non è null, il rilevamento automatico non viene utilizzato e il server proxy qui specificato verrà utilizzato in Snapshot Uploader. |

## <a name="cosmosdb"></a>cosmosDb

L'impostazione di configurazione è reperibile in [Trigger e associazioni di Cosmos DB](functions-bindings-cosmosdb-v2-output.md#host-json).

## <a name="durabletask"></a>durableTask

L'impostazione di configurazione è reperibile nelle [associazioni per Durable Functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di Hub eventi](functions-bindings-event-hubs-output.md#host-json). 

## <a name="extensions"></a>Estensioni

Proprietà che restituisce un oggetto che contiene tutte le impostazioni di associazione specifiche, ad esempio [http](#http) e [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

I pacchetti di estensione consentono di aggiungere un set compatibile di estensioni di associazione di Funzioni all'app per le funzioni. Per altre informazioni, vedere Pacchetti di [estensioni per lo sviluppo locale](functions-bindings-register.md#extension-bundles).

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Un elenco di funzioni eseguite dall'host di processo. Una matrice vuota indica l’esecuzione di tutte le funzioni. Deve essere utilizzato solo in caso di [esecuzione in locale](functions-run-local.md). In app per le funzioni in Azure è necessario invece seguire i passaggi descritti in [Come disabilitare le funzioni in Funzioni di Azure](disable-function.md) per disabilitare le funzioni specifiche invece di usare questa impostazione.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la durata del timeout per tutte le funzioni. Segue il formato della stringa timespan. In un piano di consumo serverless l'intervallo valido va da 1 secondo a 10 minuti e il valore predefinito è 5 minuti.  

Nel piano Premium, l'intervallo valido è compreso tra 1 secondo e 60 minuti e il valore predefinito è 30 minuti.

In un piano dedicato (servizio app) non esiste alcun limite complessivo e il valore predefinito è 30 minuti. `-1` È consigliabile mantenere un limite superiore fisso, ma è consigliabile mantenere un limite superiore fisso.

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

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|Enabled|true|Indica se la funzionalità è abilitata. | 
|healthCheckInterval|10 secondi|Intervallo di tempo tra i controlli dell'integrità periodici in background. | 
|healthCheckWindow|2 minuti|Finestra temporale scorrevole usata in combinazione con l'impostazione `healthCheckThreshold`.| 
|healthCheckThreshold|6|Numero massimo di volte in cui il controllo dell'integrità può non riuscire prima che venga avviato un riciclo host.| 
|counterThreshold|0,80|Soglia a partire dalla quale un contatore delle prestazioni verrà considerato non integro.| 

## <a name="http"></a>http

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni HTTP](functions-bindings-http-webhook-output.md#hostjson-settings).

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

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Definisce il livello di registrazione dei file abilitato.  Le opzioni sono `never`, `always`, `debugOnly`. |
|logLevel|n/d|Oggetto che definisce il filtro delle categorie di log per le funzioni nell'app. Le versioni 2.x e successive seguono il ASP.NET layout Core per il filtro delle categorie di log. Questa impostazione consente di filtrare la registrazione per funzioni specifiche. Per altre informazioni, vedere [Filtro dei log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) nella documentazione di ASP.NET Core. |
|console|n/d| Impostazione di registrazione nella [console](#console). |
|applicationInsights|n/d| Impostazione di [ApplicationInsights](#applicationinsights). |

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

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|isEnabled|false|Abilita o disabilita la registrazione nella console.| 

## <a name="manageddependency"></a>managedDependency (dipendenza di managed

Dipendenza gestita è una funzionalità attualmente supportata solo con le funzioni basate su PowerShell.Managed dependency is a feature that is currently only supported with PowerShell based functions. Consente alle dipendenze di essere gestite automaticamente dal servizio. Quando `enabled` la proprietà `true`è `requirements.psd1` impostata su , il file viene elaborato. Le dipendenze vengono aggiornate quando vengono rilasciate versioni secondarie. Per altre informazioni, vedere Dipendenza gestita nell'articolo di PowerShell.For more information, see [Managed dependency](functions-reference-powershell.md#dependency-management) in the PowerShell article.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>queues

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di Archiviazione code](functions-bindings-storage-queue-output.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni di SendGrid](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

Le impostazioni di configurazione sono reperibili in [Trigger e associazioni del bus di servizio](functions-bindings-service-bus-output.md#host-json).

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

|Proprietà  |Predefinito | Descrizione |
|---------|---------|---------| 
|lockPeriod|00:00:15|Il periodo per cui vengono eseguiti blocchi a livello di funzione. I blocchi si rinnovano automaticamente.| 
|listenerLockPeriod|00:01:00|Il periodo per cui vengono acquisiti blocchi di listener.| 
|listenerLockRecoveryPollingInterval|00:01:00|L'intervallo di tempo utilizzato per il ripristino di blocco listener se non è stato possibile acquisire un blocco di listener all'avvio.| 
|lockAcquisitionTimeout|00:01:00|La quantità massima di tempo per cui il runtime tenterà di acquisire un blocco.| 
|lockAcquisitionPollingInterval|n/d|L'intervallo tra i tentativi di acquisizione di un blocco.| 

## <a name="version"></a>version

Questo valore indica la versione dello schema di host.json. La stringa `"version": "2.0"` di versione è necessaria per un'app per le funzioni destinata al runtime v2 o a una versione successiva. Non sono presenti modifiche allo schema host.json tra v2 e v3.

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
