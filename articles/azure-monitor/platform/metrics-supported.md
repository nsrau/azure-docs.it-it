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
ms.openlocfilehash: f3907cbc46bd226ef8e90d2061f2d36b2afabf8a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957148"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure. Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. L'elenco riportato di seguito include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Per cercare metriche e per accedervi, usare la [versione API 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica "Messaggi in arrivo" su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|qpu_metric|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|TotalConnectionRequests|Numero totale di richieste di connessione|Conteggio|Media|Numero totale delle richieste di connessione in arrivo.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|CountPerSecond|Media|Numero delle connessioni completate correttamente al secondo.|ServerResourceType|
|TotalConnectionFailures|Numero totale di errori di connessione|Conteggio|Media|Numero totale dei tentativi di connessione non riusciti.|ServerResourceType|
|CurrentUserSessions|Sessioni utente correnti|Conteggio|Media|Numero corrente di sessioni utente attive.|ServerResourceType|
|QueryPoolBusyThreads|Thread occupati pool di query|Conteggio|Media|Numero dei thread occupati nel pool dei thread di query.|ServerResourceType|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Conteggio|Media|Numero dei processi nella coda del pool dei thread dei comandi.|ServerResourceType|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Conteggio|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|CurrentConnections|Connessione: connessioni correnti|Conteggio|Media|Numero corrente delle connessioni client stabilite.|ServerResourceType|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Conteggio|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|MemoryUsage|Memoria: Utilizzo memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria xVelocity (VertiPaq) in eccesso rispetto al limite di memoria del motore xVelocity.|ServerResourceType|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|ServerResourceType|
|Gestione quote|Memoria: Gestione quote|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|ServerResourceType|
|QuotaBlocked|Memoria: quota bloccata|Conteggio|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|ServerResourceType|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|ServerResourceType|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|ServerResourceType|
|RowsReadPerSec|Elaborazione: righe lette al secondo|CountPerSecond|Media|Velocità di lettura delle righe da tutti i database relazionali.|ServerResourceType|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|CountPerSecond|Media|Velocità di conversione delle righe durante l'elaborazione.|ServerResourceType|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|CountPerSecond|Media|Velocità di scrittura delle righe durante l'elaborazione.|ServerResourceType|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Conteggio|Media|Numero dei thread occupati nel pool dei thread dei comandi.|ServerResourceType|
|CommandPoolIdleThreads|Thread: thread inattivi nel pool di comandi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|ServerResourceType|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingIdleThreads|Thread: thread inattivi per analisi dei thread lunghi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Thread: thread non di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Conteggio|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|ServerResourceType|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|QueryPoolJobQueueLength|Thread: lunghezza coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|ServerResourceType|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|memory_thrashing_metric|Thrashing di memoria|Percent|Media|Thrashing di memoria medio.|ServerResourceType|
|mashup_engine_qpu_metric|QPU Motore M|Conteggio|Media|Utilizzo QPU da parte dei processi motore mashup|ServerResourceType|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalRequests|Totale richieste gateway|Conteggio|Totale|Numero di richieste del gateway|Location, Hostname|
|SuccessfulRequests|Richieste gateway riuscite|Conteggio|Totale|Numero di richieste del gateway riuscite|Location, Hostname|
|UnauthorizedRequests|Richieste del gateway non autorizzate|Conteggio|Totale|Numero di richieste del gateway non autorizzate|Location, Hostname|
|FailedRequests|Richieste gateway non riuscite|Conteggio|Totale|Numero di errori nelle richieste gateway|Location, Hostname|
|OtherRequests|Altre richieste del gateway|Conteggio|Totale|Numero di altre richieste del gateway|Location, Hostname|
|Durata|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Location, Hostname|
|Capacity|Capacity|Percent|Media|Metrica di utilizzo per il servizio ApiManagement|Località|
|EventHubTotalEvents|Eventi di hub eventi totali|Conteggio|Totale|Numero di eventi inviati all'hub eventi|Località|
|EventHubSuccessfulEvents|Eventi di hub eventi ha esito positivo|Conteggio|Totale|Numero di eventi di hub eventi riusciti|Località|
|EventHubTotalFailedEvents|Eventi di hub eventi non riusciti|Conteggio|Totale|Numero di eventi di hub eventi non riusciti|Località|
|EventHubRejectedEvents|Eventi di hub eventi rifiutati|Conteggio|Totale|Numero di eventi di hub eventi rifiutati (configurazione errato o non autorizzato)|Località|
|EventHubThrottledEvents|Eventi di hub eventi limitate|Conteggio|Totale|Numero di eventi limitati di hub eventi|Località|
|EventHubTimedoutEvents|Timeout gli eventi di hub eventi|Conteggio|Totale|Numero di timeout degli eventi di hub eventi|Località|
|EventHubDroppedEvents|Eventi di hub eventi eliminati|Conteggio|Totale|Numero di eventi ignorati a causa di raggiunte le dimensioni della coda|Località|
|EventHubTotalBytesSent|Dimensioni degli eventi di hub eventi|Byte|Totale|Dimensione totale degli eventi di hub eventi in byte|Località|
|Requests|Requests|Conteggio|Totale|Richieste gateway|Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentRuns|Esecuzione distribuzione aggiornamento totale|Conteggio|Totale|Esecuzioni di distribuzione di aggiornamenti software totale|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Esecuzione di computer di distribuzione aggiornamento totale|Conteggio|Totale|Computer di distribuzione aggiornamento software totale viene eseguito in una distribuzione degli aggiornamenti software eseguita|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CoreCount|Numero di core dedicati|Conteggio|Totale|Numero totale di core dedicati nell'account Batch|Nessuna dimensione|
|TotalNodeCount|Numero di nodi dedicati|Conteggio|Totale|Numero totale di nodi dedicati nell'account Batch|Nessuna dimensione|
|LowPriorityCoreCount|Numero di core a bassa priorità|Conteggio|Totale|Numero totale di core a bassa priorità nell'account Batch|Nessuna dimensione|
|TotalLowPriorityNodeCount|Numero di nodi a bassa priorità|Conteggio|Totale|Numero totale di nodi a bassa priorità nell'account Batch|Nessuna dimensione|
|CreatingNodeCount|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|Nessuna dimensione|
|StartingNodeCount|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|Nessuna dimensione|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|Nessuna dimensione|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|Nessuna dimensione|
|IdleNodeCount|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|Nessuna dimensione|
|OfflineNodeCount|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|Nessuna dimensione|
|RebootingNodeCount|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|Nessuna dimensione|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|Nessuna dimensione|
|RunningNodeCount|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|Nessuna dimensione|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|Nessuna dimensione|
|UnusableNodeCount|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|Nessuna dimensione|
|PreemptedNodeCount|Numero di nodi annullati|Conteggio|Totale|Numero di nodi annullati|Nessuna dimensione|
|TaskStartEvent|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|Nessuna dimensione|
|TaskCompleteEvent|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|Nessuna dimensione|
|TaskFailEvent|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|Nessuna dimensione|
|PoolCreateEvent|Eventi pool creati|Conteggio|Totale|Il numero totale di pool che sono stati creati|Nessuna dimensione|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool avviati|Nessuna dimensione|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool completati|Nessuna dimensione|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Conteggio|Totale|Il numero totale di eliminazioni di pool avviate|Nessuna dimensione|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Conteggio|Totale|Il numero totale di eliminazioni di pool completate|Nessuna dimensione|
|JobDeleteCompleteEvent|Eventi eliminazione processi completati|Conteggio|Totale|Numero totale dei processi eliminati.|Nessuna dimensione|
|JobDeleteStartEvent|Eventi eliminazione processi avviati|Conteggio|Totale|Numero totale di processi da eliminare.|Nessuna dimensione|
|JobDisableCompleteEvent|Eventi disabilitazione processi completati|Conteggio|Totale|Numero totale dei processi disabilitati.|Nessuna dimensione|
|JobDisableStartEvent|Eventi disabilitazione processi avviati|Conteggio|Totale|Numero totale di processi da disabilitare.|Nessuna dimensione|
|JobStartEvent|Eventi di avvio processi|Conteggio|Totale|Numero totale di processi avviati.|Nessuna dimensione|
|JobTerminateCompleteEvent|Eventi terminazione processi completati|Conteggio|Totale|Numero totale di processi terminati.|Nessuna dimensione|
|JobTerminateStartEvent|Eventi terminazione processi avviati|Conteggio|Totale|Numero totale di processi da terminare.|Nessuna dimensione|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Conteggio|Massima||ShardId|
|totalcommandsprocessed|Totale operazioni|Conteggio|Totale||ShardId|
|cachehits|Riscontri nella cache|Conteggio|Totale||ShardId|
|cachemisses|Mancati riscontri nella cache|Conteggio|Totale||ShardId|
|getcommands|Recuperi|Conteggio|Totale||ShardId|
|setcommands|Set|Conteggio|Totale||ShardId|
|operationsPerSecond|Operazioni al secondo|Conteggio|Massima||ShardId|
|evictedkeys|Chiavi eliminate|Conteggio|Totale||ShardId|
|totalkeys|Totale chiavi|Conteggio|Massima||ShardId|
|expiredkeys|Chiavi scadute|Conteggio|Totale||ShardId|
|usedmemory|Memoria utilizzata|Byte|Massima||ShardId|
|usedmemorypercentage|Percentuale memoria usata|Percent|Massima||ShardId|
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massima||ShardId|
|serverLoad|Carico server|Percent|Massima||ShardId|
|cacheWrite|Scrittura nella cache|Byte al secondo|Massima||ShardId|
|cacheRead|Lettura della cache|Byte al secondo|Massima||ShardId|
|percentProcessorTime|CPU|Percent|Massima||ShardId|
|cacheLatency|Microsecondi di latenza della cache (anteprima)|Conteggio|Media||ShardId, SampleType|
|errori|Errors|Conteggio|Massima||ShardId, ErrorType|
|connectedclients0|Client connessi (partizione 0)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachehits0|Riscontri cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|getcommands0|Operazioni Get (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|setcommands0|Operazioni Set (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond0|Operazioni al secondo (partizione 0)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys0|Chiavi rimosse (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|totalkeys0|Totale chiavi (partizione 0)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys0|Chiavi scadute (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massima||Nessuna dimensione|
|serverLoad0|Carico server (partizione 0)|Percent|Massima||Nessuna dimensione|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime0|CPU (partizione 0)|Percent|Massima||Nessuna dimensione|
|connectedclients1|Client connessi (partizione 1)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachehits1|Riscontri cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|getcommands1|Operazioni Get (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|setcommands1|Operazioni Set (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond1|Operazioni al secondo (partizione 1)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys1|Chiavi rimosse (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|totalkeys1|Totale chiavi (partizione 1)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys1|Chiavi scadute (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massima||Nessuna dimensione|
|serverLoad1|Carico server (partizione 1)|Percent|Massima||Nessuna dimensione|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime1|CPU (partizione 1)|Percent|Massima||Nessuna dimensione|
|connectedclients2|Client connessi (partizione 2)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachehits2|Riscontri cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|getcommands2|Operazioni Get (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|setcommands2|Operazioni Set (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond2|Operazioni al secondo (partizione 2)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys2|Chiavi rimosse (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|totalkeys2|Totale chiavi (partizione 2)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys2|Chiavi scadute (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massima||Nessuna dimensione|
|serverLoad2|Carico server (partizione 2)|Percent|Massima||Nessuna dimensione|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime2|CPU (partizione 2)|Percent|Massima||Nessuna dimensione|
|connectedclients3|Client connessi (partizione 3)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachehits3|Riscontri cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|getcommands3|Operazioni Get (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|setcommands3|Operazioni Set (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond3|Operazioni al secondo (partizione 3)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys3|Chiavi rimosse (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|totalkeys3|Totale chiavi (partizione 3)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys3|Chiavi scadute (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massima||Nessuna dimensione|
|serverLoad3|Carico server (partizione 3)|Percent|Massima||Nessuna dimensione|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime3|CPU (partizione 3)|Percent|Massima||Nessuna dimensione|
|connectedclients4|Client connessi (partizione 4)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachehits4|Riscontri cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|getcommands4|Operazioni Get (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|setcommands4|Operazioni Set (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond4|Operazioni al secondo (partizione 4)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys4|Chiavi rimosse (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|totalkeys4|Totale chiavi (partizione 4)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys4|Chiavi scadute (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massima||Nessuna dimensione|
|serverLoad4|Carico server (partizione 4)|Percent|Massima||Nessuna dimensione|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime4|CPU (partizione 4)|Percent|Massima||Nessuna dimensione|
|connectedclients5|Client connessi (partizione 5)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachehits5|Riscontri cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|getcommands5|Operazioni Get (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|setcommands5|Operazioni Set (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond5|Operazioni al secondo (partizione 5)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys5|Chiavi rimosse (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|totalkeys5|Totale chiavi (partizione 5)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys5|Chiavi scadute (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massima||Nessuna dimensione|
|serverLoad5|Carico server (partizione 5)|Percent|Massima||Nessuna dimensione|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime5|CPU (partizione 5)|Percent|Massima||Nessuna dimensione|
|connectedclients6|Client connessi (partizione 6)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachehits6|Riscontri cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|getcommands6|Operazioni Get (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|setcommands6|Operazioni Set (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond6|Operazioni al secondo (partizione 6)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys6|Chiavi rimosse (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|totalkeys6|Totale chiavi (partizione 6)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys6|Chiavi scadute (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massima||Nessuna dimensione|
|serverLoad6|Carico server (partizione 6)|Percent|Massima||Nessuna dimensione|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime6|CPU (partizione 6)|Percent|Massima||Nessuna dimensione|
|connectedclients7|Client connessi (partizione 7)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachehits7|Riscontri cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|getcommands7|Operazioni Get (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|setcommands7|Operazioni Set (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond7|Operazioni al secondo (partizione 7)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys7|Chiavi rimosse (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|totalkeys7|Totale chiavi (partizione 7)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys7|Chiavi scadute (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massima||Nessuna dimensione|
|serverLoad7|Carico server (partizione 7)|Percent|Massima||Nessuna dimensione|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime7|CPU (partizione 7)|Percent|Massima||Nessuna dimensione|
|connectedclients8|Client connessi (partizione 8)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachehits8|Riscontri cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|getcommands8|Operazioni Get (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|setcommands8|Operazioni Set (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond8|Operazioni al secondo (partizione 8)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys8|Chiavi rimosse (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|totalkeys8|Totale chiavi (partizione 8)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys8|Chiavi scadute (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massima||Nessuna dimensione|
|serverLoad8|Carico server (partizione 8)|Percent|Massima||Nessuna dimensione|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime8|CPU (partizione 8)|Percent|Massima||Nessuna dimensione|
|connectedclients9|Client connessi (partizione 9)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cachehits9|Riscontri cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|getcommands9|Operazioni Get (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|setcommands9|Operazioni Set (partizione 9)|Numero|Totale||Nessuna dimensione|
|operationsPerSecond9|Operazioni al secondo (partizione 9)|Conteggio|Massima||Nessuna dimensione|
|evictedkeys9|Chiavi rimosse (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|totalkeys9|Totale chiavi (partizione 9)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys9|Chiavi scadute (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massima||Nessuna dimensione|
|serverLoad9|Carico server (partizione 9)|Percent|Massima||Nessuna dimensione|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime9|CPU (partizione 9)|Percent|Massima||Nessuna dimensione|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali.|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso).|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita).|Nessuna dimensione|
|Byte letti da disco/sec|Letti da disco|Byte al secondo|Media|Media di byte letti dal disco durante il periodo di monitoraggio.|Nessuna dimensione|
|Byte scritti su disco/sec|Scrittura disco|Byte al secondo|Media|Media di byte scritti sul disco durante il periodo di monitoraggio.|Nessuna dimensione|
|Operazioni di lettura da disco/sec|Operazioni lettura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo lette dal disco.|Nessuna dimensione|
|Operazioni di scrittura su disco/sec|Operazioni scrittura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo scritte su disco.|Nessuna dimensione|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali.|ID istanza del ruolo|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso).|ID istanza del ruolo|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita).|ID istanza del ruolo|
|Byte letti da disco/sec|Letti da disco|Byte al secondo|Media|Media di byte letti dal disco durante il periodo di monitoraggio.|ID istanza del ruolo|
|Byte scritti su disco/sec|Scrittura disco|Byte al secondo|Media|Media di byte scritti sul disco durante il periodo di monitoraggio.|ID istanza del ruolo|
|Operazioni di lettura da disco/sec|Operazioni lettura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo lette dal disco.|ID istanza del ruolo|
|Operazioni di scrittura su disco/sec|Operazioni scrittura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo scritte su disco.|ID istanza del ruolo|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalCalls|Totale chiamate|Conteggio|Totale|Numero totale di chiamate.|ApiName, OperationName, Region|
|SuccessfulCalls|Chiamate riuscite|Conteggio|Totale|Numero di chiamate riuscite.|ApiName, OperationName, Region|
|TotalErrors|Totale errori|Conteggio|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Chiamate bloccate|Conteggio|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|ApiName, OperationName, Region|
|ServerErrors|Errori server|Conteggio|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|ApiName, OperationName, Region|
|ClientErrors|Errori client|Conteggio|Totale|Numero di chiamate con errore lato client (codice di risposta HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Dati in entrata|Byte|Totale|Dimensioni dei dati in ingresso in byte.|ApiName, OperationName, Region|
|DataOut|Dati in uscita|Byte|Totale|Dimensioni dei dati in uscita in byte.|ApiName, OperationName, Region|
|Latenza|Latenza|Millisecondi|Media|Latenza in millisecondi.|ApiName, OperationName, Region|
|CharactersTranslated|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri della richiesta di testo in arrivo.|ApiName, OperationName, Region|
|CharactersTrained|Caratteri sottoposto a training|Conteggio|Totale|Numero totale di caratteri con training.|ApiName, OperationName, Region|
|SpeechSessionDuration|Durata sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|ApiName, OperationName, Region|
|TotalTransactions|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|Nessuna dimensione|
|TotalTokenCalls|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso (fatturabile)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Rete in uscita (fatturabile)|Byte|Totale|Numero di byte fatturabili inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni di lettura da disco/sec|Operazioni lettura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo lette dal disco|Nessuna dimensione|
|Operazioni di scrittura su disco/sec|Operazioni scrittura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo scritte su disco|Nessuna dimensione|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU utilizzati|Crediti CPU utilizzati|Conteggio|Media|Numero totale di crediti utilizzati dalla macchina virtuale|Nessuna dimensione|
|Byte letti da disco/sec per disco|Byte letti da disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio|ID slot|
|Byte scritti su disco/sec per disco|Byte scritti su disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio|ID slot|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura da disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio|ID slot|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio|ID slot|
|Profondità coda per disco dati|Profondità coda per un disco dati (deprecato)|Conteggio|Media|Profondità coda disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco sistema operativo|Profondità coda per un disco del sistema operativo (deprecato)|Conteggio|Media|Profondità coda disco sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per un disco dati|Byte letti da disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco/sec per un disco dati|Byte scritti su disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura da disco/sec per un disco dati|Operazioni di lettura da disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco/sec per un disco dati|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità coda per un disco dati|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda disco dati (o lunghezza coda)|LUN|
|Byte letti da disco/sec per un disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per un disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Profondità coda per un disco del sistema operativo|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda disco sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Flussi in ingresso|Flussi in ingresso (anteprima)|Conteggio|Media|Flussi in ingresso è il numero di flussi correnti nella direzione in ingresso (traffico verso la macchina virtuale)|Nessuna dimensione|
|Flussi in uscita|Flussi in uscita (anteprima)|Conteggio|Media|Flussi in uscita è il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in ingresso|Numero massimo richieste di creazione flussi in ingresso (anteprima)|CountPerSecond|Media|Numero massimo di richieste di creazione per i flussi in ingresso (traffico verso la macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in uscita|Numero massimo richieste di creazione flussi in uscita (anteprima)|CountPerSecond|Media|Numero massimo di richieste di creazione per i flussi in uscita (traffico dalla macchina virtuale)|Nessuna dimensione|
|Riscontri letture cache per un disco dati Premium|Riscontri letture cache per un disco dati Premium (anteprima)|Percent|Media|Riscontri letture cache per un disco dati Premium|LUN|
|Mancati riscontri letture cache per un disco dati Premium|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percent|Media|Mancati riscontri letture cache per un disco dati Premium|LUN|
|Riscontri letture cache per un disco del sistema operativo Premium|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percent|Media|Riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percent|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|
|Rete in ingresso (totale)|Rete in ingresso (totale)|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita (totale)|Rete in uscita (totale)|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Rete in ingresso|Rete in ingresso (fatturabile)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Rete in uscita|Rete in uscita (fatturabile)|Byte|Totale|Numero di byte fatturabili inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti sul disco durante il periodo di monitoraggio|VMName|
|Operazioni di lettura da disco/sec|Operazioni lettura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo lette dal disco|VMName|
|Operazioni di scrittura su disco/sec|Operazioni scrittura disco/sec|CountPerSecond|Media|Operazioni di I/O al secondo scritte su disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU utilizzati|Crediti CPU utilizzati|Conteggio|Media|Numero totale di crediti utilizzati dalla macchina virtuale|Nessuna dimensione|
|Byte letti da disco/sec per disco|Byte letti da disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio|ID slot|
|Byte scritti su disco/sec per disco|Byte scritti su disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio|ID slot|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura da disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio|ID slot|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio|ID slot|
|Profondità coda per disco dati|Profondità coda per un disco dati (deprecato)|Conteggio|Media|Profondità coda disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (deprecato)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco sistema operativo|Profondità coda per un disco del sistema operativo (deprecato)|Conteggio|Media|Profondità coda disco sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per un disco dati|Byte letti da disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti su disco/sec per un disco dati|Byte scritti su disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura da disco/sec per un disco dati|Operazioni di lettura da disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco/sec per un disco dati|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Profondità coda per un disco dati|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti da disco/sec per un disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Byte al secondo letti da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|VMName|
|Byte scritti su disco/sec per un disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Byte al secondo scritti su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|VMName|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo lette da un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|VMName|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|CountPerSecond|Media|Operazioni di I/O al secondo scritte su un singolo disco durante il periodo di monitoraggio per un disco del sistema operativo|VMName|
|Profondità coda per un disco del sistema operativo|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda disco sistema operativo (o lunghezza coda)|VMName|
|Flussi in ingresso|Flussi in ingresso (anteprima)|Conteggio|Media|Flussi in ingresso è il numero di flussi correnti nella direzione in ingresso (traffico verso la macchina virtuale)|VMName|
|Flussi in uscita|Flussi in uscita (anteprima)|Conteggio|Media|Flussi in uscita è il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in ingresso|Numero massimo richieste di creazione flussi in ingresso (anteprima)|CountPerSecond|Media|Numero massimo di richieste di creazione per i flussi in ingresso (traffico verso la macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in uscita|Numero massimo richieste di creazione flussi in uscita (anteprima)|CountPerSecond|Media|Numero massimo di richieste di creazione per i flussi in uscita (traffico dalla macchina virtuale)|VMName|
|Riscontri letture cache per un disco dati Premium|Riscontri letture cache per un disco dati Premium (anteprima)|Percent|Media|Riscontri letture cache per un disco dati Premium|LUN, VMName|
|Mancati riscontri letture cache per un disco dati Premium|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percent|Media|Mancati riscontri letture cache per un disco dati Premium|LUN, VMName|
|Riscontri letture cache per un disco del sistema operativo Premium|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percent|Media|Riscontri letture cache per un disco del sistema operativo Premium|VMName|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percent|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|VMName|
|Rete in ingresso (totale)|Rete in ingresso (totale)|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Rete in uscita (totale)|Rete in uscita (totale)|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo CPU|Conteggio|Media|Utilizzo della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo memoria|Byte|Media|Utilizzo totale memoria in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|Nessuna dimensione|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|Nessuna dimensione|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalPullCount|Conteggio Pull totali|Conteggio|Media|Numero di immagine effettua il pull in totale|Nessuna dimensione|
|SuccessfulPullCount|Conteggio Pull ha esito positivo|Conteggio|Media|Numero di pull di immagini con esito positivo|Nessuna dimensione|
|TotalPushCount|Numero totale di Push|Conteggio|Media|Numero di immagine effettua il push in totale|Nessuna dimensione|
|SuccessfulPushCount|Conteggio Push ha esito positivo|Conteggio|Media|Numero di push immagine ha esito positivo|Nessuna dimensione|
|RunDuration|Durata esecuzione|Millisecondi|Totale|Durata dell'esecuzione in millisecondi|Nessuna dimensione|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Totale|Numero totale di core CPU disponibili in un cluster gestito|Nessuna dimensione|
|kube_node_status_allocatable_memory_bytes|Numero totale di memoria disponibile in un cluster gestito|Byte|Totale|Numero totale di memoria disponibile in un cluster gestito|Nessuna dimensione|
|kube_pod_status_ready|Numero di pod in stato Pronto|Conteggio|Totale|Numero di pod in stato Pronto|spazio dei nomi, pod|
|kube_node_status_condition|Stati per diverse condizioni dei nodi|Conteggio|Totale|Stati per diverse condizioni dei nodi|condizione di stato, status2, nodo|
|kube_pod_status_phase|Numero di pod per fase|Conteggio|Totale|Numero di pod per fase|fase, spazio dei nomi, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|DCIApiCalls|Chiamate all'API di Customer Insights|Conteggio|Totale||Nessuna dimensione|
|DCIMappingImportOperationSuccessfulLines|Righe riuscite con l'operazione di importazione mapping|Conteggio|Totale||Nessuna dimensione|
|DCIMappingImportOperationFailedLines|Righe non riuscite con l'operazione di importazione mapping|Conteggio|Totale||Nessuna dimensione|
|DCIMappingImportOperationTotalLines|Righe totali dell'operazione di importazione mapping|Conteggio|Totale||Nessuna dimensione|
|DCIMappingImportOperationRuntimeInSeconds|Runtime dell'operazione di importazione mapping in secondi|Secondi|Totale||Nessuna dimensione|
|DCIOutboundProfileExportSucceeded|Esportazione profili in uscita riuscita|Conteggio|Totale||Nessuna dimensione|
|DCIOutboundProfileExportFailed|Esportazione profili in uscita non riuscita|Conteggio|Totale||Nessuna dimensione|
|DCIOutboundProfileExportDuration|Durata esportazione profili in uscita|Secondi|Totale||Nessuna dimensione|
|DCIOutboundKpiExportSucceeded|Esportazione di KPI in uscita riuscita|Conteggio|Totale||Nessuna dimensione|
|DCIOutboundKpiExportFailed|Esportazione di KPI in uscita non riuscita|Conteggio|Totale||Nessuna dimensione|
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
|ImportASAValuesFailed|Numero importazioni valori ASA non riuscite|Conteggio|Totale||Nessuna dimensione|
|ImportASAValuesSucceeded|Numero importazioni valori ASA riuscite|Conteggio|Totale||Nessuna dimensione|
|DCIProfilesCount|Numero di istanze del profilo|Conteggio|Ultima||Nessuna dimensione|
|DCIInteractionsPerMonthCount|Numero di interazioni per mese|Conteggio|Ultima||Nessuna dimensione|
|DCIKpisCount|Numero di indicatori KPI|Conteggio|Ultima||Nessuna dimensione|
|DCISegmentsCount|Numero di segmenti|Conteggio|Ultima||Nessuna dimensione|
|DCIPredictiveMatchPoliciesCount|Numero di corrispondenze predittive|Conteggio|Ultima||Nessuna dimensione|
|DCIPredictionsCount|Numero di stime|Conteggio|Ultima||Nessuna dimensione|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva lettura (rete)|Byte al secondo|Media|Velocità effettiva in lettura dell'interfaccia di rete nel dispositivo nel periodo di riferimento per tutti i volumi nel gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva in scrittura dell'interfaccia di rete nel dispositivo nel periodo di riferimento per tutti i volumi nel gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettova download cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di riferimento.|Condividi|
|CloudUploadThroughputPerShare|Velocità effettiva caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di riferimento.|Condividi|
|BytesUploadedToCloudPerShare|Cloud byte caricati (condivisione)|Byte|Media|Numero totale di byte caricati in Azure da una condivisione durante il periodo di riferimento.|Condividi|
|TotalCapacity|Capacità totale|Byte|Media|Capacità totale|Nessuna dimensione|
|AvailableCapacity|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di riferimento.|Nessuna dimensione|
|CloudUploadThroughput|Velocità effettiva caricamento cloud|Byte al secondo|Media|Velocità effettiva di caricamento cloud in Azure durante il periodo di riferimento.|Nessuna dimensione|
|CloudReadThroughput|Velocità effettiva download cloud|Byte al secondo|Media|Velocità effettiva di download cloud in Azure durante il periodo di riferimento.|Nessuna dimensione|
|BytesUploadedToCloud|Cloud byte caricati (dispositivo)|Byte|Media|Numero totale di byte caricati in Azure da un dispositivo durante il periodo di riferimento.|Nessuna dimensione|
|HyperVVirtualProcessorUtilization|Calcolo Edge - Percentuale CPU|Percent|Media|Percentuale di utilizzo CPU|InstanceName|
|HyperVMemoryUtilization|Calcolo Edge - Utilizzo memoria|Percent|Media|Quantità di RAM in uso|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale||pipelineName, activityName|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Uso della CPU di Integration runtime|Percent|Media||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Numero massimo consentito entità|Conteggio|Massima||Nessuna dimensione|
|MaxAllowedFactorySizeInGbUnits|Numero massimo di dimensioni factory (GB per unità)|Conteggio|Massima||Nessuna dimensione|
|ResourceCount|Numero totale di entità|Conteggio|Massima||Nessuna dimensione|
|FactorySizeInGbUnits|Dimensioni factory totale (GB per unità)|Conteggio|Massima||Nessuna dimensione|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|Nessuna dimensione|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|Nessuna dimensione|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati.|Nessuna dimensione|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|Nessuna dimensione|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|Nessuna dimensione|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati.|Nessuna dimensione|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massima|Quantità totale di dati archiviati nell'account|Nessuna dimensione|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuna dimensione|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuna dimensione|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|Nessuna dimensione|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|Nessuna dimensione|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percent|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|IO percent (Percentuale IO)|Percent|Media|IO percent (Percentuale IO)|Nessuna dimensione|
|storage_percent|Percentuale di archiviazione|Percent|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Spazio di archiviazione utilizzato|Byte|Media|Spazio di archiviazione utilizzato|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percent|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Media|Intervallo di replica in secondi|Nessuna dimensione|
|backup_storage_used|Archivio di backup usato|Byte|Media|Archivio di backup usato|Nessuna dimensione|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percent|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|IO percent (Percentuale IO)|Percent|Media|IO percent (Percentuale IO)|Nessuna dimensione|
|storage_percent|Percentuale di archiviazione|Percent|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Spazio di archiviazione utilizzato|Byte|Media|Spazio di archiviazione utilizzato|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percent|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Media|Intervallo di replica in secondi|Nessuna dimensione|
|backup_storage_used|Archivio di backup usato|Byte|Media|Archivio di backup usato|Nessuna dimensione|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percent|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|IO percent (Percentuale IO)|Percent|Media|IO percent (Percentuale IO)|Nessuna dimensione|
|storage_percent|Percentuale di archiviazione|Percent|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Spazio di archiviazione utilizzato|Byte|Media|Spazio di archiviazione utilizzato|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percent|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|backup_storage_used|Archivio di backup usato|Byte|Media|Archivio di backup usato|Nessuna dimensione|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massima|Ritardo di replica in pochi secondi|Nessuna dimensione|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massima|Ritardo in byte di più repliche in ritardo|Nessuna dimensione|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percent|Media|Percentuale memoria|Nessuna dimensione|
|numero di IOPS|Input/output al secondo|Conteggio|Media|Operazioni dei / o al secondo|Nessuna dimensione|
|storage_percent|Percentuale di archiviazione|Percent|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Spazio di archiviazione utilizzato|Byte|Media|Spazio di archiviazione utilizzato|Nessuna dimensione|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|c2d.commands.egress.complete.success|Comandi completati|Conteggio|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Conteggio|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.reject.success|Comandi rifiutati|Conteggio|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|devices.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|Nessuna dimensione|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|Nessuna dimensione|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |Nessuna dimensione|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|Nessuna dimensione|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|Nessuna dimensione|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|Nessuna dimensione|
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di richieste per l'IoT Hub inviare eventi a griglia di eventi. Questo numero include le richieste riuscite e non riuscite. Utilizzare la dimensione di risultato per il numero di diversi tipi di risposta. Per visualizzare la posizione in cui le richieste provengono da, utilizzare la dimensione di tipo di evento.|Risultato, il tipo di evento|
|EventGridLatency|Latenza di griglia di eventi (anteprima)|Millisecondi|Media|La latenza media (in millisecondi) tra l'ingresso di eventi all'IoT Hub e di ingresso degli eventi in griglia di eventi. Questo numero è una media tra tutti i tipi di eventi. Utilizzare la dimensione di tipo di evento per visualizzare la latenza di un tipo specifico di evento.|Tipo di evento|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|Nessuna dimensione|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|Nessuna dimensione|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|Nessuna dimensione|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|Nessuna dimensione|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|Nessuna dimensione|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|Nessuna dimensione|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|Nessuna dimensione|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|Nessuna dimensione|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|Nessuna dimensione|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|Nessuna dimensione|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|Nessuna dimensione|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|Nessuna dimensione|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|Nessuna dimensione|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|Nessuna dimensione|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|Nessuna dimensione|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|Nessuna dimensione|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|Nessuna dimensione|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|Nessuna dimensione|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|Nessuna dimensione|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna. Si tratta di un valore cumulativo che viene reimpostato su zero alle 00:00 UTC ogni giorno.|Nessuna dimensione|
|deviceDataUsage|Utilizzo dati totale del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|Nessuna dimensione|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AvailableStorage|Spazio di archiviazione disponibile|Byte|Totale|Archiviazione totale segnalati con una granularità di 5 minuti|Area CollectionName, DatabaseName,|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni di Cassandra che sono stati chiusi, viene segnalato a una granularità di 1 minuto|Area, ClosureReason|
|CassandraRequestCharges|Addebiti richieste Cassandra|Conteggio|Totale|Unità richiesta consumate per le richieste di Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste di Cassandra|DatabaseName, CollectionName, area, OperationType, ResourceType, codice di errore|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo dati totale segnalati con una granularità di 5 minuti|Area CollectionName, DatabaseName,|
|DocumentCount|Conteggio documenti|Conteggio|Totale|Numero totale di documenti segnalato con una granularità di 5 minuti|Area CollectionName, DatabaseName,|
|DocumentQuota|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalati con una granularità di 5 minuti|Area CollectionName, DatabaseName,|
|IndexUsage|Utilizzo indice|Byte|Totale|Utilizzo della totali dell'indice indicato con una granularità di 5 minuti|Area CollectionName, DatabaseName,|
|MetadataRequests|Metadata Requests (Richieste di metadati)|Conteggio|Conteggio|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, consentendo di enumerare le raccolte, i database e così via e le relative configurazioni gratuitamente.|DatabaseName, CollectionName, area, StatusCode, |
|MongoRequestCharge|Mongo Request Charge (Addebito richiesta Mongo)|Conteggio|Totale|Unità richiesta Mongo utilizzate|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Richieste Mongo|Conteggio|Conteggio|Numero di richieste Mongo eseguite|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Velocità effettiva sottoposta a provisioning|Conteggio|Massima|Velocità effettiva sottoposta a provisioning|DatabaseName, CollectionName|
|ReplicationLatency|Livello P99 Latenza di replica|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion, TargetRegion|
|ServiceAvailability|Disponibilità del servizio|Percent|Media|Disponibilità dell'account di richieste con una granularità di un'ora, giorno o mese|Nessuna dimensione|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, area, StatusCode, OperationType|
|TotalRequests|Richieste totali|Conteggio|Conteggio|Numero di richieste eseguite|DatabaseName, CollectionName, area, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishFailCount|Pubblicare eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Pubblicare latenza operazioni riuscite|Conteggio|Totale|Pubblicare la latenza di esito positivo in millisecondi|Nessuna dimensione|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuna dimensione|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Error, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuna dimensione|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuna dimensione|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishFailCount|Eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Pubblicare latenza operazioni riuscite|Conteggio|Totale|Pubblicare la latenza di esito positivo in millisecondi|Nessuna dimensione|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste completate|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|EntityName, |
|ServerErrors|Errori del server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|EntityName, |
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|EntityName, |
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Messaggi in arrivo|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|Connessioni attive|Connessioni attive|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuna dimensione|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (deprecate)|Conteggio|Totale|Totale in ingresso Invia le richieste di uno spazio dei nomi (deprecata)|Nessuna dimensione|
|SUCCREQ|Richieste riuscite (deprecate)|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|FAILREQ|Richieste non riuscite (deprecate)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|SVRBSY|Errori server occupato (deprecati)|Conteggio|Totale|Totale errori del server occupato per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|INTERR|Errori interni del Server (deprecati)|Conteggio|Totale|Errori server interni totali per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|MISCERR|Altri errori (deprecate)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|INMSGS|Messaggi in ingresso (deprecata) (deprecati)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica messaggi in ingresso (deprecata)|Nessuna dimensione|
|EHINMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totali messaggi in ingresso per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|OUTMSGS|Messaggi in uscita (deprecati) (deprecati)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica messaggi in uscita (deprecata)|Nessuna dimensione|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|EHINMBS|Byte in ingresso (deprecata) (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica byte in ingresso (deprecata)|Nessuna dimensione|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Dell'Hub eventi in arrivo messaggio elaborati per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|EHOUTMBS|Byte in uscita (deprecata) (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica byte in uscita (deprecata)|Nessuna dimensione|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Dell'Hub eventi in uscita messaggi elaborati per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|EHABL|Archiviazione backlog messaggi (deprecata)|Conteggio|Totale|Messaggi archiviati di Hub eventi in backlog per uno spazio dei nomi (deprecata)|Nessuna dimensione|
|EHAMSGS|Messaggi archiviati (deprecata)|Conteggio|Totale|Hub eventi di messaggi archiviati in uno spazio dei nomi (deprecata)|Nessuna dimensione|
|EHAMBS|Velocità effettiva messaggi archiviati (deprecata)|Byte|Totale|Velocità effettiva dei messaggi archiviati dell'Hub eventi in uno spazio dei nomi (deprecata)|Nessuna dimensione|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste completate (anteprima)|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ServerErrors|Errori del server. (Anteprima)|Conteggio|Totale|Errori del server per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|UserErrors|Errori utente. (Anteprima)|Conteggio|Totale|Errori utente per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|QuotaExceededErrors|Errori di superamento quota. (Anteprima)|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ThrottledRequests|Richieste limitate. (Anteprima)|Conteggio|Totale|Richieste limitate per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingRequests|Richieste in ingresso (anteprima)|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingMessages|Messaggi in ingresso (anteprima)|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|OutgoingMessages|Messaggi in uscita (anteprima)|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|IncomingBytes|Byte in ingresso. (Anteprima)|Byte|Totale|Byte in ingresso per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|OutgoingBytes|Byte in uscita. (Anteprima)|Byte|Totale|Byte in uscita per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|Connessioni attive|ActiveConnections (anteprima)|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ConnectionsOpened|Connessioni aperte. (Anteprima)|Conteggio|Media|Connessioni aperte for Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|ConnectionsClosed|Connessioni chiuse. (Anteprima)|Conteggio|Media|Connessioni chiuse for Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CaptureBacklog|Backlog acquisiti. (Anteprima)|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CapturedMessages|Messaggi acquisiti. (Anteprima)|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CapturedBytes|Byte acquisiti. (Anteprima)|Byte|Totale|Byte acquisiti per Microsoft.EventHub. (Anteprima)|Nessuna dimensione|
|CPU|CPU (anteprima)|Percent|Massima|Utilizzo CPU per il cluster dell'hub eventi in percentuale|Ruolo|
|AvailableMemory|Memoria disponibile (anteprima)|Conteggio|Massima|Memoria disponibile per il cluster dell'hub eventi in byte|Ruolo|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Numero di thread di lavoro attivi|Conteggio|Massima|Numero di thread di lavoro attivi|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dalla scalabilità automatica al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di scalabilità automatica configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata alla scalabilità automatica al momento dell'esecuzione dell'operazione.|Nessuna dimensione|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Anteprima pubblica)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult availabilityResult/nome, e un percorso|
|availabilityResults/count|Test di disponibilità|Conteggio|Conteggio|Numero di test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|Nessuna dimensione|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|Nessuna dimensione|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|Nessuna dimensione|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|Nessuna dimensione|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|Nessuna dimensione|
|dependencies/count|Chiamate di dipendenza|Conteggio|Conteggio|Conteggio delle chiamate effettuate dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate effettuate dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Errori di chiamate di dipendenza|Conteggio|Conteggio|Numero di chiamate di dipendenza non riuscite effettuate dall'applicazione alle risorse esterne.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Visualizzazioni pagina|Conteggio|Conteggio|Conteggio delle visualizzazioni pagina.|operation/synthetic|
|pageViews/duration|Tempo di caricamento della visualizzazione pagina|Millisecondi|Media|Tempo di caricamento della visualizzazione pagina|operation/synthetic|
|performanceCounters/requestExecutionTime|Tempo di esecuzione delle richieste HTTP|Millisecondi|Media|Tempo di esecuzione della richiesta più recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Richieste HTTP nella coda dell'applicazione|Conteggio|Media|Lunghezza della coda di richieste dell'applicazione.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Frequenza delle richieste HTTP|CountPerSecond|Media|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Percentuale di eccezioni|CountPerSecond|Media|Conteggio delle eccezioni gestite e non gestite restituite in Windows, incluse le eccezioni .NET e non gestite che vengono convertite in eccezioni .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocità di I/O processi|Byte al secondo|Media|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU processi|Percent|Media|Percentuale di tempo trascorso per l'uso del processore da parte di tutti i thread di processo per eseguire le istruzioni. Il valore può variare tra 0 e 100. Questa metrica indica le prestazioni del solo processo w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo processore|Percent|Media|Percentuale di tempo trascorsa dal processore in thread non inattivi.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memoria disponibile|Byte|Media|Memoria fisica immediatamente disponibile per l'allocazione a un processo o utilizzabile dal sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Byte privati processi|Byte|Media|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.|cloud/roleInstance|
|requests/duration|Tempo di risposta del server|Millisecondi|Media|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Richieste server|Conteggio|Conteggio|Conteggio delle richieste HTTP completate.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Richieste non riuscite|Conteggio|Conteggio|Numero delle richieste HTTP contrassegnate come non riuscite. Nella maggior parte dei casi si tratta di richieste con un codice di risposta maggiore o uguale a 400 e diverso da 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Frequenza delle richieste server|CountPerSecond|Media|Frequenza delle richieste server al secondo|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Eccezioni|Conteggio|Conteggio|Conteggio combinato di tutte le eccezioni non rilevate.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Eccezioni del browser|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nel browser.|Nessuna dimensione|
|exceptions/server|Eccezioni del server|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nell'applicazione server.|cloud/roleName, cloud/roleInstance|
|traces/count|Analisi|Conteggio|Conteggio|Conteggio documenti di analisi|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Totale di risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Uso della cache|Percent|Media|Livello di uso nell'ambito del cluster|Nessuna dimensione|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query in secondi|QueryStatus|
|IngestionsLoadFactor|Uso dell'inserimento|Percent|Media|Percentuale di slot di inserimento usati nel cluster|Nessuna dimensione|
|IsEngineAnsweringQuery|Keep-alive|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|Nessuna dimensione|
|IngestCommandOriginalSizeInMb|Volume di inserimento (in MB)|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Nessuna dimensione|
|IngestedEventAgeSeconds|Latenza di inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|Nessuna dimensione|
|EventRecievedFromEventHub|Eventi elaborati (per gli hub eventi)|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi|Nessuna dimensione|
|IngestionResult|Risultato inserimento|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|EngineCPU|CPU|Percent|Media|Livello di uso della CPU|Nessuna dimensione|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Uso|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Numero di flussi di lavoro esecuzioni annullata.|Nessuna dimensione|
|RunLatency|Latenza esecuzioni|Secondi|Media|Latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|Latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Numero di eventi di azione o trigger del flusso di lavoro limitati.|Nessuna dimensione|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percent|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionsFailed|Azioni non riuscite|Conteggio|Totale|Numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionLatency|Latenza azioni |Secondi|Media|Latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|Latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Numero di eventi di azione del flusso di lavoro limitati.|Nessuna dimensione|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Numero di trigger del flusso di lavoro attivati.|Nessuna dimensione|
|TriggerLatency|Latenza trigger |Secondi|Media|Latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|Latenza dei trigger del flusso di lavoro attivati.|Nessuna dimensione|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|Latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Numero di eventi di trigger del flusso di lavoro limitati.|Nessuna dimensione|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni dell'azione del flusso di lavoro fatturate.|Nessuna dimensione|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni del trigger del flusso di lavoro fatturate.|Nessuna dimensione|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro fatturate.|Nessuna dimensione|
|BillingUsageNativeOperation|Utilizzo fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuna dimensione|
|BillingUsageStandardConnector|Utilizzo fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuna dimensione|
|BillingUsageStorageConsumption|Utilizzo fatturazione per esecuzioni con utilizzo dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con utilizzo dell'archiviazione fatturate.|Nessuna dimensione|
|BillingUsageNativeOperation|Utilizzo fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuna dimensione|
|BillingUsageStandardConnector|Utilizzo fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuna dimensione|
|BillingUsageStorageConsumption|Utilizzo fatturazione per esecuzioni con utilizzo dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con utilizzo dell'archiviazione fatturate.|Nessuna dimensione|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Numero di flussi di lavoro esecuzioni annullata.|Nessuna dimensione|
|RunLatency|Latenza esecuzioni|Secondi|Media|Latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|Latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Numero di eventi di azione o trigger del flusso di lavoro limitati.|Nessuna dimensione|
|RunStartThrottledEvents|Eventi di avvio esecuzione limitati|Conteggio|Totale|Numero di eventi di avvio esecuzione del flusso di lavoro limitati.|Nessuna dimensione|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percent|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionLatency|Latenza azioni |Secondi|Media|Latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|Latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Numero di eventi di azione del flusso di lavoro limitati.|Nessuna dimensione|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Numero di trigger del flusso di lavoro attivati.|Nessuna dimensione|
|TriggerLatency|Latenza trigger |Secondi|Media|Latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|Latenza dei trigger del flusso di lavoro attivati.|Nessuna dimensione|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|Latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Numero di eventi di trigger del flusso di lavoro limitati.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazione|Percent|Media|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazione|Percent|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione|Percent|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione|Percent|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Esecuzioni completate|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni completate correttamente per questa area di lavoro|Scenario|
|Esecuzione avviate|Esecuzione avviate|Conteggio|Totale|Numero di esecuzioni avviate per questa area di lavoro|Scenario|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscito per questa area di lavoro|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Uso|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilità|Disponibilità|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|

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
|BytesSentRate|Byte inviati|Conteggio|Totale|Numero di byte inviati dall'interfaccia di rete|Nessuna dimensione|
|BytesReceivedRate|Byte ricevuti|Conteggio|Totale|Numero di byte ricevuti dall'interfaccia di rete|Nessuna dimensione|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|Nessuna dimensione|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|Nessuna dimensione|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media del percorso dati di Load Balancer per periodo di tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Stato probe di integrità|Conteggio|Media|Stato probe di integrità media di Load Balancer per periodo di tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Numero di connessioni SNAT|Conteggio|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Porte SNAT allocate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT allocate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Porte SNAT usate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT usate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query per zona DNS|Nessuna dimensione|
|RecordSetCount|Numero di set di record|Conteggio|Massima|Numero di set di record in una zona DNS|Nessuna dimensione|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percent|Massima|Percentuale della capacità di set di record usata da una zona DNS|Nessuna dimensione|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS pacchetti in ingresso|CountPerSecond|Massima|DDoS pacchetti in ingresso|Nessuna dimensione|
|PacketsDroppedDDoS|DDoS pacchetti in ingresso eliminati|CountPerSecond|Massima|DDoS pacchetti in ingresso eliminati|Nessuna dimensione|
|PacketsForwardedDDoS|DDoS pacchetti in ingresso inoltrati|CountPerSecond|Massima|DDoS pacchetti in ingresso inoltrati|Nessuna dimensione|
|TCPPacketsInDDoS|DDoS pacchetti TCP in ingresso|CountPerSecond|Massima|DDoS pacchetti TCP in ingresso|Nessuna dimensione|
|TCPPacketsDroppedDDoS|DDoS pacchetti TCP in ingresso eliminati|CountPerSecond|Massima|DDoS pacchetti TCP in ingresso eliminati|Nessuna dimensione|
|TCPPacketsForwardedDDoS|DDoS pacchetti TCP in ingresso inoltrati|CountPerSecond|Massima|DDoS pacchetti TCP in ingresso inoltrati|Nessuna dimensione|
|UDPPacketsInDDoS|DDoS pacchetti UDP in ingresso|CountPerSecond|Massima|DDoS pacchetti UDP in ingresso|Nessuna dimensione|
|UDPPacketsDroppedDDoS|DDoS pacchetti UDP in ingresso eliminati|CountPerSecond|Massima|DDoS pacchetti UDP in ingresso eliminati|Nessuna dimensione|
|UDPPacketsForwardedDDoS|DDoS pacchetti UDP in ingresso inoltrati|CountPerSecond|Massima|DDoS pacchetti UDP in ingresso inoltrati|Nessuna dimensione|
|BytesInDDoS|DDoS byte in ingresso|Byte al secondo|Massima|DDoS byte in ingresso|Nessuna dimensione|
|BytesDroppedDDoS|DDoS byte in ingresso eliminati|Byte al secondo|Massima|DDoS byte in ingresso eliminati|Nessuna dimensione|
|BytesForwardedDDoS|DDoS byte in ingresso inoltrati|Byte al secondo|Massima|DDoS byte in ingresso inoltrati|Nessuna dimensione|
|TCPBytesInDDoS|DDoS byte TCP in ingresso|Byte al secondo|Massima|DDoS byte TCP in ingresso|Nessuna dimensione|
|TCPBytesDroppedDDoS|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massima|DDoS byte TCP in ingresso eliminati|Nessuna dimensione|
|TCPBytesForwardedDDoS|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massima|DDoS byte TCP in ingresso inoltrati|Nessuna dimensione|
|UDPBytesInDDoS|DDoS byte UDP in ingresso|Byte al secondo|Massima|DDoS byte UDP in ingresso|Nessuna dimensione|
|UDPBytesDroppedDDoS|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massima|DDoS byte UDP in ingresso eliminati|Nessuna dimensione|
|UDPBytesForwardedDDoS|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massima|DDoS byte UDP in ingresso inoltrati|Nessuna dimensione|
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Conteggio|Massima|Sotto attacco DDoS o no|Nessuna dimensione|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|CountPerSecond|Massima|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|CountPerSecond|Massima|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|CountPerSecond|Massima|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Nessuna dimensione|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Port|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Port, Direction|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Port, Direction|
|SynCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ApplicationRuleHit|Numero di riscontri delle regole di applicazione|Conteggio|Totale|Numero di volte in cui sono stati riscontri delle regole di applicazione|Status, Reason, Protocol|
|NetworkRuleHit|Numero di passaggi di regole di rete|Conteggio|Totale|Numero di volte in cui sono stati riscontri delle regole di rete|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Totale|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuna dimensione|
|UnhealthyHostCount|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Richieste totali|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Current Connections (Connessioni correnti)|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|Nessuna dimensione|
|CapacityUnits|Unità di capacità correnti|Conteggio|Media|Unità di capacità usate|Nessuna dimensione|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AverageBandwidth|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SBandwidth|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SConnectionCount|Numero di connessioni da punto a sito|Conteggio|Massima|Numero di connessioni da punto a sito di un gateway|Protocol|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Conteggio|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Conteggio|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Media|Bit in ingresso in Azure al secondo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Media|Bit in uscita da Azure al secondo|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Conteggio|Massima|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ProbesFailedPercent|% di probe non riusciti|Percent|Media|% di probe di monitoraggio connettività non riusciti|Nessuna dimensione|
|AverageRoundtripMs|Avg. Tempo di round trip (ms)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|Nessuna dimensione|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RequestCount|Conteggio richieste|Conteggio|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Conteggio delle richieste del back-end|Conteggio|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latenza della richiesta del back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|TotalLatency|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentuale di integrità del back-end|Percent|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|registration.all|Operazioni registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |Nessuna dimensione|
|registration.create|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|Nessuna dimensione|
|registration.update|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|Nessuna dimensione|
|registration.get|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|Nessuna dimensione|
|registration.delete|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|Nessuna dimensione|
|incoming|Messaggi in arrivo|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |Nessuna dimensione|
|incoming.scheduled|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche Push pianificate annullate|Nessuna dimensione|
|incoming.scheduled.cancel|Notifiche Push pianificate annullate|Conteggio|Totale|Notifiche Push pianificate annullate|Nessuna dimensione|
|scheduled.pending|Notifiche pianificate in sospeso|Conteggio|Totale|Notifiche pianificate in sospeso|Nessuna dimensione|
|installation.all|Operazioni di gestione installazione|Conteggio|Totale|Operazioni di gestione installazione|Nessuna dimensione|
|installation.get|Ottieni operazioni di installazione|Conteggio|Totale|Ottieni operazioni di installazione|Nessuna dimensione|
|installation.upsert|Crea o aggiorna operazioni di installazione|Conteggio|Totale|Crea o aggiorna operazioni di installazione|Nessuna dimensione|
|installation.patch|Operazioni di installazione patch|Conteggio|Totale|Operazioni di installazione patch|Nessuna dimensione|
|installation.delete|Elimina operazioni di installazione|Conteggio|Totale|Elimina operazioni di installazione|Nessuna dimensione|
|outgoing.allpns.success|Notifiche riuscite|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.allpns.invalidpayload|Errori payload|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|Nessuna dimensione|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Conteggio|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|Nessuna dimensione|
|outgoing.allpns.channelerror|Errori canale|Conteggio|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|Nessuna dimensione|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Conteggio|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|Nessuna dimensione|
|outgoing.wns.success|Notifiche WNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|Nessuna dimensione|
|outgoing.wns.badchannel|Errore canale WNS non valido|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|Nessuna dimensione|
|outgoing.wns.throttled|Notifiche WNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Conteggio|Totale|Windows Live non è raggiungibile.|Nessuna dimensione|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Conteggio|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|Nessuna dimensione|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido, ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|Nessuna dimensione|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Conteggio|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|Nessuna dimensione|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Conteggio|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|Nessuna dimensione|
|outgoing.wns.channelthrottled|Canale WNS limitato|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nessuna dimensione|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.dropped|Notifiche WNS eliminate|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.pnserror|Errori WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|Nessuna dimensione|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|Nessuna dimensione|
|outgoing.apns.success|Notifiche APNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.apns.badchannel|Errore canale APNS non valido|Conteggio|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|Nessuna dimensione|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Conteggio|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|Nessuna dimensione|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|Nessuna dimensione|
|outgoing.apns.pnserror|Errori APNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|Nessuna dimensione|
|outgoing.gcm.success|Notifiche GCM completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|Nessuna dimensione|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|Nessuna dimensione|
|outgoing.gcm.throttled|Notifiche GCM limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|Nessuna dimensione|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Conteggio|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|Nessuna dimensione|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|Nessuna dimensione|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|Nessuna dimensione|
|outgoing.gcm.pnserror|Errori GCM|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|Nessuna dimensione|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|Nessuna dimensione|
|outgoing.mpns.success|Notifiche MPNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Conteggio|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|Nessuna dimensione|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Conteggio|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|Nessuna dimensione|
|outgoing.mpns.pnserror|Errori MPNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|Nessuna dimensione|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|notificationhub.pushes|Tutte le notifiche in uscita|Conteggio|Totale|Tutte le notifiche in uscita dell'hub di notifica|Nessuna dimensione|
|incoming.all.requests|Tutte le richieste in ingresso|Conteggio|Totale|Totale richieste in ingresso per un hub di notifica|Nessuna dimensione|
|incoming.all.failedrequests|Tutte le richieste in ingresso non riuscite|Conteggio|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|Nessuna dimensione|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Conteggio|Media|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Conteggio|Media|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Conteggio|Media|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Conteggio|Media|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Byte letti da disco/sec |Conteggio|Media|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec |Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Byte scritti su disco/sec|Conteggio|Media|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Conteggio|Media|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Conteggio|Media|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Conteggio|Media|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Conteggio|Media|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Conteggio|Media|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Conteggio|Media|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Conteggio|Media|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Conteggio|Media|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Conteggio|Media|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Conteggio|Media|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Conteggio|Media|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Conteggio|Media|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Conteggio|Media|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Conteggio|Media|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Byte totali|Conteggio|Media|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Conteggio|Media|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Conteggio|Media|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Conteggio|Media|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Conteggio|Media|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Conteggio|Media|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Conteggio|Media|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Conteggio|Media|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Conteggio|Media|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Conteggio|Media|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Conteggio|Media|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Conteggio|Media|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Conteggio|Media|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Conteggio|Media|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Conteggio|Media|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Conteggio|Media|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Conteggio|Media|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Conteggio|Media|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Conteggio|Media|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Conteggio|Media|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Conteggio|Media|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Conteggio|Media|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processi|Conteggio|Media|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Tempo di attività|Conteggio|Media|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Utenti|Conteggio|Media|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Lunghezza corrente coda su disco|Conteggio|Media|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec |Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Conteggio|Media|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Conteggio|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aggiornamento|Aggiornamento|Conteggio|Media|Aggiornamento|Computer, Product, Classification, UpdateState, Optional, Approved|
|Event|Event|Conteggio|Media|Event|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percent|Media|Thrashing di memoria medio.|Nessuna dimensione|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Connessioni listener - Operazione riuscita|Connessioni listener - Operazione riuscita|Conteggio|Totale|Connessioni listener riuscite per Microsoft.Relay.|EntityName|
|Connessioni listener - Errore del client|Connessioni listener - Errore del client|Conteggio|Totale|Errore del client su connessioni listener per Microsoft.Relay.|EntityName|
|Connessioni listener - Errore del server|Connessioni listener - Errore del server|Conteggio|Totale|Errore del server su connessioni listener per Microsoft.Relay.|EntityName|
|Connessioni mittente - Operazione riuscita|Connessioni mittente - Operazione riuscita|Conteggio|Totale|Connessioni mittente riuscite per Microsoft.Relay.|EntityName|
|Connessioni mittente - Errore del client|Connessioni mittente - Errore del client|Conteggio|Totale|Errore del client su connessioni mittente per Microsoft.Relay.|EntityName|
|Connessioni mittente - Errore del server|Connessioni mittente - Errore del server|Conteggio|Totale|Errore del server su connessioni mittente per Microsoft.Relay.|EntityName|
|Connessioni listener - Totale richieste|Connessioni listener - Totale richieste|Conteggio|Totale|Totale connessioni listener per Microsoft.Relay.|EntityName|
|Connessioni mittente - Totale richieste|Connessioni mittente - Totale richieste|Conteggio|Totale|Totale richieste connessioni mittente per Microsoft.Relay.|EntityName|
|Connessioni attive|Connessioni attive|Conteggio|Totale|Totale connessioni attive per Microsoft.Relay.|EntityName|
|Listener attivi|Listener attivi|Conteggio|Totale|Totale listener attivi per Microsoft.Relay.|EntityName|
|Byte trasferiti|Byte trasferiti|Conteggio|Totale|Totale byte trasferiti per Microsoft.Relay.|EntityName|
|Disconnessioni listener|Disconnessioni listener|Conteggio|Totale|Totale disconnessioni listener per Microsoft.Relay.|EntityName|
|Disconnessioni mittente|Disconnessioni mittente|Conteggio|Totale|Totale disconnessioni mittente per Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuna dimensione|
|SearchQueriesPerSecond|Query di ricerca al secondo|CountPerSecond|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuna dimensione|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percent|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuna dimensione|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste completate (anteprima)|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi (anteprima)|EntityName|
|ServerErrors|Errori del server. (Anteprima)|Conteggio|Totale|Errori del server per Microsoft.ServiceBus. (Anteprima)|EntityName|
|UserErrors|Errori utente. (Anteprima)|Conteggio|Totale|Errori utente per Microsoft.ServiceBus. (Anteprima)|EntityName|
|ThrottledRequests|Richieste limitate. (Anteprima)|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingRequests|Richieste in ingresso (anteprima)|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingMessages|Messaggi in ingresso (anteprima)|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|OutgoingMessages|Messaggi in uscita (anteprima)|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus. (Anteprima)|EntityName|
|Connessioni attive|ActiveConnections (anteprima)|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus. (Anteprima)|Nessuna dimensione|
|Dimensione|Dimensione (anteprima)|Byte|Media|Dimensioni di una coda/argomento in byte. (Anteprima)|EntityName|
|Messaggi|Numero di messaggi in una coda/argomento. (Anteprima)|Conteggio|Media|Numero di messaggi in una coda/argomento. (Anteprima)|EntityName|
|Messaggi attivi|Numero di messaggi attivi in una coda/argomento. (Anteprima)|Conteggio|Media|Numero di messaggi attivi in una coda/argomento. (Anteprima)|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda/argomento. (anteprima)|Conteggio|Media|Numero di messaggi non recapitabili in una coda/argomento. (anteprima)|EntityName|
|ScheduledMessages|Numero di messaggi pianificati in una coda/argomento. (anteprima)|Conteggio|Media|Numero di messaggi pianificati in una coda/argomento. (anteprima)|EntityName|
|CPUXNS|Uso della CPU per spazio dei nomi|Percent|Massima|Metrica per l'utilizzo della CPU dello spazio dei nomi Premium del bus di servizio|Nessuna dimensione|
|WSXNS|Uso delle dimensioni della memoria per spazio dei nomi|Percent|Massima|Metrica per l'utilizzo della memoria dello spazio dei nomi Premium del bus di servizio|Nessuna dimensione|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Conteggio|Media|CPU allocata a questo contenitore in millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Media|Memoria allocata a questo contenitore in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Conteggio|Media|Utilizzo della CPU effettivo in millicore|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Media|Utilizzo effettivo della memoria in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percent|Media|Utilizzo della CPU per questo contenitore come percentuale della AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percent|Media|Utilizzo della CPU per questo contenitore come percentuale della AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Conteggio|Media|Stato dell'applicazione di Service Fabric Mesh|ApplicationName, stato|
|ServiceStatus|ServiceStatus|Conteggio|Media|Stato di integrità di un servizio nell'applicazione di Service Fabric Mesh|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Conteggio|Media|Stato di integrità di una replica del servizio nell'applicazione di Service Fabric Mesh|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Conteggio|Media|Stato del contenitore nell'applicazione di Service Fabric Mesh|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Conteggio|Media|Riavvia conteggio di un contenitore nell'applicazione di Service Fabric Mesh|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Conteggio|Massima|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|Nessuna dimensione|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|Nessuna dimensione|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|Nessuna dimensione|
|UserErrors|Errori utente|Percent|Massima|Percentuale di errori utente|Nessuna dimensione|
|SystemErrors|Errori di sistema|Percent|Massima|Percentuale di errori di sistema|Nessuna dimensione|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|physical_data_read_percent|Percentuale di I/O di dati|Percent|Media|Percentuale di I/O di dati|Nessuna dimensione|
|log_write_percent|Percentuale I/O registro|Percent|Media|Percentuale I/O log|Nessuna dimensione|
|dtu_consumption_percent|Percentuale DTU|Percent|Media|Percentuale DTU|Nessuna dimensione|
|archiviazione|Spazio dati usato|Byte|Massima|Dimensioni totali database|Nessuna dimensione|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuna dimensione|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|Nessuna dimensione|
|deadlock|Deadlock|Conteggio|Totale|Deadlock|Nessuna dimensione|
|storage_percent|Percentuale di spazio usato i dati|Percent|Massima|Percentuale di dimensioni del database|Nessuna dimensione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percent|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percent|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|sessions_percent|Percentuale sessioni|Percent|Media|Percentuale sessioni|Nessuna dimensione|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU|Nessuna dimensione|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|Nessuna dimensione|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|Nessuna dimensione|
|cpu_used|CPU usata|Conteggio|Media|CPU usata|Nessuna dimensione|
|dwu_limit|Limite DWU|Conteggio|Massima|Limite DWU|Nessuna dimensione|
|dwu_consumption_percent|Percentuale DWU|Percent|Massima|Percentuale DWU|Nessuna dimensione|
|dwu_used|Uso DWU|Conteggio|Massima|Uso DWU|Nessuna dimensione|
|dw_cpu_percent|Percentuale CPU a livello di nodo DW|Percent|Media|Percentuale CPU a livello di nodo DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Percentuale I/O dati a livello di nodo DW|Percent|Media|Percentuale I/O dati a livello di nodo DW|DwLogicalNodeId|
    |cache_hit_percent|Percentuale dei riscontri nella cache|Percent|Massima|Percentuale dei riscontri nella cache|Nessuna dimensione|
|cache_used_percent|Percentuale della cache utilizzata|Percent|Massima|Percentuale della cache utilizzata|Nessuna dimensione|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percent|Media|Percentuale di tempdb locale|Nessuna dimensione|
|app_cpu_billed|CPU dell'app fatturata|Conteggio|Totale|CPU dell'app fatturata|Nessuna dimensione|
|app_cpu_percent|Percentuale CPU di App|Percent|Media|Percentuale CPU di App|Nessuna dimensione|
|app_memory_percent|Percentuale memoria utilizzata di App|Percent|Media|Percentuale memoria utilizzata di App|Nessuna dimensione|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuna dimensione|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percent|Media|Percentuale CPU|Nessuna dimensione|
|physical_data_read_percent|Percentuale di I/O di dati|Percent|Media|Percentuale di I/O di dati|Nessuna dimensione|
|log_write_percent|Percentuale I/O registro|Percent|Media|Percentuale I/O log|Nessuna dimensione|
|dtu_consumption_percent|Percentuale DTU|Percent|Media|Percentuale DTU|Nessuna dimensione|
|storage_percent|Percentuale di spazio usato i dati||Percent|Media|Percentuale archiviazione|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percent|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|sessions_percent|Percentuale sessioni|Percent|Media|Percentuale sessioni|Nessuna dimensione|
|eDTU_limit|Limite eDTU|Conteggio|Media|Limite eDTU|Nessuna dimensione|
|storage_limit|Dimensioni massime dati|Byte|Media|Limite archiviazione|Nessuna dimensione|
|eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|Nessuna dimensione|
|storage_used|Spazio dati usato|Byte|Media|Spazio di archiviazione utilizzato|Nessuna dimensione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percent|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|Nessuna dimensione|
|cpu_used|CPU usata|Conteggio|Media|CPU usata|Nessuna dimensione|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuna dimensione|
|allocated_data_storage_percent|Percentuale di spazio allocato dati|Percent|Massima|Percentuale di spazio allocato dati|Nessuna dimensione|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|virtual_core_count|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|Nessuna dimensione|
|avg_cpu_percent|Percentuale CPU Media|Percent|Media|Percentuale CPU Media|Nessuna dimensione|
|reserved_storage_mb|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|Nessuna dimensione|
|storage_space_used_mb|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|Nessuna dimensione|
|io_requests|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|Nessuna dimensione|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|Nessuna dimensione|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|Nessuna dimensione|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di spazio di archiviazione in byte usata dal servizio BLOB dell'account di archiviazione.|BlobType, Tier|
|BlobCount|Numero di BLOB|Conteggio|Totale|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType|       |BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, Tier|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuna dimensione|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di memoria usata dall'indice di ADLS Gen2 (gerarchico) in byte.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità File|Byte|Media|Quantità di spazio di archiviazione in byte usata dal servizio File dell'account di archiviazione.|Nessuna dimensione|
|FileCount|Numero file|Conteggio|Media|Numero di file nel servizio File dell'account di archiviazione.|Nessuna dimensione|
|FileShareCount|Numero condivisioni file|Conteggio|Media|Numero di condivisioni file nel servizio File dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuna dimensione|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuna dimensione|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|Dati in uscita|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Risultato della sessione di sincronizzazione|Conteggio|Media|Metrica che registri un valore di 1 ogni tempo l'Endpoint Server è stata completata una sessione di sincronizzazione con l'Endpoint Cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Numero di file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Numero di file non sono state sincronizzate|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali dei file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stato online del server|Conteggio|Massima|Metrica che registri un valore di 1 ogni ora il server registrato correttamente registra un heartbeat con l'Endpoint Cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percent|Massima|% utilizzo unità di streaming|LogicalName, PartitionId|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|LogicalName, PartitionId|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName, PartitionId|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|LogicalName, PartitionId|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|LogicalName, PartitionId|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|LogicalName, PartitionId|
|Errors|Errori di runtime|Conteggio|Totale|Errori di runtime|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|LogicalName, PartitionId|
|AMLCalloutRequests|Richieste di funzioni|Conteggio|Totale|Richieste della funzione|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste della funzione non riuscite|LogicalName, PartitionId|
|AMLCalloutInputEvents|Eventi di funzioni|Conteggio|Totale|Eventi della funzione|LogicalName, PartitionId|
|DeserializationError|Errori di deserializzazione dell'input|Conteggio|Totale|Errori di deserializzazione dell'input|LogicalName, PartitionId|
|EarlyInputEvents|Eventi di input anticipati|Conteggio|Totale|Eventi di input anticipati|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Ritardo limite|Secondi|Massima|Ritardo limite|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eventi di input con backlog|Conteggio|Massima|Eventi di input con backlog|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Origini di input ricevute|Conteggio|Totale|Origini di input ricevute|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|Nessuna dimensione|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massima|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso|Nessuna dimensione|
|WarmStorageMaxProperties|Proprietà Max a caldo di archiviazione|Conteggio|Massima|Numero massimo di proprietà utilizzate consentito dall'ambiente per lo SKU S1 o S2 e numero massimo di proprietà consentiti da Store Warm per PAYG SKU|Nessuna dimensione|
|WarmStorageUsedProperties|Utilizzare l'archiviazione a caldo proprietà |Conteggio|Massima|Numero di proprietà utilizzato dall'ambiente per lo SKU S1 o S2 e numero di proprietà utilizzate da Store Warm per PAYG SKU|Nessuna dimensione|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|Nessuna dimensione|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massima|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza del messaggio elaborato in ingresso|Nessuna dimensione|
|WarmStorageMaxProperties|Proprietà Max a caldo di archiviazione|Conteggio|Massima|Numero massimo di proprietà utilizzate consentito dall'ambiente per lo SKU S1 o S2 e numero massimo di proprietà consentiti da Store Warm per PAYG SKU|Nessuna dimensione|
|WarmStorageUsedProperties|Utilizzare l'archiviazione a caldo proprietà |Conteggio|Massima|Numero di proprietà utilizzato dall'ambiente per lo SKU S1 o S2 e numero di proprietà utilizzate da Store Warm per PAYG SKU|Nessuna dimensione|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Byte letti da disco/sec|Byte al secondo|Media|Velocità effettiva Media a causa di operazioni di lettura durante il periodo dell'esempio.|Nessuna dimensione|
|DiskWriteBytesPerSecond|Byte scritti su disco/sec|Byte al secondo|Media|Velocità effettiva Media a causa di operazioni di scrittura durante il periodo dell'esempio.|Nessuna dimensione|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco a causa di operazioni di lettura durante il periodo dell'esempio.|Nessuna dimensione|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco a causa di operazioni di scrittura durante il periodo dell'esempio.|Nessuna dimensione|
|DiskReadOperations|Operazioni di lettura disco|Conteggio|Totale|Il numero dei / o operazioni di lettura nel periodo dell'esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskWriteOperations|Operazioni di scrittura disco|Conteggio|Totale|Il numero dei / o operazioni di scrittura nel periodo dell'esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|Operazioni di lettura da disco/sec|Operazioni lettura disco/sec|CountPerSecond|Media|Il numero medio dei / o operazioni di lettura nel periodo dell'esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|Operazioni di scrittura su disco/sec|Operazioni scrittura disco/sec|CountPerSecond|Media|Il numero medio dei / o operazioni di scrittura nel periodo dell'esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskReadLatency|Latenza di lettura da disco|Millisecondi|Media|Totale latenza di lettura. La somma del dispositivo e kernel latenze di lettura.|Nessuna dimensione|
|DiskWriteLatency|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. La somma del dispositivo e kernel latenze di scrittura.|Nessuna dimensione|
|NetworkInBytesPerSecond|Rete In byte/Sec|Byte al secondo|Media|Velocità effettiva di rete medio per il traffico ricevuto.|Nessuna dimensione|
|NetworkOutBytesPerSecond|Rete in uscita byte/Sec|Byte al secondo|Media|Velocità effettiva di rete medio per il traffico trasmessa.|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Velocità effettiva di rete totale per il traffico ricevuto.|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Velocità effettiva di rete totale per il traffico trasmessa.|Nessuna dimensione|
|MemoryUsed|Memoria utilizzata|Byte|Media|La quantità di memoria della macchina in uso dalla macchina virtuale.|Nessuna dimensione|
|MemoryGranted|Memoria concessa|Byte|Media|La quantità di memoria che è stato concesso alla macchina virtuale dall'host. Memoria non viene concessa all'host fino a quando non selezionata una sola volta e concessa memoria può essere scambiata o ballooned assente se il VMkernel necessita la memoria.|Nessuna dimensione|
|MemoryActive|Memoria attiva|Byte|Media|La quantità di memoria utilizzata dalla macchina virtuale nella finestra di piccole dimensioni precedente del tempo. Questo è il numero "true" della quantità di memoria della macchina virtuale attualmente abbia bisogno. Sia lo swapping della memoria aggiuntiva e non usata o ballooned senza alcun impatto sulle prestazioni del guest.|Nessuna dimensione|
|Percentuale CPU|CPU percentuale|Percentuale|Media|L'utilizzo della CPU. Questo valore viene restituito con il 100%, che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale 2 vie usando 50% di un sistema di quattro core è completamente usando due core.|Nessuna dimensione|
|PercentageCpuReady|Percentuale CPU pronto|Millisecondi|Totale|L'ora Ready time è il tempo trascorso in attesa di CPU diventi disponibile nel precedente intervallo di aggiornamento.|Nessuna dimensione|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percent|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percent|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda http|Conteggio|Media|Lunghezza coda http|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|HTTP 101|Conteggio|Totale|HTTP 101|Istanza|
|Http2xx|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medi di risposta|Istanza|
|AppConnections|connessioni|Conteggio|Media|connessioni|Istanza|
|Handle|Conteggio handle|Conteggio|Media|Conteggio handle|Istanza|
|Thread|Conteggio thread|Conteggio|Media|Conteggio thread|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale domini app|Conteggio|Media|Totale domini app|Istanza|
|TotalAppDomainsUnloaded|Totale domini app scaricati|Conteggio|Media|Totale domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità esecuzioni di funzioni|MB / millisecondi|Totale|[Unità di esecuzione (funzione)](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
|FunctionExecutionCount|Numero esecuzioni di funzioni|Conteggio|Totale|Numero esecuzioni di funzioni|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale domini app|Conteggio|Media|Totale domini app|Istanza|
|TotalAppDomainsUnloaded|Totale domini app scaricati|Conteggio|Media|Totale domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|HTTP 101|Conteggio|Totale|HTTP 101|Istanza|
|Http2xx|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medi di risposta|Istanza|
|FunctionExecutionUnits|Unità esecuzioni di funzioni|Conteggio|Totale|Unità esecuzioni di funzioni|Istanza|
|FunctionExecutionCount|Numero esecuzioni di funzioni|Conteggio|Totale|Numero esecuzioni di funzioni|Istanza|
|AppConnections|connessioni|Conteggio|Media|connessioni|Istanza|
|Handle|Conteggio handle|Conteggio|Media|Conteggio handle|Istanza|
|Thread|Conteggio thread|Conteggio|Media|Conteggio thread|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale domini app|Conteggio|Media|Totale domini app|Istanza|
|TotalAppDomainsUnloaded|Totale domini app scaricati|Conteggio|Media|Totale domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|HTTP 101|Conteggio|Totale|HTTP 101|Istanza|
|Http2xx|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medi di risposta|Istanza|
|CpuPercentage|Percentuale CPU|Percent|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percent|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda http|Conteggio|Media|Lunghezza coda http|Istanza|
|ActiveRequests|Richieste attive|Conteggio|Totale|Richieste attive|Istanza|
|TotalFrontEnds|Totale front-end|Conteggio|Media|Totale front-end|Nessuna dimensione|
|SmallAppServicePlanInstances|Ruoli di lavoro del piano di servizio app Small|Conteggio|Media|Ruoli di lavoro del piano di servizio app Small|Nessuna dimensione|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro del piano di servizio app Medium|Nessuna dimensione|
|LargeAppServicePlanInstances|Ruoli di lavoro del piano di servizio app Large|Conteggio|Media|Ruoli di lavoro del piano di servizio app Large|Nessuna dimensione|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|Nessuna dimensione|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|Nessuna dimensione|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|Nessuna dimensione|
|CpuPercentage|Percentuale CPU|Percent|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percent|Media|Percentuale memoria|Istanza|

## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](diagnostic-logs-overview.md)
