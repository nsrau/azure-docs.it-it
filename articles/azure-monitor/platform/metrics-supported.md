---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 58c0db1f8200a757c95d7f66f9d821968e7e1839
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121118"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure. Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. L'elenco riportato di seguito include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Per cercare metriche e per accedervi, usare la [versione API 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|qpu_metric|QPU|Numero|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Byte|Media|Quantità totale di memoria allocata dal processo del motore Analysis Services e dai processi del contenitore mashup, esclusa la memoria condivisa con altri processi.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Byte|Media|Dimensioni correnti dello spazio degli indirizzi virtuali usato dal processo del motore Analysis Services e dai processi del contenitore mashup.|ServerResourceType|
|TotalConnectionRequests|Numero totale di richieste di connessione|Numero|Media|Numero totale delle richieste di connessione in arrivo.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|ServerResourceType|
|TotalConnectionFailures|Numero totale di errori di connessione|Numero|Media|Numero totale dei tentativi di connessione non riusciti.|ServerResourceType|
|CurrentUserSessions|Sessioni utente correnti|Numero|Media|Numero corrente di sessioni utente attive.|ServerResourceType|
|QueryPoolBusyThreads|Thread occupati pool di query|Numero|Media|Numero dei thread occupati nel pool dei thread di query.|ServerResourceType|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Numero|Media|Numero dei processi nella coda del pool dei thread dei comandi.|ServerResourceType|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Numero|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|CurrentConnections|Connessione: connessioni correnti|Numero|Media|Numero corrente delle connessioni client stabilite.|ServerResourceType|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Numero|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|MemoryUsage|Memoria: utilizzo memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria xVelocity (VertiPaq) in eccesso rispetto al limite di memoria del motore xVelocity.|ServerResourceType|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|ServerResourceType|
|Quota|Memoria: quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|ServerResourceType|
|QuotaBlocked|Memoria: Richieste di quota bloccate|Numero|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|ServerResourceType|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|ServerResourceType|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|ServerResourceType|
|RowsReadPerSec|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|ServerResourceType|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|ServerResourceType|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|ServerResourceType|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Numero|Media|Numero dei thread occupati nel pool dei thread dei comandi.|ServerResourceType|
|CommandPoolIdleThreads|Thread: Thread inattivi pool di comandi|Numero|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|ServerResourceType|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Numero|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingIdleThreads|Thread: Thread inattivi per analisi dei thread lunghi|Numero|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Numero|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Numero|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Thread: Thread non di I/O occupati nel pool di elaborazione|Numero|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Numero|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Numero|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Numero|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|ServerResourceType|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Numero|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Numero|Media|Numero dei processi nella coda del pool dei thread di query.|ServerResourceType|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Numero|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Numero|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Numero|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|ServerResourceType|
|mashup_engine_qpu_metric|QPU Motore M|Numero|Media|Utilizzo QPU da parte dei processi motore mashup|ServerResourceType|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Byte privati motore M |Byte|Media|La quantità totale di processi del contenitore mashup di memoria è stata allocata, esclusa la memoria condivisa con altri processi.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore M |Byte|Media|Dimensioni correnti dello spazio degli indirizzi virtuali che i processi del contenitore mashup usano.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Requests|Requests|Numero|Totale|Numero totale di richieste del gateway in un determinato periodo. Può essere sezionata in base a diverse dimensioni per facilitare la diagnosi dei problemi. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Totale richieste gateway|Numero|Totale|Numero totale di richieste del gateway in un determinato periodo. Questa metrica è stata deprecata, è consigliabile usare la nuova metrica `Requests`. |Location, Hostname|
|SuccessfulRequests|Richieste gateway riuscite|Numero|Totale|Numero totale di richieste del gateway riuscite in un determinato periodo. Questa metrica è stata deprecata, è consigliabile usare la nuova metrica `Requests`.|Location, Hostname|
|UnauthorizedRequests|Richieste del gateway non autorizzate|Numero|Totale| Numero totale di richieste del gateway non autorizzate in un determinato periodo. Questa metrica è stata deprecata, è consigliabile usare la nuova metrica `Requests`.|Location, Hostname|
|FailedRequests|Richieste gateway non riuscite|Numero|Totale|Numero totale di richieste del gateway non riuscite in un determinato periodo. Questa metrica è stata deprecata, è consigliabile usare la nuova metrica `Requests`.|Location, Hostname|
|OtherRequests|Altre richieste del gateway|Numero|Totale|Numero totale di richieste del gateway in un determinato periodo che non rientrano nelle categorie di esito positivo, non autorizzato o non riuscito. Questa metrica è stata deprecata, è consigliabile usare la nuova metrica `Requests`. |Location, Hostname|
|Durata|Durata complessiva delle richieste del gateway|Millisecondi|Media|Tempo che intercorre tra la ricezione di una richiesta da un client da parte di gestione API e la restituzione di una risposta al client.|Location, Hostname|
|Capacità|Capacità|Percentuale|Media|Indicatore di carico su un'istanza di gestione API per prendere decisioni informate sulla scalabilità dell'istanza in modo da gestire un carico maggiore.|Location|
|EventHubTotalEvents|Totale eventi EventHub|Numero|Totale|Il numero totale di eventi inviati a EventHub da gestione API in un determinato periodo.|Location|
|EventHubSuccessfulEvents|Eventi EventHub riusciti|Numero|Totale|Numero totale di eventi EventHub riusciti in un determinato periodo.|Location|
|EventHubTotalFailedEvents|Eventi EventHub non riusciti|Numero|Totale|Numero totale di eventi EventHub non riusciti in un determinato periodo.|Location|
|EventHubRejectedEvents|Eventi EventHub rifiutati|Numero|Totale|Numero totale di eventi EventHub rifiutati (configurazione errata o non autorizzati) in un determinato periodo.|Location|
|EventHubThrottledEvents|Eventi EventHub limitati|Numero|Totale|Numero totale di eventi EventHub limitati in un determinato periodo.|Location|
|EventHubTimedoutEvents|Timeout degli eventi di EventHub|Numero|Totale|Numero totale di eventi EventHub di timeout in un determinato periodo.|Location|
|EventHubDroppedEvents|Eventi EventHub eliminati|Numero|Totale|Numero totale di eventi ignorati a causa del raggiungimento del limite delle dimensioni della coda in un determinato periodo.|Location|
|EventHubTotalBytesSent|Dimensioni degli eventi di EventHub|Byte|Totale|Dimensioni totali degli eventi EventHub in byte in un determinato periodo.|Location|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Numero|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentRuns|Totale esecuzioni di aggiornamenti della distribuzione|Numero|Totale|Totale esecuzioni di distribuzioni di aggiornamenti software|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Totale esecuzioni computer di distribuzione aggiornamenti|Numero|Totale|Il computer di distribuzione degli aggiornamenti software totale viene eseguito in un'esecuzione di distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CoreCount|Numero di core dedicati|Numero|Totale|Numero totale di core dedicati nell'account Batch|Nessuna dimensione|
|TotalNodeCount|Numero di nodi dedicati|Numero|Totale|Numero totale di nodi dedicati nell'account Batch|Nessuna dimensione|
|LowPriorityCoreCount|Numero di core a bassa priorità|Numero|Totale|Numero totale di core a bassa priorità nell'account Batch|Nessuna dimensione|
|TotalLowPriorityNodeCount|Numero di nodi a bassa priorità|Numero|Totale|Numero totale di nodi a bassa priorità nell'account Batch|Nessuna dimensione|
|CreatingNodeCount|Numero nodi creati|Numero|Totale|Il numero di nodi in fase di creazione|Nessuna dimensione|
|StartingNodeCount|Numero nodi avviati|Numero|Totale|Il numero di nodi in fase di avvio|Nessuna dimensione|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Numero|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|Nessuna dimensione|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Numero|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|Nessuna dimensione|
|IdleNodeCount|Numero nodi inattivi|Numero|Totale|Il numero di nodi inattivi|Nessuna dimensione|
|OfflineNodeCount|Numero nodi offline|Numero|Totale|Il numero di nodi offline|Nessuna dimensione|
|RebootingNodeCount|Numero nodi riavviati|Numero|Totale|Il numero di nodi in fase di riavvio|Nessuna dimensione|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Numero|Totale|Il numero di nodi in fase di ricreazione dell'immagine|Nessuna dimensione|
|RunningNodeCount|Numero nodi eseguiti|Numero|Totale|Il numero di nodi in esecuzione|Nessuna dimensione|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Numero|Totale|Il numero di nodi in uscita dal pool|Nessuna dimensione|
|UnusableNodeCount|Numero nodi non usabili|Numero|Totale|Il numero di nodi che non possono essere usati|Nessuna dimensione|
|PreemptedNodeCount|Numero di nodi annullati|Numero|Totale|Numero di nodi annullati|Nessuna dimensione|
|TaskStartEvent|Eventi attività avviate|Numero|Totale|Il numero totale di attività avviate|Nessuna dimensione|
|TaskCompleteEvent|Eventi attività completate|Numero|Totale|Il numero totale di attività completate|Nessuna dimensione|
|TaskFailEvent|Eventi attività non riuscite|Numero|Totale|Il numero totale di attività completate con lo stato Non riuscito|Nessuna dimensione|
|PoolCreateEvent|Eventi pool creati|Numero|Totale|Il numero totale di pool che sono stati creati|Nessuna dimensione|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Numero|Totale|Il numero totale di ridimensionamenti dei pool avviati|Nessuna dimensione|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Numero|Totale|Il numero totale di ridimensionamenti dei pool completati|Nessuna dimensione|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Numero|Totale|Il numero totale di eliminazioni di pool avviate|Nessuna dimensione|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Numero|Totale|Il numero totale di eliminazioni di pool completate|Nessuna dimensione|
|JobDeleteCompleteEvent|Eventi eliminazione processi completati|Numero|Totale|Numero totale dei processi eliminati.|Nessuna dimensione|
|JobDeleteStartEvent|Eventi eliminazione processi avviati|Numero|Totale|Numero totale di processi da eliminare.|Nessuna dimensione|
|JobDisableCompleteEvent|Eventi disabilitazione processi completati|Numero|Totale|Numero totale dei processi disabilitati.|Nessuna dimensione|
|JobDisableStartEvent|Eventi disabilitazione processi avviati|Numero|Totale|Numero totale di processi da disabilitare.|Nessuna dimensione|
|JobStartEvent|Eventi di avvio processi|Numero|Totale|Numero totale di processi avviati.|Nessuna dimensione|
|JobTerminateCompleteEvent|Eventi terminazione processi completati|Numero|Totale|Numero totale di processi terminati.|Nessuna dimensione|
|JobTerminateStartEvent|Eventi terminazione processi avviati|Numero|Totale|Numero totale di processi da terminare.|Nessuna dimensione|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Numero|Massima||ShardId|
|totalcommandsprocessed|Totale operazioni|Numero|Totale||ShardId|
|cachehits|Riscontri cache|Numero|Totale||ShardId|
|cachemisses|Mancati riscontri nella cache|Numero|Totale||ShardId|
|getcommands|Operazioni Get|Numero|Totale||ShardId|
|setcommands|Operazioni Set|Numero|Totale||ShardId|
|operationsPerSecond|Operazioni al secondo|Numero|Massima||ShardId|
|evictedkeys|Chiavi rimosse|Numero|Totale||ShardId|
|totalkeys|Totale chiavi|Numero|Massima||ShardId|
|expiredkeys|Chiavi scadute|Numero|Totale||ShardId|
|usedmemory|Memoria utilizzata|Byte|Massima||ShardId|
|usedmemorypercentage|Percentuale memoria usata|Percentuale|Massima||ShardId|
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massima||ShardId|
|serverLoad|Carico server|Percentuale|Massima||ShardId|
|cacheWrite|Scrittura nella cache|Byte al secondo|Massima||ShardId|
|cacheRead|Lettura da cache|Byte al secondo|Massima||ShardId|
|percentProcessorTime|CPU|Percentuale|Massima||ShardId|
|cacheLatency|Microsecondi di latenza della cache (anteprima)|Numero|Media||ShardId, SampleType|
|errors|Errori|Numero|Massima||ShardId, ErrorType|
|connectedclients0|Client connessi (partizione 0)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Numero|Totale||Nessuna dimensione|
|cachehits0|Riscontri cache (partizione 0)|Numero|Totale||Nessuna dimensione|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Numero|Totale||Nessuna dimensione|
|getcommands0|Operazioni Get (partizione 0)|Numero|Totale||Nessuna dimensione|
|setcommands0|Operazioni Set (partizione 0)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond0|Operazioni al secondo (partizione 0)|Numero|Massima||Nessuna dimensione|
|evictedkeys0|Chiavi rimosse (partizione 0)|Numero|Totale||Nessuna dimensione|
|totalkeys0|Totale chiavi (partizione 0)|Numero|Massima||Nessuna dimensione|
|expiredkeys0|Chiavi scadute (partizione 0)|Numero|Totale||Nessuna dimensione|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massima||Nessuna dimensione|
|serverLoad0|Carico server (partizione 0)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massima||Nessuna dimensione|
|connectedclients1|Client connessi (partizione 1)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Numero|Totale||Nessuna dimensione|
|cachehits1|Riscontri cache (partizione 1)|Numero|Totale||Nessuna dimensione|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Numero|Totale||Nessuna dimensione|
|getcommands1|Operazioni Get (partizione 1)|Numero|Totale||Nessuna dimensione|
|setcommands1|Operazioni Set (partizione 1)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond1|Operazioni al secondo (partizione 1)|Numero|Massima||Nessuna dimensione|
|evictedkeys1|Chiavi rimosse (partizione 1)|Numero|Totale||Nessuna dimensione|
|totalkeys1|Totale chiavi (partizione 1)|Numero|Massima||Nessuna dimensione|
|expiredkeys1|Chiavi scadute (partizione 1)|Numero|Totale||Nessuna dimensione|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massima||Nessuna dimensione|
|serverLoad1|Carico server (partizione 1)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massima||Nessuna dimensione|
|connectedclients2|Client connessi (partizione 2)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Numero|Totale||Nessuna dimensione|
|cachehits2|Riscontri cache (partizione 2)|Numero|Totale||Nessuna dimensione|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Numero|Totale||Nessuna dimensione|
|getcommands2|Operazioni Get (partizione 2)|Numero|Totale||Nessuna dimensione|
|setcommands2|Operazioni Set (partizione 2)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond2|Operazioni al secondo (partizione 2)|Numero|Massima||Nessuna dimensione|
|evictedkeys2|Chiavi rimosse (partizione 2)|Numero|Totale||Nessuna dimensione|
|totalkeys2|Totale chiavi (partizione 2)|Numero|Massima||Nessuna dimensione|
|expiredkeys2|Chiavi scadute (partizione 2)|Numero|Totale||Nessuna dimensione|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massima||Nessuna dimensione|
|serverLoad2|Carico server (partizione 2)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massima||Nessuna dimensione|
|connectedclients3|Client connessi (partizione 3)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Numero|Totale||Nessuna dimensione|
|cachehits3|Riscontri cache (partizione 3)|Numero|Totale||Nessuna dimensione|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Numero|Totale||Nessuna dimensione|
|getcommands3|Operazioni Get (partizione 3)|Numero|Totale||Nessuna dimensione|
|setcommands3|Operazioni Set (partizione 3)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond3|Operazioni al secondo (partizione 3)|Numero|Massima||Nessuna dimensione|
|evictedkeys3|Chiavi rimosse (partizione 3)|Numero|Totale||Nessuna dimensione|
|totalkeys3|Totale chiavi (partizione 3)|Numero|Massima||Nessuna dimensione|
|expiredkeys3|Chiavi scadute (partizione 3)|Numero|Totale||Nessuna dimensione|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massima||Nessuna dimensione|
|serverLoad3|Carico server (partizione 3)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massima||Nessuna dimensione|
|connectedclients4|Client connessi (partizione 4)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Numero|Totale||Nessuna dimensione|
|cachehits4|Riscontri cache (partizione 4)|Numero|Totale||Nessuna dimensione|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Numero|Totale||Nessuna dimensione|
|getcommands4|Operazioni Get (partizione 4)|Numero|Totale||Nessuna dimensione|
|setcommands4|Operazioni Set (partizione 4)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond4|Operazioni al secondo (partizione 4)|Numero|Massima||Nessuna dimensione|
|evictedkeys4|Chiavi rimosse (partizione 4)|Numero|Totale||Nessuna dimensione|
|totalkeys4|Totale chiavi (partizione 4)|Numero|Massima||Nessuna dimensione|
|expiredkeys4|Chiavi scadute (partizione 4)|Numero|Totale||Nessuna dimensione|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massima||Nessuna dimensione|
|serverLoad4|Carico server (partizione 4)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massima||Nessuna dimensione|
|connectedclients5|Client connessi (partizione 5)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Numero|Totale||Nessuna dimensione|
|cachehits5|Riscontri cache (partizione 5)|Numero|Totale||Nessuna dimensione|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Numero|Totale||Nessuna dimensione|
|getcommands5|Operazioni Get (partizione 5)|Numero|Totale||Nessuna dimensione|
|setcommands5|Operazioni Set (partizione 5)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond5|Operazioni al secondo (partizione 5)|Numero|Massima||Nessuna dimensione|
|evictedkeys5|Chiavi rimosse (partizione 5)|Numero|Totale||Nessuna dimensione|
|totalkeys5|Totale chiavi (partizione 5)|Numero|Massima||Nessuna dimensione|
|expiredkeys5|Chiavi scadute (partizione 5)|Numero|Totale||Nessuna dimensione|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massima||Nessuna dimensione|
|serverLoad5|Carico server (partizione 5)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massima||Nessuna dimensione|
|connectedclients6|Client connessi (partizione 6)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Numero|Totale||Nessuna dimensione|
|cachehits6|Riscontri cache (partizione 6)|Numero|Totale||Nessuna dimensione|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Numero|Totale||Nessuna dimensione|
|getcommands6|Operazioni Get (partizione 6)|Numero|Totale||Nessuna dimensione|
|setcommands6|Operazioni Set (partizione 6)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond6|Operazioni al secondo (partizione 6)|Numero|Massima||Nessuna dimensione|
|evictedkeys6|Chiavi rimosse (partizione 6)|Numero|Totale||Nessuna dimensione|
|totalkeys6|Totale chiavi (partizione 6)|Numero|Massima||Nessuna dimensione|
|expiredkeys6|Chiavi scadute (partizione 6)|Numero|Totale||Nessuna dimensione|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massima||Nessuna dimensione|
|serverLoad6|Carico server (partizione 6)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massima||Nessuna dimensione|
|connectedclients7|Client connessi (partizione 7)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Numero|Totale||Nessuna dimensione|
|cachehits7|Riscontri cache (partizione 7)|Numero|Totale||Nessuna dimensione|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Numero|Totale||Nessuna dimensione|
|getcommands7|Operazioni Get (partizione 7)|Numero|Totale||Nessuna dimensione|
|setcommands7|Operazioni Set (partizione 7)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond7|Operazioni al secondo (partizione 7)|Numero|Massima||Nessuna dimensione|
|evictedkeys7|Chiavi rimosse (partizione 7)|Numero|Totale||Nessuna dimensione|
|totalkeys7|Totale chiavi (partizione 7)|Numero|Massima||Nessuna dimensione|
|expiredkeys7|Chiavi scadute (partizione 7)|Numero|Totale||Nessuna dimensione|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massima||Nessuna dimensione|
|serverLoad7|Carico server (partizione 7)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massima||Nessuna dimensione|
|connectedclients8|Client connessi (partizione 8)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Numero|Totale||Nessuna dimensione|
|cachehits8|Riscontri cache (partizione 8)|Numero|Totale||Nessuna dimensione|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Numero|Totale||Nessuna dimensione|
|getcommands8|Operazioni Get (partizione 8)|Numero|Totale||Nessuna dimensione|
|setcommands8|Operazioni Set (partizione 8)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond8|Operazioni al secondo (partizione 8)|Numero|Massima||Nessuna dimensione|
|evictedkeys8|Chiavi rimosse (partizione 8)|Numero|Totale||Nessuna dimensione|
|totalkeys8|Totale chiavi (partizione 8)|Numero|Massima||Nessuna dimensione|
|expiredkeys8|Chiavi scadute (partizione 8)|Numero|Totale||Nessuna dimensione|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massima||Nessuna dimensione|
|serverLoad8|Carico server (partizione 8)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massima||Nessuna dimensione|
|connectedclients9|Client connessi (partizione 9)|Numero|Massima||Nessuna dimensione|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Numero|Totale||Nessuna dimensione|
|cachehits9|Riscontri cache (partizione 9)|Numero|Totale||Nessuna dimensione|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Numero|Totale||Nessuna dimensione|
|getcommands9|Operazioni Get (partizione 9)|Numero|Totale||Nessuna dimensione|
|setcommands9|Operazioni Set (partizione 9)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond9|Operazioni al secondo (partizione 9)|Numero|Massima||Nessuna dimensione|
|evictedkeys9|Chiavi rimosse (partizione 9)|Numero|Totale||Nessuna dimensione|
|totalkeys9|Totale chiavi (partizione 9)|Numero|Massima||Nessuna dimensione|
|expiredkeys9|Chiavi scadute (partizione 9)|Numero|Totale||Nessuna dimensione|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massima||Nessuna dimensione|
|serverLoad9|Carico server (partizione 9)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massima||Nessuna dimensione|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Network In|Network In|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Network Out|Network Out|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|Nessuna dimensione|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|Nessuna dimensione|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|
|Network In|Network In|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Network Out|Network Out|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalCalls|Totale chiamate|Numero|Totale|Numero totale di chiamate.|ApiName, OperationName, Region|
|SuccessfulCalls|Chiamate riuscite|Numero|Totale|Numero di chiamate riuscite.|ApiName, OperationName, Region|
|TotalErrors|Totale errori|Numero|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Chiamate bloccate|Numero|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|ApiName, OperationName, Region|
|ServerErrors|Errori server|Numero|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|ApiName, OperationName, Region|
|ClientErrors|Errori client|Numero|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|ApiName, OperationName, Region|
|DataOut|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|ApiName, OperationName, Region|
|Latenza|Latenza|Millisecondi|Media|Latenza in millisecondi.|ApiName, OperationName, Region|
|CharactersTranslated|Caratteri convertiti|Numero|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|ApiName, OperationName, Region|
|CharactersTrained|Caratteri sottoposti a training|Numero|Totale|Numero totale di caratteri sottoposti a training.|ApiName, OperationName, Region|
|SpeechSessionDuration|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|ApiName, OperationName, Region|
|TotalTransactions|Totale transazioni|Numero|Totale|Numero totale di transazioni.|Nessuna dimensione|
|TotalTokenCalls|Totale chiamate token|Numero|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Network In|Rete in fatturabile|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Network Out|Rete in uscita fatturabile|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Disk Read Bytes|Disk Read Bytes|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Disk Write Bytes|Disk Write Bytes|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuna dimensione|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Numero|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU usati|Crediti CPU usati|Numero|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Numero|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Numero|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità della coda del disco dati|Profondità coda del disco dati (anteprima)|Numero|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Profondità coda del disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Numero|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Flussi in ingresso|Flussi in ingresso (anteprima)|Numero|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuna dimensione|
|Flussi in uscita|Flussi in uscita (anteprima)|Numero|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuna dimensione|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|Nessuna dimensione|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|Nessuna dimensione|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Network In|Rete in fatturabile|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Network Out|Rete in uscita fatturabile|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|
|Disk Read Bytes|Disk Read Bytes|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Disk Write Bytes|Disk Write Bytes|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Numero|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU usati|Crediti CPU usati|Numero|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Numero|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Numero|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Profondità della coda del disco dati|Profondità coda del disco dati (anteprima)|Numero|Media|Profondità coda del disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Profondità coda del disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Numero|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
|Flussi in ingresso|Flussi in ingresso (anteprima)|Numero|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|VMName|
|Flussi in uscita|Flussi in uscita (anteprima)|Numero|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|VMName|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN, VMName|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN, VMName|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|VMName|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|VMName|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo di CPU|Numero|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|Nessuna dimensione|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|Nessuna dimensione|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalPullCount|Conteggio pull totale|Numero|Media|Numero totale di pull di immagini|Nessuna dimensione|
|SuccessfulPullCount|Conteggio pull riuscito|Numero|Media|Numero di pull di immagini riuscite|Nessuna dimensione|
|TotalPushCount|Numero totale di push|Numero|Media|Numero totale di push di immagini|Nessuna dimensione|
|SuccessfulPushCount|Conteggio push riuscito|Numero|Media|Numero di push di immagini riusciti|Nessuna dimensione|
|RunDuration|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|Nessuna dimensione|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster (Numero totale di core CPU disponibili in un cluster gestito)|Numero|Totale|Total number of available cpu cores in a managed cluster (Numero totale di core CPU disponibili in un cluster gestito)|Nessuna dimensione|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster (Numero totale di memoria disponibile in un cluster gestito)|Byte|Totale|Total amount of available memory in a managed cluster (Numero totale di memoria disponibile in un cluster gestito)|Nessuna dimensione|
|kube_pod_status_ready|Number of pods in Ready state (Numero di pod in stato Pronto)|Numero|Totale|Number of pods in Ready state (Numero di pod in stato Pronto)|spazio dei nomi, pod|
|kube_node_status_condition|Statuses for various node conditions (Stati per diverse condizioni dei nodi)|Numero|Totale|Statuses for various node conditions (Stati per diverse condizioni dei nodi)|Condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase (Numero di pod per fase)|Numero|Totale|Number of pods by phase (Numero di pod per fase)|fase, spazio dei nomi, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|DCIApiCalls|Chiamate all'API di Customer Insights|Numero|Totale||Nessuna dimensione|
|DCIMappingImportOperationSuccessfulLines|Righe riuscite con l'operazione di importazione mapping|Numero|Totale||Nessuna dimensione|
|DCIMappingImportOperationFailedLines|Righe non riuscite con l'operazione di importazione mapping|Numero|Totale||Nessuna dimensione|
|DCIMappingImportOperationTotalLines|Righe totali dell'operazione di importazione mapping|Numero|Totale||Nessuna dimensione|
|DCIMappingImportOperationRuntimeInSeconds|Runtime dell'operazione di importazione mapping in secondi|Secondi|Totale||Nessuna dimensione|
|DCIOutboundProfileExportSucceeded|Esportazione profili in uscita riuscita|Numero|Totale||Nessuna dimensione|
|DCIOutboundProfileExportFailed|Esportazione profili in uscita non riuscita|Numero|Totale||Nessuna dimensione|
|DCIOutboundProfileExportDuration|Durata esportazione profili in uscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundKpiExportSucceeded|Esportazione di KPI in uscita riuscita|Numero|Totale||Nessuna dimensione|
|DCIOutboundKpiExportFailed|Esportazione di KPI in uscita non riuscita|Numero|Totale||Nessuna dimensione|
|DCIOutboundKpiExportDuration|Durata esportazione KPI in uscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundKpiExportStarted|Esportazione KPI in uscita avviata|Secondi|Totale||Nessuna dimensione|
|DCIOutboundKpiRecordCount|Numero di record KPI in uscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundProfileExportCount|Numero di esportazioni profili in uscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundInitialProfileExportFailed|Esportazione profili iniziali in uscita non riuscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundInitialProfileExportSucceeded|Esportazione profili iniziali in uscita riuscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundInitialKpiExportFailed|Esportazione KPI iniziali in uscita non riuscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundInitialKpiExportSucceeded|Esportazione KPI iniziali in uscita riuscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundInitialProfileExportDurationInSeconds|Durata esportazione profili iniziali in uscita in secondi|Secondi|Totale||Nessuna dimensione|
|AdlaJobForStandardKpiFailed|Processo Adla per Kpi standard non riuscito in secondi|Secondi|Totale||Nessuna dimensione|
|AdlaJobForStandardKpiTimeOut|Timeout processo Adla per Kpi standard in secondi|Secondi|Totale||Nessuna dimensione|
|AdlaJobForStandardKpiCompleted|Processo Adla per Kpi standard completato in secondi|Secondi|Totale||Nessuna dimensione|
|ImportASAValuesFailed|Numero importazioni valori ASA non riuscite|Numero|Totale||Nessuna dimensione|
|ImportASAValuesSucceeded|Numero importazioni valori ASA riuscite|Numero|Totale||Nessuna dimensione|
|DCIProfilesCount|Numero di istanze del profilo|Numero|Last (Ultimo)||Nessuna dimensione|
|DCIInteractionsPerMonthCount|Numero di interazioni per mese|Numero|Last (Ultimo)||Nessuna dimensione|
|DCIKpisCount|Numero di indicatori KPI|Numero|Last (Ultimo)||Nessuna dimensione|
|DCISegmentsCount|Numero di segmenti|Numero|Last (Ultimo)||Nessuna dimensione|
|DCIPredictiveMatchPoliciesCount|Numero di corrispondenze predittive|Numero|Last (Ultimo)||Nessuna dimensione|
|DCIPredictionsCount|Numero di stime|Numero|Last (Ultimo)||Nessuna dimensione|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva di lettura (rete)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettiva di download del cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|CloudUploadThroughputPerShare|Velocità effettiva di caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|BytesUploadedToCloudPerShare|Byte cloud caricati (condivisione)|Byte|Media|Il numero totale di byte caricati in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|Archiviazione|Capacità totale|Byte|Media|Capacità totale|Nessuna dimensione|
|AvailableCapacity|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di Reporting.|Nessuna dimensione|
|CloudUploadThroughput|Velocità effettiva di caricamento cloud|Byte al secondo|Media|La velocità effettiva di caricamento nel cloud in Azure durante il periodo di Reporting.|Nessuna dimensione|
|CloudReadThroughput|Velocità effettiva di download del cloud|Byte al secondo|Media|La velocità effettiva di download del cloud in Azure durante il periodo di Reporting.|Nessuna dimensione|
|BytesUploadedToCloud|Byte cloud caricati (dispositivo)|Byte|Media|Il numero totale di byte caricati in Azure da un dispositivo durante il periodo di Reporting.|Nessuna dimensione|
|HyperVVirtualProcessorUtilization|Calcolo Edge-percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|HyperVMemoryUtilization|Calcolo Edge-utilizzo memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Numero|Totale||PipelineName, ActivityName|
|SuccessfulRuns|Esecuzioni riuscite|Numero|Totale||PipelineName, ActivityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Numero|Totale||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Numero|Totale||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Numero|Totale||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Numero|Totale||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Numero|Totale||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Numero|Totale||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Uso della CPU di Integration runtime|Percentuale|Media||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Conteggio entità massime consentite|Numero|Massima||Nessuna dimensione|
|MaxAllowedFactorySizeInGbUnits|Dimensioni massime consentite per le factory (unità GB)|Numero|Massima||Nessuna dimensione|
|ResourceCount|Conteggio entità totali|Numero|Massima||Nessuna dimensione|
|FactorySizeInGbUnits|Dimensioni totali Factory (unità GB)|Numero|Massima||Nessuna dimensione|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Numero|Totale|Numero di processi completati|Nessuna dimensione|
|JobEndedFailure|Processi non riusciti|Numero|Totale|Numero di processi non riusciti|Nessuna dimensione|
|JobEndedCancelled|Processi annullati|Numero|Totale|Numero di processi annullati.|Nessuna dimensione|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|Nessuna dimensione|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|Nessuna dimensione|
|JobAUEndedCancelled|Tempo AU annullato|Secondi|Totale|Tempo totale AU per i processi annullati.|Nessuna dimensione|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massima|Quantità totale di dati archiviati nell'account|Nessuna dimensione|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuna dimensione|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuna dimensione|
|WriteRequests|Richieste di scrittura|Numero|Totale|Numero di richieste di scrittura dati all'account|Nessuna dimensione|
|ReadRequests|Richieste di lettura|Numero|Totale|Numero di richieste di lettura dati all'account|Nessuna dimensione|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Server Log storage percent (Percentuale archiviazione log server)|Percentuale|Media|Server Log storage percent (Percentuale archiviazione log server)|Nessuna dimensione|
|serverlog_storage_usage|Server Log storage used (Archiviazione log server usata)|Byte|Media|Server Log storage used (Archiviazione log server usata)|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Numero|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Numero|Totale|Connessioni non riuscite|Nessuna dimensione|
|seconds_behind_master|Intervallo di replica in secondi|Numero|Media|Intervallo di replica in secondi|Nessuna dimensione|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuna dimensione|
|network_bytes_egress|Network Out|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Network In|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Server Log storage percent (Percentuale archiviazione log server)|Percentuale|Media|Server Log storage percent (Percentuale archiviazione log server)|Nessuna dimensione|
|serverlog_storage_usage|Server Log storage used (Archiviazione log server usata)|Byte|Media|Server Log storage used (Archiviazione log server usata)|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Numero|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Numero|Totale|Connessioni non riuscite|Nessuna dimensione|
|seconds_behind_master|Intervallo di replica in secondi|Numero|Media|Intervallo di replica in secondi|Nessuna dimensione|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuna dimensione|
|network_bytes_egress|Network Out|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Network In|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Server Log storage percent (Percentuale archiviazione log server)|Percentuale|Media|Server Log storage percent (Percentuale archiviazione log server)|Nessuna dimensione|
|serverlog_storage_usage|Server Log storage used (Archiviazione log server usata)|Byte|Media|Server Log storage used (Archiviazione log server usata)|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Numero|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Numero|Totale|Connessioni non riuscite|Nessuna dimensione|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuna dimensione|
|network_bytes_egress|Network Out|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Network In|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massima|Ritardo replica in secondi|Nessuna dimensione|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massima|Ritardo in byte della replica più in ritardo|Nessuna dimensione|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|IOPS|IOPS|Numero|Media|Operazioni di i/o al secondo|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|active_connections|Connessioni attive|Numero|Media|Connessioni attive|Nessuna dimensione|
|network_bytes_egress|Network Out|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Network In|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Numero|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|c2d.commands.egress.complete.success|Comandi completati|Numero|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Numero|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.reject.success|Comandi rifiutati|Numero|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|devices.totalDevices|Dispositivi totali (deprecati)|Numero|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Numero|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Numero|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|Nessuna dimensione|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Numero|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|Nessuna dimensione|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Numero|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |Nessuna dimensione|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Numero|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|Nessuna dimensione|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|Nessuna dimensione|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi). Questa metrica inizia a funzionare solo quando il routing è abilitato (https://aka.ms/iotrouting) per l'hub Internet.|Nessuna dimensione|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi). Questa metrica inizia a funzionare solo quando il routing è abilitato (https://aka.ms/iotrouting) per l'hub Internet.|Nessuna dimensione|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Numero|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Numero|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|Nessuna dimensione|
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Numero|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento (https://aka.ms/ioteventgrid).|Risultato, EventType|
|EventGridLatency|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|EventType|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|Nessuna dimensione|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|Nessuna dimensione|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Numero|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Numero|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|Nessuna dimensione|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Numero|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|Nessuna dimensione|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Numero|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|Nessuna dimensione|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|twinQueries.success|Query dei dispositivi gemelli completate|Numero|Totale|Numero di tutte le query dei dispositivi gemelli completate.|Nessuna dimensione|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Numero|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|Nessuna dimensione|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Numero|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|Nessuna dimensione|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Numero|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Numero|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|Nessuna dimensione|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Numero|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|Nessuna dimensione|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Numero|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|Nessuna dimensione|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Numero|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|Nessuna dimensione|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Numero|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|Nessuna dimensione|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Numero|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|Nessuna dimensione|
|jobs.queryJobs.success|Query sui processi riuscite|Numero|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|Nessuna dimensione|
|jobs.queryJobs.failure|Query sui processi non riuscite|Numero|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|Nessuna dimensione|
|jobs.completed|Processi completati|Numero|Totale|Numero di tutti i processi completati.|Nessuna dimensione|
|jobs.failed|Processi non riusciti|Numero|Totale|Numero di tutti i processi non riusciti.|Nessuna dimensione|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Numero|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|Nessuna dimensione|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Numero|Media|Numero totale di messaggi usati nella data odierna. Si tratta di un valore cumulativo che viene reimpostato su zero alle 00:00 UTC ogni giorno.|Nessuna dimensione|
|deviceDataUsage|Utilizzo totale dei dati del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|totalDeviceCount|Dispositivi totali (anteprima)|Numero|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Numero|Media|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|configurazioni|Metriche di configurazione|Numero|Totale|Metriche per le operazioni di configurazione|Nessuna dimensione|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Numero|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivi assegnati|Numero|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Numero|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AvailableStorage|Spazio di archiviazione disponibile|Byte|Totale|Totale spazio di archiviazione disponibile segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Numero|Totale|Numero di connessioni Cassandra chiuse, segnalate a una granularità di 1 minuto|Region, ClosureReason|
|CassandraRequestCharges|Addebiti richieste Cassandra|Numero|Totale|Ur utilizzate per le richieste Cassandra effettuate|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Richieste di Cassandra|Numero|Numero|Numero di richieste Cassandra effettuate|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DocumentCount|Conteggio documenti|Numero|Totale|Numero totale di documenti segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DocumentQuota|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalata con granularità di 5 minuti|CollectionName, DatabaseName, Region|
|IndexUsage|Utilizzo indice|Byte|Totale|Totale utilizzo di indici restituiti a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests (Richieste di metadati)|Numero|Numero|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, che consente di enumerare le raccolte, i database e così via e le relative configurazioni, gratuitamente.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge (Addebito richiesta Mongo)|Numero|Totale|Unità richiesta Mongo utilizzate|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Richieste Mongo|Numero|Numero|Numero di richieste Mongo eseguite|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Velocità effettiva sottoposta a provisioning|Numero|Massima|Velocità effettiva sottoposta a provisioning|DatabaseName, CollectionName|
|ReplicationLatency|Latenza di replica P99|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion, TargetRegion|
|ServiceAvailability|Disponibilità del servizio|Percentuale|Media|L'account richiede disponibilità a una granularità di un'ora, giorno o mese|Nessuna dimensione|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Numero|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totale richieste|Numero|Numero|Numero di richieste eseguite|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Numero|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishFailCount|Pubblica eventi non riusciti|Numero|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|UnmatchedEventCount|Eventi senza corrispondenza|Numero|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Numero|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Numero|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuna dimensione|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Numero|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Error, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Numero|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuna dimensione|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuna dimensione|
|DroppedEventCount|Eventi eliminati|Numero|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Numero|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Numero|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishFailCount|Eventi non riusciti|Numero|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|UnmatchedEventCount|Eventi senza corrispondenza|Numero|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Numero|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Numero|Totale|Richieste riuscite per Microsoft.EventHub.|EntityName, |
|ServerErrors|Errori server.|Numero|Totale|Errori del server per Microsoft.EventHub.|EntityName, |
|UserErrors|Errori utente.|Numero|Totale|Errori utente per Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Errori di superamento quota.|Numero|Totale|Errori di superamento quota per Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Richieste limitate.|Numero|Totale|Richieste limitate per Microsoft.EventHub.|EntityName, |
|IncomingRequests|Richieste in ingresso|Numero|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Messaggi in ingresso|Numero|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messaggi in uscita|Numero|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Numero|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuna dimensione|
|ConnectionsOpened|Connessioni aperte.|Numero|Media|Connessioni aperte per Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Numero|Media|Connessioni chiuse per Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog acquisiti.|Numero|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|Messaggi acquisiti.|Numero|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (deprecate)|Numero|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|SUCCREQ|Richieste riuscite (deprecate)|Numero|Totale|Totale richieste riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|FAILREQ|Richieste non riuscite (deprecate)|Numero|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|SVRBSY|Errori di server occupato (deprecato)|Numero|Totale|Totale errori server occupati per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|INTERR|Errori interni del server (deprecati)|Numero|Totale|Totale errori interni del server per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|MISCERR|Altri errori (deprecato)|Numero|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|INMSGS|Messaggi in ingresso (deprecati) (deprecato)|Numero|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in ingresso (deprecata)|Nessuna dimensione|
|EHINMSGS|Messaggi in ingresso (deprecata)|Numero|Totale|Totale messaggi in ingresso per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|OUTMSGS|Messaggi in uscita (deprecati) (deprecato)|Numero|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in uscita (deprecata)|Nessuna dimensione|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Numero|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHINMBS|Byte in ingresso (deprecato) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in ingresso (deprecata)|Nessuna dimensione|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva del messaggio in ingresso dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHOUTMBS|Byte in uscita (deprecato) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in uscita (deprecata)|Nessuna dimensione|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva del messaggio in uscita dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHABL|Archivia messaggi backlog (deprecato)|Numero|Totale|Messaggi di archiviazione dell'hub eventi nel backlog per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHAMSGS|Messaggi di archivio (deprecati)|Numero|Totale|Messaggi archiviati dell'hub eventi in uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHAMBS|Archiviazione della velocità effettiva del messaggio (deprecata)|Byte|Totale|Velocità effettiva del messaggio archiviato nell'hub eventi in uno spazio dei nomi (deprecato)|Nessuna dimensione|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste completate (anteprima)|Numero|Totale|Richieste riuscite per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ServerErrors|Errori server. (Anteprima)|Numero|Totale|Errori del server per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|UserErrors|Errori utente. (Anteprima)|Numero|Totale|Errori utente per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|QuotaExceededErrors|Errori di superamento quota. (Anteprima)|Numero|Totale|Errori di superamento quota per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ThrottledRequests|Richieste limitate. (Anteprima)|Numero|Totale|Richieste limitate per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingRequests|Richieste in ingresso (anteprima)|Numero|Totale|Richieste in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingMessages|Messaggi in ingresso (anteprima)|Numero|Totale|Messaggi in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|OutgoingMessages|Messaggi in uscita (anteprima)|Numero|Totale|Messaggi in uscita per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingBytes|Byte in ingresso. (Anteprima)|Byte|Totale|Byte in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|OutgoingBytes|Byte in uscita. (Anteprima)|Byte|Totale|Byte in uscita per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ActiveConnections|ActiveConnections (anteprima)|Numero|Media|Totale connessioni attive per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ConnectionsOpened|Connessioni aperte. (Anteprima)|Numero|Media|Connessioni aperte per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ConnectionsClosed|Connessioni chiuse. (Anteprima)|Numero|Media|Connessioni chiuse per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CaptureBacklog|Backlog acquisiti. (Anteprima)|Numero|Totale|Backlog acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CapturedMessages|Messaggi acquisiti. (Anteprima)|Numero|Totale|Messaggi acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CapturedBytes|Byte acquisiti. (Anteprima)|Byte|Totale|Byte acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CPU|CPU (anteprima)|Percentuale|Massima|Uso CPU per il cluster dell'hub eventi in percentuale|Ruolo|
|AvailableMemory|Memoria disponibile (anteprima)|Numero|Massima|Memoria disponibile per il cluster dell'hub eventi in byte|Ruolo|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Numero|Totale|Numero di richieste del gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Numero|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Numero di thread di lavoro attivi|Numero|Massima|Numero di thread di lavoro attivi|ClusterDnsName, Metricname|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Numero|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Numero|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Numero|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|Nessuna dimensione|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Numero|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Anteprima pubblica)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Availability|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult/Name, availabilityResult/location|
|availabilityResults/count|Test della disponibilità|Numero|Numero|Conteggio dei test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|Nessuna dimensione|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|Nessuna dimensione|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|Nessuna dimensione|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|Nessuna dimensione|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|Nessuna dimensione|
|dependencies/count|Chiamate di dipendenza|Numero|Numero|Numero delle chiamate eseguite dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate eseguite dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Errori delle chiamate di dipendenza|Numero|Numero|Numero di chiamate di dipendenza non riuscite eseguite dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Visualizzazioni pagina|Numero|Numero|Numero delle visualizzazioni di pagina.|operation/synthetic|
|pageViews/duration|Tempo di caricamento della visualizzazione pagina|Millisecondi|Media|Tempo di caricamento della visualizzazione pagina|operation/synthetic|
|performanceCounters/requestExecutionTime|Tempo di esecuzione della richiesta HTTP|Millisecondi|Media|Tempo di esecuzione della richiesta più recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Richieste HTTP nella coda dell'applicazione|Numero|Media|Lunghezza della coda di richieste dell'applicazione.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Frequenza delle richieste HTTP|Conteggio al secondo|Media|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Frequenza di eccezioni|Conteggio al secondo|Media|Conteggio delle eccezioni gestite e non gestite restituite in Windows, incluse le eccezioni .NET e non gestite che vengono convertite in eccezioni .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocità di I/O di elaborazione|Byte al secondo|Media|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU processo|Percentuale|Media|Percentuale di tempo trascorso per l'uso del processore da parte di tutti i thread di processo per eseguire le istruzioni. Il valore può variare tra 0 e 100. Questa metrica indica le prestazioni del solo processo w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo processore|Percentuale|Media|Percentuale di tempo che il processore dedica a thread non inattivi.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memoria disponibile|Byte|Media|Memoria fisica immediatamente disponibile per l'allocazione a un processo o usabile dal sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Byte privati processo|Byte|Media|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.|cloud/roleInstance|
|requests/duration|Tempo di risposta del server|Millisecondi|Media|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Richieste server|Numero|Numero|Numero delle richieste HTTP completate.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Richieste non riuscite|Numero|Numero|Numero delle richieste HTTP contrassegnate come non riuscite. Nella maggior parte dei casi si tratta di richieste con un codice di risposta maggiore o uguale a 400 e diverso da 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|richieste/frequenza|Frequenza richieste server|Conteggio al secondo|Media|Frequenza delle richieste server al secondo|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Eccezioni|Numero|Numero|Numero combinato di tutte le eccezioni non rilevate.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Eccezioni del browser|Numero|Numero|Conteggio delle eccezioni non rilevate generate nel browser.|Nessuna dimensione|
|exceptions/server|Eccezioni del server|Numero|Numero|Numero delle eccezioni non rilevate generate nell'applicazione server.|cloud/roleName, cloud/roleInstance|
|traces/count|Tracce|Numero|Numero|Numero documenti di analisi|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Numero|Numero|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totale dei risultati dell'API del servizio|Numero|Numero|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CacheUtilization|Uso della cache|Percentuale|Media|Livello di uso nell'ambito del cluster|nessuno|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query in secondi|Eseguire query sullo stato|
|IngestionUtilization|Uso dell'inserimento|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|nessuno|
|KeepAlive|Keep-alive|Numero|Media|Test di integrità che indica che il cluster risponde alle query|nessuno|
|IngestionVolumeInMB|Volume di inserimento (in MB)|Numero|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Database|
|IngestionLatencyInSeconds|Latenza di inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|nessuno|
|EventProcessedForEventHubs|Eventi elaborati (per gli hub eventi)|Numero|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi|nessuno|
|IngestionResult|Risultato inserimento|Numero|Numero|Numero di operazioni di inserimento|Stato|
|CPU|CPU|Percentuale|Media|Livello di uso della CPU|nessuno|
| ContinuousExportNumOfRecordsExported | Numero di record esportati nell'esportazione continua | Numero | Totale | Numero di record esportati per ogni artefatto di archiviazione scritto durante l'operazione di esportazione  | nessuno |
| ExportUtilization | Esporta utilizzo | Percentuale | Massima | Esporta utilizzo | nessuno |
| ContinuousExportPendingCount | Conteggio in sospeso esportazione continua | Numero | Massima | Numero di processi di esportazione continua in sospeso pronti per l'esecuzione | nessuno |
| ContinuousExportMaxLatenessMinutes | Minuti di ritardo massimo esportazione continua | Numero | Massima | Tempo massimo in minuti per tutte le esportazioni continue in sospeso e pronte per l'esecuzione | nessuno |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Utilizzo|Utilizzo|Numero|Numero|Numero di chiamate API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsCompleted|Esecuzioni completate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsSucceeded|Esecuzioni riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunsFailed|Esecuzioni non riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsCancelled|Esecuzioni annullate|Numero|Totale|Numero di esecuzioni del flusso di lavoro annullate.|Nessuna dimensione|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Eventi limitati esecuzione|Numero|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuna dimensione|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsStarted|Azioni avviate |Numero|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsCompleted|Azioni completate |Numero|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsSucceeded|Azioni riuscite |Numero|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionsFailed|Azioni non riuscite|Numero|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Azioni ignorate |Numero|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Eventi limitati azione|Numero|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuna dimensione|
|TriggersStarted|Trigger avviati |Numero|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersCompleted|Trigger completati |Numero|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersSucceeded|Trigger riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggersFailed|Trigger non riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersSkipped|Trigger ignorati|Numero|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersFired|Trigger rimossi |Numero|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Eventi limitati trigger|Numero|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuna dimensione|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Numero|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|Nessuna dimensione|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Numero|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|Nessuna dimensione|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Numero|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|Nessuna dimensione|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Numero|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuna dimensione|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Numero|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuna dimensione|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Numero|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|Nessuna dimensione|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Numero|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuna dimensione|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Numero|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuna dimensione|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Numero|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|Nessuna dimensione|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsCompleted|Esecuzioni completate|Numero|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsSucceeded|Esecuzioni riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunsFailed|Esecuzioni non riuscite|Numero|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsCancelled|Esecuzioni annullate|Numero|Totale|Numero di esecuzioni del flusso di lavoro annullate.|Nessuna dimensione|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Eventi limitati esecuzione|Numero|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuna dimensione|
|RunStartThrottledEvents|Eseguire gli eventi di avvio limitato|Numero|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuna dimensione|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsStarted|Azioni avviate |Numero|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsCompleted|Azioni completate |Numero|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsSucceeded|Azioni riuscite |Numero|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionsFailed|Azioni non riuscite |Numero|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Azioni ignorate |Numero|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Eventi limitati azione|Numero|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuna dimensione|
|TriggersStarted|Trigger avviati |Numero|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersCompleted|Trigger completati |Numero|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersSucceeded|Trigger riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggersFailed|Trigger non riusciti |Numero|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersSkipped|Trigger ignorati|Numero|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersFired|Trigger rimossi |Numero|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Eventi limitati trigger|Numero|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente di Integration Services.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente di Integration Services.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo memoria connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Esecuzioni completate|Esecuzioni completate|Numero|Totale|Numero di esecuzioni completate per l'area di lavoro|Scenario|
|Esecuzioni avviate|Esecuzioni avviate|Numero|Totale|Numero di esecuzioni avviate per l'area di lavoro|Scenario|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Numero|Totale|Numero di esecuzioni non riuscite per l'area di lavoro|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Utilizzo|Utilizzo|Numero|Numero|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|
|Availability|Availability|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AverageOtherLatency|Altra latenza media|ms/op|Media|Altra latenza media (non in lettura o scrittura) in millisecondi per operazione|Nessuna dimensione|
|AverageReadLatency|Latenza media in lettura|ms/op|Media|Latenza media in millisecondi per ogni operazione di lettura|Nessuna dimensione|
|AverageTotalLatency|Latenza media totale|ms/op|Media|Latenza media totale in millisecondi per operazione|Nessuna dimensione|
|AverageWriteLatency|Latenza media in scrittura|ms/op|Media|Latenza media in millisecondi per ogni operazione di scrittura|Nessuna dimensione|
|FilesystemOtherOps|Altre operazioni di file system|ops|Media|Numero di altre operazioni di file system (né lettura né scrittura)|Nessuna dimensione|
|FilesystemReadOps|Operazioni di lettura del file system|ops|Media|Numero di operazioni di lettura del file system|Nessuna dimensione|
|FilesystemTotalOps|Operazioni totali del file system|ops|Media|Somma di tutte le operazioni del file system|Nessuna dimensione|
|FilesystemWriteOps|Operazioni di scrittura del file system|ops|Media|Numero di operazioni di scrittura del file system|Nessuna dimensione|
|IoBytesPerOtherOps|Byte di I/O per altre operazioni|bytes/op|Media|Numero di byte di I/O per altre operazioni (né in lettura né in scrittura)|Nessuna dimensione|
|IoBytesPerReadOps|Byte di I/O per operazioni di lettura|bytes/op|Media|Numero di byte di I/O per operazione di lettura|Nessuna dimensione|
|IoBytesPerTotalOps|Byte di I/O per operazione per tutte le operazioni|bytes/op|Media|Somma di tutti i byte di I/O per operazione|Nessuna dimensione|
|IoBytesPerWriteOps|Byte di I/O per operazioni di scrittura|bytes/op|Media|Numero di byte di I/O per operazione di scrittura|Nessuna dimensione|
|OtherIops|Altre operazioni di I/O|operations/second|Media|Altre operazioni di I/O al secondo|Nessuna dimensione|
|OtherThroughput|Altra velocità effettiva|MBps|Media|Altra velocità effettiva (per operazioni diverse da lettura e scrittura) in megabyte al secondo|Nessuna dimensione|
|ReadIops|Operazioni di I/O in lettura|operations/second|Media|Operazioni di I/O in lettura al secondo|Nessuna dimensione|
|ReadThroughput|Velocità effettiva in lettura|MBps|Media|Velocità effettiva in lettura in megabyte al secondo|Nessuna dimensione|
|TotalIops|Totale operazioni di I/O al secondo|operations/second|Media|Somma di tutte le operazioni di I/O al secondo|Nessuna dimensione|
|TotalThroughput|Velocità effettiva elaborata|MBps|Media|Somma di tutta la velocità effettiva in megabyte al secondo|Nessuna dimensione|
|VolumeAllocatedSize|Dimensioni allocate del volume|byte|Media|Dimensioni allocate del volume (non i byte effettivamente usati)|Nessuna dimensione|
|VolumeLogicalSize|Dimensioni logiche del volume|byte|Media|Dimensioni logiche del volume (byte usati)|Nessuna dimensione|
|VolumeSnapshotSize|Dimensioni dello snapshot del volume|byte|Media|Dimensioni di tutti gli snapshot nel volume|Nessuna dimensione|
|WriteIops|Operazioni di I/O in scrittura|operations/second|Media|Operazioni di I/O in scrittura al secondo|Nessuna dimensione|
|WriteThroughput|Velocità effettiva in scrittura|MBps|Media|Velocità effettiva in scrittura in megabyte al secondo|Nessuna dimensione|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Dimensioni allocate del pool di volumi|byte|Media|Dimensione allocate del pool (non i byte effettivamente usati)|Nessuna dimensione|
|VolumePoolAllocatedUsed|Dimensioni usate del pool di volumi|byte|Media|Dimensioni usate del pool|Nessuna dimensione|
|VolumePoolTotalLogicalSize|Dimensioni logiche totale del pool di volumi|byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|Nessuna dimensione|
|VolumePoolTotalSnapshotSize|Dimensioni totali degli snapshot del pool di volumi|byte|Media|Somma di tutti gli snapshot nel pool|Nessuna dimensione|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Numero|Totale|Numero di byte inviati dall'interfaccia di rete|Nessuna dimensione|
|BytesReceivedRate|Byte ricevuti|Numero|Totale|Numero di byte ricevuti dall'interfaccia di rete|Nessuna dimensione|
|PacketsSentRate|Pacchetti inviati|Numero|Totale|Numero di pacchetti inviati dall'interfaccia di rete|Nessuna dimensione|
|PacketsReceivedRate|Pacchetti ricevuti|Numero|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|Nessuna dimensione|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|VipAvailability|Disponibilità del percorso dati|Numero|Media|Disponibilità media del percorso dati di Load Balancer per periodo di tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Stato probe di integrità|Numero|Media|Stato probe di integrità media di Load Balancer per periodo di tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Numero di byte|Numero|Totale|Numero totale di byte trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Numero di pacchetti|Numero|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Numero di SYN|Numero|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Numero di connessioni SNAT|Numero|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Porte SNAT allocate (anteprima)|Numero|Totale|Numero totale di porte SNAT allocate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Porte SNAT usate (anteprima)|Numero|Totale|Numero totale di porte SNAT usate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Numero|Totale|Numero di query per zona DNS|Nessuna dimensione|
|RecordSetCount|Numero di set di record|Numero|Massima|Numero di set di record in una zona DNS|Nessuna dimensione|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massima|Percentuale della capacità di set di record usata da una zona DNS|Nessuna dimensione|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS pacchetti in ingresso|Conteggio al secondo|Massima|DDoS pacchetti in ingresso|Nessuna dimensione|
|PacketsDroppedDDoS|DDoS pacchetti in ingresso eliminati|Conteggio al secondo|Massima|DDoS pacchetti in ingresso eliminati|Nessuna dimensione|
|PacketsForwardedDDoS|DDoS pacchetti in ingresso inoltrati|Conteggio al secondo|Massima|DDoS pacchetti in ingresso inoltrati|Nessuna dimensione|
|TCPPacketsInDDoS|DDoS pacchetti TCP in ingresso|Conteggio al secondo|Massima|DDoS pacchetti TCP in ingresso|Nessuna dimensione|
|TCPPacketsDroppedDDoS|DDoS pacchetti TCP in ingresso eliminati|Conteggio al secondo|Massima|DDoS pacchetti TCP in ingresso eliminati|Nessuna dimensione|
|TCPPacketsForwardedDDoS|DDoS pacchetti TCP in ingresso inoltrati|Conteggio al secondo|Massima|DDoS pacchetti TCP in ingresso inoltrati|Nessuna dimensione|
|UDPPacketsInDDoS|DDoS pacchetti UDP in ingresso|Conteggio al secondo|Massima|DDoS pacchetti UDP in ingresso|Nessuna dimensione|
|UDPPacketsDroppedDDoS|DDoS pacchetti UDP in ingresso eliminati|Conteggio al secondo|Massima|DDoS pacchetti UDP in ingresso eliminati|Nessuna dimensione|
|UDPPacketsForwardedDDoS|DDoS pacchetti UDP in ingresso inoltrati|Conteggio al secondo|Massima|DDoS pacchetti UDP in ingresso inoltrati|Nessuna dimensione|
|BytesInDDoS|DDoS byte in ingresso|Byte al secondo|Massima|DDoS byte in ingresso|Nessuna dimensione|
|BytesDroppedDDoS|DDoS byte in ingresso eliminati|Byte al secondo|Massima|DDoS byte in ingresso eliminati|Nessuna dimensione|
|BytesForwardedDDoS|DDoS byte in ingresso inoltrati|Byte al secondo|Massima|DDoS byte in ingresso inoltrati|Nessuna dimensione|
|TCPBytesInDDoS|DDoS byte TCP in ingresso|Byte al secondo|Massima|DDoS byte TCP in ingresso|Nessuna dimensione|
|TCPBytesDroppedDDoS|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massima|DDoS byte TCP in ingresso eliminati|Nessuna dimensione|
|TCPBytesForwardedDDoS|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massima|DDoS byte TCP in ingresso inoltrati|Nessuna dimensione|
|UDPBytesInDDoS|DDoS byte UDP in ingresso|Byte al secondo|Massima|DDoS byte UDP in ingresso|Nessuna dimensione|
|UDPBytesDroppedDDoS|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massima|DDoS byte UDP in ingresso eliminati|Nessuna dimensione|
|UDPBytesForwardedDDoS|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massima|DDoS byte UDP in ingresso inoltrati|Nessuna dimensione|
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Numero|Massima|Sotto attacco DDoS o no|Nessuna dimensione|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massima|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massima|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Conteggio al secondo|Massima|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Nessuna dimensione|
|VipAvailability|Disponibilità del percorso dati|Numero|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Port|
|ByteCount|Numero di byte|Numero|Totale|Numero totale di byte trasmessi nel periodo di tempo|Port, Direction|
|PacketCount|Numero di pacchetti|Numero|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Port, Direction|
|SynCount|Numero di SYN|Numero|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ApplicationRuleHit|Numero di passaggi delle regole dell'applicazione|Numero|Totale|Numero di volte in cui sono state raggiunte le regole applicazione|Stato, motivo, protocollo|
|NetworkRuleHit|Numero di passaggi delle regole di rete|Numero|Totale|Numero di volte in cui sono state eseguite le regole di rete|Stato, motivo, protocollo|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Totale|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuna dimensione|
|UnhealthyHostCount|Numero di host non integri|Numero|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Numero di host integri|Numero|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Totale richieste|Numero|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Numero|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Numero|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Connessioni correnti|Numero|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|Nessuna dimensione|
|CapacityUnits|Unità di capacità correnti|Numero|Media|Unità di capacità utilizzate|Nessuna dimensione|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AverageBandwidth|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SBandwidth|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SConnectionCount|Numero di connessioni da punto a sito|Numero|Massima|Numero di connessioni da punto a sito di un gateway|Protocol|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Numero|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Numero|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Numero|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Numero|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Numero|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Numero|Massima|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ProbesFailedPercent|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|Nessuna dimensione|
|AverageRoundtripMs|Tempo medio di round trip (MS)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|Nessuna dimensione|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RequestCount|Numero di richieste|Numero|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Numero di richieste di back-end|Numero|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latenza richiesta di back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|TotalLatency|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentuale di integrità back-end|Percentuale|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Numero di richieste Web Application Firewall|Numero|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|registration.all|Operazioni di registrazione|Numero|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |Nessuna dimensione|
|registration.create|Operazioni di creazione registrazione|Numero|Totale|Numero di tutte le creazioni di registrazioni riuscite.|Nessuna dimensione|
|registration.update|Operazioni di aggiornamento registrazione|Numero|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|Nessuna dimensione|
|registration.get|Operazioni di lettura registrazione|Numero|Totale|Numero di tutte le query su registrazioni riuscite.|Nessuna dimensione|
|registration.delete|Operazioni di eliminazione registrazione|Numero|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|Nessuna dimensione|
|incoming|Messaggi in ingresso|Numero|Totale|Numero di tutte le chiamate all'API di invio riuscite. |Nessuna dimensione|
|incoming.scheduled|Notifiche push pianificate inviate|Numero|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Numero|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
|scheduled.pending|Notifiche pianificate in sospeso|Numero|Totale|Notifiche pianificate in sospeso|Nessuna dimensione|
|installation.all|Operazioni di gestione installazione|Numero|Totale|Operazioni di gestione installazione|Nessuna dimensione|
|installation.get|Ottieni operazioni di installazione|Numero|Totale|Ottieni operazioni di installazione|Nessuna dimensione|
|installation.upsert|Crea o aggiorna operazioni di installazione|Numero|Totale|Crea o aggiorna operazioni di installazione|Nessuna dimensione|
|installation.patch|Operazioni di installazione patch|Numero|Totale|Operazioni di installazione patch|Nessuna dimensione|
|installation.delete|Elimina operazioni di installazione|Numero|Totale|Elimina operazioni di installazione|Nessuna dimensione|
|outgoing.allpns.success|Notifiche riuscite|Numero|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.allpns.invalidpayload|Errori payload|Numero|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|Nessuna dimensione|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Numero|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|Nessuna dimensione|
|outgoing.allpns.channelerror|Errori canale|Numero|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|Nessuna dimensione|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Numero|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|Nessuna dimensione|
|outgoing.wns.success|Notifiche WNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|Nessuna dimensione|
|outgoing.wns.badchannel|Errore canale WNS non valido|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Numero|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|Nessuna dimensione|
|outgoing.wns.throttled|Notifiche WNS limitate|Numero|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Numero|Totale|Windows Live non è raggiungibile.|Nessuna dimensione|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Numero|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|Nessuna dimensione|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Numero|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|Nessuna dimensione|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Numero|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|Nessuna dimensione|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Numero|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|Nessuna dimensione|
|outgoing.wns.channelthrottled|Canale WNS limitato|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nessuna dimensione|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.dropped|Notifiche WNS eliminate|Numero|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.pnserror|Errori WNS|Numero|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|Nessuna dimensione|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Numero|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|Nessuna dimensione|
|outgoing.apns.success|Notifiche APNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.apns.badchannel|Errore canale APNS non valido|Numero|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del protocollo binario APNS: 8. Codice di stato del protocollo HTTP APNS: 400 con "BadDeviceToken").|Nessuna dimensione|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Numero|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|Nessuna dimensione|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Numero|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del protocollo binario APNS: 7).|Nessuna dimensione|
|outgoing.apns.pnserror|Errori APNS|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|Nessuna dimensione|
|outgoing.gcm.success|Notifiche GCM completate|Numero|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|Nessuna dimensione|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|Nessuna dimensione|
|outgoing.gcm.throttled|Notifiche GCM limitate|Numero|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|Nessuna dimensione|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Numero|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|Nessuna dimensione|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Numero|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|Nessuna dimensione|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Numero|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|Nessuna dimensione|
|outgoing.gcm.pnserror|Errori GCM|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|Nessuna dimensione|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|Nessuna dimensione|
|outgoing.mpns.success|Notifiche MPNS completate|Numero|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Numero|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Numero|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|Nessuna dimensione|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Numero|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Numero|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|Nessuna dimensione|
|outgoing.mpns.pnserror|Errori MPNS|Numero|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|Nessuna dimensione|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Numero|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|notificationhub.pushes|Tutte le notifiche in uscita|Numero|Totale|Tutte le notifiche in uscita dell'hub di notifica|Nessuna dimensione|
|incoming.all.requests|Tutte le richieste in ingresso|Numero|Totale|Totale richieste in ingresso per un hub di notifica|Nessuna dimensione|
|incoming.all.failedrequests|Tutte le richieste in ingresso non riuscite|Numero|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|Nessuna dimensione|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Numero|Media|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Numero|Media|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Numero|Media|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Numero|Media|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Byte letti da disco/sec|Numero|Media|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec|Numero|Media|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Numero|Media|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Byte scritti su disco/sec|Numero|Media|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Numero|Media|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Numero|Media|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Numero|Media|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Numero|Media|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Numero|Media|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Numero|Media|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Numero|Media|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Numero|Media|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Numero|Media|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Numero|Media|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Numero|Media|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Numero|Media|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Numero|Media|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Numero|Media|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Numero|Media|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Numero|Media|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Numero|Media|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Numero|Media|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Numero|Media|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Numero|Media|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Numero|Media|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Numero|Media|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Media letture disco/sec|Numero|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Media sec/trasferimento disco|Numero|Media|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Media scritture disco/sec|Numero|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Numero|Media|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Numero|Media|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Numero|Media|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Numero|Media|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Numero|Media|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Numero|Media|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Numero|Media|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Numero|Media|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Numero|Media|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Numero|Media|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Numero|Media|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Numero|Media|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Numero|Media|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Numero|Media|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Numero|Media|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Numero|Media|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processi|Numero|Media|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Numero|Media|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Numero|Media|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Users|Numero|Media|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Media letture disco/sec|Numero|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Media scritture disco/sec|Numero|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Lunghezza corrente coda su disco|Numero|Media|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec|Numero|Media|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Numero|Media|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Numero|Media|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Numero|Media|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Numero|Media|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|MByte disponibili|Numero|Media|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Numero|Media|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Byte ricevuti/sec|Numero|Media|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Byte inviati/sec|Numero|Media|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Totale byte/sec|Numero|Media|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Numero|Media|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Lunghezza coda processore|Numero|Media|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Numero|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Update|Update|Numero|Media|Update|Computer, Product, Classification, UpdateState, Optional, Approved|
|Evento|Evento|Numero|Media|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Numero|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Numero|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Numero|Totale|Connessioni listener riuscite per Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Numero|Totale|Errore del client su connessioni listener per Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Numero|Totale|Errore del server su connessioni listener per Microsoft.Relay.|EntityName|
|Connessioni mittente - Operazione riuscita|Connessioni mittente - Operazione riuscita|Numero|Totale|Connessioni mittente riuscite per Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Numero|Totale|Errore del client su connessioni mittente per Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Numero|Totale|Errore del server su connessioni mittente per Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Numero|Totale|Totale connessioni listener per Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Numero|Totale|Totale richieste connessioni mittente per Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Numero|Totale|Totale connessioni attive per Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Numero|Totale|Totale listener attivi per Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Numero|Totale|Totale byte trasferiti per Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Numero|Totale|Totale disconnessioni listener per Microsoft.Relay.|EntityName|
|Disconnessioni mittente|Disconnessioni mittente|Numero|Totale|Totale disconnessioni mittente per Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuna dimensione|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuna dimensione|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuna dimensione|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste completate (anteprima)|Numero|Totale|Richieste riuscite totali per uno spazio dei nomi (anteprima)|EntityName|
|ServerErrors|Errori server. (Anteprima)|Numero|Totale|Errori del server per Microsoft.ServiceBus. (Anteprima)|EntityName|
|UserErrors|Errori utente. (Anteprima)|Numero|Totale|Errori utente per Microsoft.ServiceBus. (Anteprima)|EntityName|
|ThrottledRequests|Richieste limitate. (Anteprima)|Numero|Totale|Richieste limitate per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingRequests|Richieste in ingresso (anteprima)|Numero|Totale|Richieste in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingMessages|Messaggi in ingresso (anteprima)|Numero|Totale|Messaggi in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|OutgoingMessages|Messaggi in uscita (anteprima)|Numero|Totale|Messaggi in uscita per Microsoft.ServiceBus. (Anteprima)|EntityName|
|ActiveConnections|ActiveConnections (anteprima)|Numero|Totale|Totale connessioni attive per Microsoft.ServiceBus. (Anteprima)|Nessuna dimensione|
|Dimensione|Dimensione (anteprima)|Byte|Media|Dimensioni di una coda o di un argomento in byte. (Anteprima)|EntityName|
|messaggi|Numero di messaggi contenuti in una coda o in un argomento. (Anteprima)|Numero|Media|Numero di messaggi contenuti in una coda o in un argomento. (Anteprima)|EntityName|
|ActiveMessages|Numero di messaggi attivi contenuti in una coda o in un argomento. (Anteprima)|Numero|Media|Numero di messaggi attivi contenuti in una coda o in un argomento. (Anteprima)|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda o in un argomento. (Anteprima)|Numero|Media|Numero di messaggi non recapitabili in una coda o in un argomento. (Anteprima)|EntityName|
|ScheduledMessages|Numero di messaggi pianificati in una coda o un argomento. (Anteprima)|Numero|Media|Numero di messaggi pianificati in una coda o un argomento. (Anteprima)|EntityName|
|CPUXNS|Uso della CPU per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo CPU per spazio dei nomi Premium del bus di servizio|Nessuna dimensione|
|WSXNS|Uso delle dimensioni della memoria per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo memoria per spazio dei nomi Premium del bus di servizio|Nessuna dimensione|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Numero|Media|CPU allocata a questo contenitore in millicore|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Media|Memoria allocata a questo contenitore in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ActualCpu|ActualCpu|Numero|Media|Utilizzo effettivo della CPU in millicore|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Media|Utilizzo effettivo della memoria in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Numero|Media|Stato dell'applicazione Service Fabric mesh|ApplicationName, stato|
|ServiceStatus|ServiceStatus|Numero|Media|Stato di integrità di un servizio nell'applicazione Service Fabric mesh|ApplicationName, stato, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Numero|Media|Stato di integrità di una replica del servizio nell'applicazione Service Fabric mesh|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Numero|Media|Stato del contenitore nell'applicazione Service Fabric mesh|ApplicationName, ServiceName, codepackagename, ServiceReplicaName, stato|
|RestartCount|RestartCount|Numero|Media|Riavviare il conteggio di un contenitore nell'applicazione Service Fabric mesh|ApplicationName, status, ServiceName, ServiceReplicaName, codepackagename|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Numero|Massima|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Numero|Totale|Quantità totale di messaggi.|Nessuna dimensione|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|Nessuna dimensione|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|Nessuna dimensione|
|UserErrors|Errori utente|Percentuale|Massima|Percentuale di errori utente|Nessuna dimensione|
|SystemErrors|Errori di sistema|Percentuale|Massima|Percentuale di errori di sistema|Nessuna dimensione|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato. Non applicabile ai data warehouse.|Nessuna dimensione|
|app_cpu_billed|CPU app fatturata|Numero|Totale|CPU app fatturata. Si applica ai database senza server.|Nessuna dimensione|
|app_cpu_percent|Percentuale CPU app|Percentuale|Media|Percentuale CPU app. Si applica ai database senza server.|Nessuna dimensione|
|app_memory_percent|Percentuale di utilizzo della memoria dell'app|Percentuale|Media|Percentuale di utilizzo della memoria dell'app. Si applica ai database senza server.|Nessuna dimensione|
|blocked_by_firewall|Blocco da parte del firewall|Numero|Totale|Blocco da parte del firewall|Nessuna dimensione|
|cache_hit_percent|Percentuale dei riscontri nella cache|Percentuale|Massima|Percentuale riscontri cache. Si applica solo ai data warehouse.|Nessuna dimensione|
|cache_used_percent|Percentuale della cache utilizzata|Percentuale|Massima|Percentuale di utilizzo della cache. Si applica solo ai data warehouse.|Nessuna dimensione|
|connection_failed|Connessioni non riuscite|Numero|Totale|Connessioni non riuscite|Nessuna dimensione|
|connection_successful|Connessioni riuscite|Numero|Totale|Connessioni riuscite|Nessuna dimensione|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|cpu_limit|Limite CPU|Numero|Media|Limite CPU. Si applica ai database basati su vCore.|Nessuna dimensione|
|cpu_used|CPU utilizzata|Numero|Media|CPU utilizzata. Si applica ai database basati su vCore.|Nessuna dimensione|
|deadlock|Deadlock|Numero|Totale|Deadlock. Non applicabile ai data warehouse.|Nessuna dimensione|
|diff_backup_size_bytes|Dimensioni di archiviazione del backup differenziale|Byte|Massima|Dimensioni di archiviazione del backup differenziale cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuna dimensione|
|dtu_limit|Limite DTU|Numero|Media|Limite DTU. Si applica ai database basati su DTU.|Nessuna dimensione|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai database basati su DTU.|Nessuna dimensione|
|dtu_used|Uso DTU|Numero|Media|DTU usato. Si applica ai database basati su DTU.|Nessuna dimensione|
|dw_cpu_percent|Percentuale CPU a livello di nodo DW|Percentuale|Media|Percentuale CPU a livello di nodo DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Percentuale I/O dati a livello di nodo DW|Percentuale|Media|Percentuale I/O dati a livello di nodo DW|DwLogicalNodeId|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massima|Percentuale di DWU. Si applica solo ai data warehouse.|Nessuna dimensione|
|dwu_limit|Limite DWU|Numero|Massima|Limite DWU. Si applica solo ai data warehouse.|Nessuna dimensione|
|dwu_used|Uso DWU|Numero|Massima|DWU usato. Si applica solo ai data warehouse.|Nessuna dimensione|
|full_backup_size_bytes|Dimensioni di archiviazione del backup completo|Byte|Massima|Dimensioni di archiviazione dei backup completi cumulativi. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuna dimensione|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|Nessuna dimensione|
|log_backup_size_bytes|Dimensioni di archiviazione dei backup del log|Byte|Massima|Dimensioni di archiviazione dei backup del log cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuna dimensione|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale di i/o del log. Non applicabile ai data warehouse.|Nessuna dimensione|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni. Non applicabile ai data warehouse.|Nessuna dimensione|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massima|Questa metrica è un segnaposto e non viene popolata in questo momento.|Nessuna dimensione|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massima|Questa metrica è un segnaposto e non viene popolata in questo momento.|Nessuna dimensione|
|storage|Spazio dati utilizzato|Byte|Massima|Dimensioni totali del database. Non applicabile ai data warehouse.|Nessuna dimensione|
|storage_percent|Percentuale spazio dati utilizzato|Percentuale|Massima|Percentuale dimensioni database. Non applicabile a data warehouse o database con iperscalabilità.|Nessuna dimensione|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Numero|Massima|Dimensioni del file di dati tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Numero|Massima|Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massima|Log percentuale tempdb utilizzato. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro. Non applicabile ai data warehouse.|Nessuna dimensione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria. Non applicabile ai data warehouse.|Nessuna dimensione|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuna dimensione|
|allocated_data_storage_percent|Percentuale allocata spazio dati|Percentuale|Massima|Percentuale allocata spazio dati|Nessuna dimensione|
|cpu_limit|Limite CPU|Numero|Media|Limite CPU. Si applica ai pool elastici basati su vCore.|Nessuna dimensione|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|cpu_used|CPU utilizzata|Numero|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|Nessuna dimensione|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|eDTU_limit|Limite eDTU|Numero|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|eDTU_used|Uso eDTU|Numero|Media|eDTU usato. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuna dimensione|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuna dimensione|
|storage_limit|Dimensioni massime dati|Byte|Media|Limite archiviazione|Nessuna dimensione|
|storage_percent|Percentuale spazio dati utilizzato||Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Spazio dati utilizzato|Byte|Media|Uso archiviazione|Nessuna dimensione|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massima|Questa metrica è un segnaposto e non viene popolata in questo momento.|Nessuna dimensione|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massima|Questa metrica è un segnaposto e non viene popolata in questo momento.|Nessuna dimensione|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Numero|Massima|Dimensioni del file di dati tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Numero|Massima|Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massima|Log percentuale tempdb utilizzato. Non applicabile ai data warehouse. Questa metrica sarà disponibile per i database che usano il modello di acquisto vCore o 100 DTU e versioni successive per i modelli di acquisto basati su DTU.|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|avg_cpu_percent|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|Nessuna dimensione|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|Nessuna dimensione|
|io_requests|Numero di richieste I/O|Numero|Media|Numero di richieste I/O|Nessuna dimensione|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|Nessuna dimensione|
|reserved_storage_mb|Spazio di archiviazione riservato|Numero|Media|Spazio di archiviazione riservato|Nessuna dimensione|
|storage_space_used_mb|Spazio di archiviazione usato|Numero|Media|Spazio di archiviazione usato|Nessuna dimensione|
|virtual_core_count|Numero di core virtuali|Numero|Media|Numero di core virtuali|Nessuna dimensione|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuna dimensione|
|Transazioni|Transazioni|Numero|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno verso Archiviazione di Azure, oltre ai dati in ingresso entro Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Dati in uscita|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere l'acknowledgment della risposta.|GeoType, ApiName, Authentication|
|Availability|Availability|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Numero|Totale|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType|       |BlobCount|Numero di BLOB|Numero|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Numero|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuna dimensione|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuna dimensione|
|Transazioni|Transazioni|Numero|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno verso Archiviazione di Azure, oltre ai dati in ingresso entro Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Dati in uscita|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere l'acknowledgment della risposta.|GeoType, ApiName, Authentication|
|Availability|Availability|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|Nessuna dimensione|
|FileCount|Numero di file|Numero|Media|Numero di file nel servizio file dell'account di archiviazione.|Nessuna dimensione|
|FileShareCount|Numero di condivisione file|Numero|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Numero|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno verso Archiviazione di Azure, oltre ai dati in ingresso entro Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Dati in uscita|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere l'acknowledgment della risposta.|GeoType, ApiName, Authentication|
|Availability|Availability|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuna dimensione|
|QueueCount|Numero di coda|Numero|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|QueueMessageCount|Numero di messaggi in coda|Numero|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Numero|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno verso Archiviazione di Azure, oltre ai dati in ingresso entro Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Dati in uscita|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere l'acknowledgment della risposta.|GeoType, ApiName, Authentication|
|Availability|Availability|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuna dimensione|
|TableCount|Numero di tabella|Numero|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|TableEntityCount|Numero di entità di tabella|Numero|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Numero|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno verso Archiviazione di Azure, oltre ai dati in ingresso entro Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Dati in uscita|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere l'acknowledgment della risposta.|GeoType, ApiName, Authentication|
|Availability|Availability|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Risultato della sessione di sincronizzazione|Numero|Media|Metrica che registra il valore 1 ogni volta che l'endpoint del server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Numero|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Numero|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stato online del server|Numero|Massima|Metrica che registra il valore 1 ogni volta che il server registrato registra correttamente un heartbeat con l'endpoint cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massima|% utilizzo unità di streaming|LogicalName, PartitionId|
|InputEvents|Eventi di input|Numero|Totale|Eventi di input|LogicalName, PartitionId|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName, PartitionId|
|LateInputEvents|Ultimi eventi di input|Numero|Totale|Ultimi eventi di input|LogicalName, PartitionId|
|OutputEvents|Eventi di output|Numero|Totale|Eventi di output|LogicalName, PartitionId|
|ConversionErrors|Errori di conversione dati|Numero|Totale|Errori di conversione dati|LogicalName, PartitionId|
|Errori|Errori di runtime|Numero|Totale|Errori di runtime|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Eventi non in ordine|Numero|Totale|Eventi non in ordine|LogicalName, PartitionId|
|AMLCalloutRequests|Richieste di funzioni|Numero|Totale|Richieste di funzioni|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Numero|Totale|Richieste di funzioni non riuscite|LogicalName, PartitionId|
|AMLCalloutInputEvents|Eventi di funzioni|Numero|Totale|Eventi di funzioni|LogicalName, PartitionId|
|DeserializationError|Errori di deserializzazione dell'input|Numero|Totale|Errori di deserializzazione dell'input|LogicalName, PartitionId|
|EarlyInputEvents|Eventi di input anticipati|Numero|Totale|Eventi di input anticipati|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Ritardo limite|Secondi|Massima|Ritardo limite|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eventi di input con backlog|Numero|Massima|Eventi di input con backlog|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Origini di input ricevute|Numero|Totale|Origini di input ricevute|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Numero|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Numero|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|Nessuna dimensione|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Numero|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massima|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Numero|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso|Nessuna dimensione|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Numero|Massima|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuna dimensione|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Numero|Massima|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuna dimensione|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Numero|Totale|Numero messaggi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Numero|Totale|Numero di messaggi non validi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|Nessuna dimensione|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Numero|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massima|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Numero|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso|Nessuna dimensione|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Numero|Massima|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuna dimensione|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Numero|Massima|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuna dimensione|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuna dimensione|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di scrittura nel periodo di campionamento.|Nessuna dimensione|
|Disk Read Bytes|Disk Read Bytes|Byte|Totale|Velocità effettiva totale del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuna dimensione|
|Disk Write Bytes|Disk Write Bytes|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|Nessuna dimensione|
|DiskReadOperations|Operazioni di lettura su disco|Numero|Totale|Numero di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskWriteOperations|Operazioni di scrittura su disco|Numero|Totale|Numero di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Conteggio al secondo|Media|Numero medio di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskReadLatency|Latenza lettura disco|Millisecondi|Media|Latenza di lettura totale. Somma delle latenze di lettura del dispositivo e del kernel.|Nessuna dimensione|
|DiskWriteLatency|Latenza scrittura disco|Millisecondi|Media|Latenza di scrittura totale. Somma delle latenze di scrittura del dispositivo e del kernel.|Nessuna dimensione|
|NetworkInBytesPerSecond|Rete in byte/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|Nessuna dimensione|
|NetworkOutBytesPerSecond|Byte di rete in uscita/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|Nessuna dimensione|
|Network In|Network In|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|Nessuna dimensione|
|Network Out|Network Out|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|Nessuna dimensione|
|MemoryUsed|Memoria utilizzata|Byte|Media|Quantità di memoria del computer utilizzata dalla VM.|Nessuna dimensione|
|MemoryGranted|Memoria concessa|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host fino a quando non viene toccata una sola volta e la memoria concessa potrebbe essere invertita o rimossa se il VMkernel richiede la memoria.|Nessuna dimensione|
|MemoryActive|Memoria attiva|Byte|Media|Quantità di memoria usata dalla macchina virtuale in un intervallo di tempo ridotto. Questo è il numero "vero" della quantità di memoria attualmente necessaria per la macchina virtuale. È possibile che venga scambiata una memoria aggiuntiva inutilizzata o che non abbia alcun effetto sulle prestazioni del Guest.|Nessuna dimensione|
|Percentage CPU|Percentage CPU|Percentuale|Media|Utilizzo della CPU. Questo valore viene segnalato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale a 2 vie che usa il 50% di un sistema a quattro core USA completamente due core.|Nessuna dimensione|
|PercentageCpuReady|Percentuale di disponibilità CPU|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento precedente.|Nessuna dimensione|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Numero|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Numero|Media|Lunghezza coda HTTP|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Numero|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Numero|Totale|Http 101|Istanza|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|Istanza|
|Http401|Http 401|Numero|Totale|Http 401|Istanza|
|Http403|Http 403|Numero|Totale|Http 403|Istanza|
|Http404|Http 404|Numero|Totale|Http 404|Istanza|
|Http406|Http 406|Numero|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|AppConnections|Connessioni|Numero|Media|Connessioni|Istanza|
|Handles|Numero di handle|Numero|Media|Numero di handle|Istanza|
|Threads|Thread Count|Numero|Media|Thread Count|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Numero|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Numero|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Numero|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Numero|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Numero|Totale|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Istanza|
|Gen1Collections|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Numero|Totale|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Istanza|
|Gen2Collections|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Numero|Totale|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Istanza|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|MB/millisecondi|Totale|[Unità di esecuzione funzioni](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Numero|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Numero|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Numero|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Numero|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Numero|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Numero|Totale|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Istanza|
|Gen1Collections|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Numero|Totale|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Istanza|
|Gen2Collections|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Numero|Totale|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Istanza|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Numero|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Numero|Totale|Http 101|Istanza|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|Istanza|
|Http401|Http 401|Numero|Totale|Http 401|Istanza|
|Http403|Http 403|Numero|Totale|Http 403|Istanza|
|Http404|Http 404|Numero|Totale|Http 404|Istanza|
|Http406|Http 406|Numero|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Numero|Totale|Unità di esecuzione della funzione|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Numero|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|AppConnections|Connessioni|Numero|Media|Connessioni|Istanza|
|Handles|Numero di handle|Numero|Media|Numero di handle|Istanza|
|Threads|Thread Count|Numero|Media|Thread Count|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Numero|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Numero|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Numero|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Numero|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Numero|Totale|Gen 0 Garbage Collections (Garbage Collection di generazione 0)|Istanza|
|Gen1Collections|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Numero|Totale|Gen 1 Garbage Collections (Garbage Collection di generazione 1)|Istanza|
|Gen2Collections|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Numero|Totale|Gen 2 Garbage Collections (Garbage Collection di generazione 2)|Istanza|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Requests|Requests|Numero|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Numero|Totale|Http 101|Istanza|
|Http2xx|Http 2xx|Numero|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Numero|Totale|Http 3xx|Istanza|
|Http401|Http 401|Numero|Totale|Http 401|Istanza|
|Http403|Http 403|Numero|Totale|Http 403|Istanza|
|Http404|Http 404|Numero|Totale|Http 404|Istanza|
|Http406|Http 406|Numero|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Numero|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Numero|Totale|Errori server HTTP|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Numero|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Numero|Media|Lunghezza coda HTTP|Istanza|
|ActiveRequests|Richieste attive|Numero|Totale|Richieste attive|Istanza|
|TotalFrontEnds|Front end totali|Numero|Media|Front end totali|Nessuna dimensione|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Numero|Media|Ruoli di lavoro piano di servizio app Small|Nessuna dimensione|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Numero|Media|Ruoli di lavoro piano di servizio app Medium|Nessuna dimensione|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Numero|Media|Ruoli di lavoro piano di servizio app Large|Nessuna dimensione|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Numero|Media|Ruoli di lavoro totali|Nessuna dimensione|
|WorkersAvailable|Ruoli di lavoro disponibili|Numero|Media|Ruoli di lavoro disponibili|Nessuna dimensione|
|WorkersUsed|Ruoli di lavoro usati|Numero|Media|Ruoli di lavoro usati|Nessuna dimensione|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|

## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](resource-logs-overview.md)
