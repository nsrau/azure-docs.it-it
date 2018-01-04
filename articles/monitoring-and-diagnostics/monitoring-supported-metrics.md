---
title: 'Metriche di Monitoraggio di Azure: metriche supportate per tipo di risorsa | Microsoft Docs'
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 673f5a5cd6832adb031ef72ce25f8a1622717cfd
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure
Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure.

> [!NOTE]
> Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. Questo elenco include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Per cercare metriche con dimensioni e per accedervi, usare l'[anteprima 01-05-2017 di api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|qpu_metric|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|TotalConnectionRequests|Numero totale di richieste di connessione|Conteggio|Media|Numero totale delle richieste di connessione in arrivo.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|ServerResourceType|
|TotalConnectionFailures|Numero totale di errori di connessione|Conteggio|Media|Numero totale dei tentativi di connessione non riusciti.|ServerResourceType|
|CurrentUserSessions|Sessioni utente correnti|Conteggio|Media|Numero corrente di sessioni utente attive.|ServerResourceType|
|QueryPoolBusyThreads|Thread occupati pool di query|Conteggio|Media|Numero dei thread occupati nel pool dei thread di query.|ServerResourceType|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Conteggio|Media|Numero dei processi nella coda del pool dei thread dei comandi.|ServerResourceType|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Conteggio|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|CurrentConnections|Connessione: connessioni correnti|Conteggio|Media|Numero corrente delle connessioni client stabilite.|ServerResourceType|
|CleanerCurrentPrice|Memoria: prezzo corrente pulitura memoria|Conteggio|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|MemoryUsage|Memoria: utilizzo memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria xVelocity (VertiPaq) in eccesso rispetto al limite di memoria del motore xVelocity.|ServerResourceType|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|ServerResourceType|
|Quota|Memoria: quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|ServerResourceType|
|QuotaBlocked|Memoria: Richieste di quota bloccate|Conteggio|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|ServerResourceType|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|ServerResourceType|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|ServerResourceType|
|RowsReadPerSec|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|ServerResourceType|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|ServerResourceType|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|ServerResourceType|
|CommandPoolBusyThreads|Thread: thread occupati nel pool di comandi|Conteggio|Media|Numero dei thread occupati nel pool dei thread dei comandi.|ServerResourceType|
|CommandPoolIdleThreads|Thread: Thread inattivi pool di comandi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|ServerResourceType|
|LongParsingBusyThreads|Thread: thread occupati per analisi dei thread lunghi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingIdleThreads|Thread: Thread inattivi per analisi dei thread lunghi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread lunghi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Thread: thread di processi di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Thread: Thread non di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Conteggio|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Thread: thread non di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|ServerResourceType|
|QueryPoolIdleThreads|Thread: thread inattivi nel pool di query|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|QueryPoolJobQueueLength|Thread: lunghezza coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|ServerResourceType|
|ShortParsingBusyThreads|Thread: thread occupati per analisi dei thread brevi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingIdleThreads|Thread: thread inattivi per analisi dei thread brevi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingJobQueueLength|Thread: lunghezza coda processi di analisi dei thread brevi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|ServerResourceType|
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
|Duration|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Location, Hostname|
|Capacity|Capacità (anteprima)|Percentuale|Massima|Metrica di utilizzo per il servizio ApiManagement|Località|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|RunbookName, stato|

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

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed|Totale operazioni|Conteggio|Totale||Nessuna dimensione|
|cachehits|Riscontri cache|Conteggio|Totale||Nessuna dimensione|
|cachemisses|Mancati riscontri nella cache|Conteggio|Totale||Nessuna dimensione|
|getcommands|Operazioni Get|Conteggio|Totale||Nessuna dimensione|
|setcommands|Operazioni Set|Conteggio|Totale||Nessuna dimensione|
|evictedkeys|Chiavi rimosse|Conteggio|Totale||Nessuna dimensione|
|totalkeys|Totale chiavi|Conteggio|Massima||Nessuna dimensione|
|expiredkeys|Chiavi scadute|Conteggio|Totale||Nessuna dimensione|
|usedmemory|Memoria utilizzata|Byte|Massima||Nessuna dimensione|
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massima||Nessuna dimensione|
|serverLoad|Carico server|Percentuale|Massima||Nessuna dimensione|
|cacheWrite|Scrittura nella cache|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead|Lettura da cache|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime|CPU|Percentuale|Massima||Nessuna dimensione|
|connectedclients0|Client connessi (partizione 0)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachehits0|Riscontri cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|getcommands0|Operazioni Get (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|setcommands0|Operazioni Set (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys0|Chiavi rimosse (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|totalkeys0|Totale chiavi (partizione 0)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys0|Chiavi scadute (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massima||Nessuna dimensione|
|serverLoad0|Carico server (partizione 0)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massima||Nessuna dimensione|
|connectedclients1|Client connessi (partizione 1)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachehits1|Riscontri cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|getcommands1|Operazioni Get (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|setcommands1|Operazioni Set (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys1|Chiavi rimosse (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|totalkeys1|Totale chiavi (partizione 1)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys1|Chiavi scadute (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massima||Nessuna dimensione|
|serverLoad1|Carico server (partizione 1)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massima||Nessuna dimensione|
|connectedclients2|Client connessi (partizione 2)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachehits2|Riscontri cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|getcommands2|Operazioni Get (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|setcommands2|Operazioni Set (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys2|Chiavi rimosse (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|totalkeys2|Totale chiavi (partizione 2)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys2|Chiavi scadute (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massima||Nessuna dimensione|
|serverLoad2|Carico server (partizione 2)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massima||Nessuna dimensione|
|connectedclients3|Client connessi (partizione 3)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachehits3|Riscontri cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|getcommands3|Operazioni Get (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|setcommands3|Operazioni Set (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys3|Chiavi rimosse (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|totalkeys3|Totale chiavi (partizione 3)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys3|Chiavi scadute (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massima||Nessuna dimensione|
|serverLoad3|Carico server (partizione 3)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massima||Nessuna dimensione|
|connectedclients4|Client connessi (partizione 4)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachehits4|Riscontri cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|getcommands4|Operazioni Get (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|setcommands4|Operazioni Set (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys4|Chiavi rimosse (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|totalkeys4|Totale chiavi (partizione 4)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys4|Chiavi scadute (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massima||Nessuna dimensione|
|serverLoad4|Carico server (partizione 4)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massima||Nessuna dimensione|
|connectedclients5|Client connessi (partizione 5)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachehits5|Riscontri cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|getcommands5|Operazioni Get (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|setcommands5|Operazioni Set (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys5|Chiavi rimosse (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|totalkeys5|Totale chiavi (partizione 5)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys5|Chiavi scadute (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massima||Nessuna dimensione|
|serverLoad5|Carico server (partizione 5)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massima||Nessuna dimensione|
|connectedclients6|Client connessi (partizione 6)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachehits6|Riscontri cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|getcommands6|Operazioni Get (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|setcommands6|Operazioni Set (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys6|Chiavi rimosse (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|totalkeys6|Totale chiavi (partizione 6)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys6|Chiavi scadute (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massima||Nessuna dimensione|
|serverLoad6|Carico server (partizione 6)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massima||Nessuna dimensione|
|connectedclients7|Client connessi (partizione 7)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachehits7|Riscontri cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|getcommands7|Operazioni Get (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|setcommands7|Operazioni Set (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys7|Chiavi rimosse (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|totalkeys7|Totale chiavi (partizione 7)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys7|Chiavi scadute (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massima||Nessuna dimensione|
|serverLoad7|Carico server (partizione 7)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massima||Nessuna dimensione|
|connectedclients8|Client connessi (partizione 8)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachehits8|Riscontri cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|getcommands8|Operazioni Get (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|setcommands8|Operazioni Set (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys8|Chiavi rimosse (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|totalkeys8|Totale chiavi (partizione 8)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys8|Chiavi scadute (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massima||Nessuna dimensione|
|serverLoad8|Carico server (partizione 8)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massima||Nessuna dimensione|
|connectedclients9|Client connessi (partizione 9)|Conteggio|Massima||Nessuna dimensione|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cachehits9|Riscontri cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|getcommands9|Operazioni Get (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|setcommands9|Operazioni Set (partizione 9)|Numero|Totale||Nessuna dimensione|
|evictedkeys9|Chiavi rimosse (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|totalkeys9|Totale chiavi (partizione 9)|Conteggio|Massima||Nessuna dimensione|
|expiredkeys9|Chiavi scadute (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massima||Nessuna dimensione|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massima||Nessuna dimensione|
|serverLoad9|Carico server (partizione 9)|Percentuale|Massima||Nessuna dimensione|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massima||Nessuna dimensione|
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massima||Nessuna dimensione|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|Nessuna dimensione|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|Nessuna dimensione|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalCalls|Totale chiamate|Conteggio|Totale|Numero totale di chiamate.|Nessuna dimensione|
|SuccessfulCalls|Chiamate riuscite|Conteggio|Totale|Numero di chiamate riuscite.|Nessuna dimensione|
|TotalErrors|Totale errori|Conteggio|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|Nessuna dimensione|
|BlockedCalls|Chiamate bloccate|Conteggio|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|Nessuna dimensione|
|ServerErrors|Errori server|Conteggio|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|Nessuna dimensione|
|ClientErrors|Errori client|Conteggio|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|Nessuna dimensione|
|DataIn|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|Nessuna dimensione|
|DataOut|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|Nessuna dimensione|
|Latenza|Latenza|Millisecondi|Media|Latenza in millisecondi.|Nessuna dimensione|
|CharactersTranslated|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|Nessuna dimensione|
|SpeechSessionDuration|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|Nessuna dimensione|
|TotalTransactions|Transazioni totali|Conteggio|Totale|Numero totale di transazioni|Nessuna dimensione|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Il numero totale di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Il numero totale di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|

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
|DCIProfilesCount|Numero di istanze del profilo|Conteggio|Last (Ultimo)||Nessuna dimensione|
|DCIInteractionsPerMonthCount|Numero di interazioni per mese|Conteggio|Last (Ultimo)||Nessuna dimensione|
|DCIKpisCount|Numero di indicatori KPI|Conteggio|Last (Ultimo)||Nessuna dimensione|
|DCISegmentsCount|Numero di segmenti|Conteggio|Last (Ultimo)||Nessuna dimensione|
|DCIPredictiveMatchPoliciesCount|Numero di corrispondenze predittive|Conteggio|Last (Ultimo)||Nessuna dimensione|
|DCIPredictionsCount|Numero di stime|Conteggio|Last (Ultimo)||Nessuna dimensione|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale|Numero di esecuzioni di esito positivo.|Nessuna dimensione|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite.|Nessuna dimensione|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||Nessuna dimensione|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||Nessuna dimensione|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||Nessuna dimensione|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||Nessuna dimensione|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Nessuna dimensione|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Nessuna dimensione|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|Nessuna dimensione|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|Nessuna dimensione|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati|Nessuna dimensione|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|Nessuna dimensione|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|Nessuna dimensione|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati|Nessuna dimensione|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massima|Quantità totale di dati archiviati nell'account|Nessuna dimensione|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuna dimensione|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuna dimensione|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|Nessuna dimensione|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|Nessuna dimensione|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|compute_limit|Limite unità di calcolo|Conteggio|Media|Limite unità di calcolo|Nessuna dimensione|
|compute_consumption_percent|Compute Unit percentage (Percentuale unità di calcolo)|Percentuale|Media|Percentuale unità di calcolo|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Media|IO percent (Percentuale IO)|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|active_connections|Total active connections (Numero totale di connessioni attive)|Conteggio|Media|Numero totale di connessioni attive|Nessuna dimensione|
|connections_failed|Total failed connections (Numero totale di connessioni non riuscite)|Conteggio|Media|Numero totale di connessioni non riuscite|Nessuna dimensione|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|compute_limit|Limite unità di calcolo|Conteggio|Media|Limite unità di calcolo|Nessuna dimensione|
|compute_consumption_percent|Compute Unit percentage (Percentuale unità di calcolo)|Percentuale|Media|Percentuale unità di calcolo|Nessuna dimensione|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|io_consumption_percent|IO percent (Percentuale IO)|Percentuale|Media|IO percent (Percentuale IO)|Nessuna dimensione|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|active_connections|Total active connections (Numero totale di connessioni attive)|Conteggio|Media|Numero totale di connessioni attive|Nessuna dimensione|
|connections_failed|Total failed connections (Numero totale di connessioni non riuscite)|Conteggio|Media|Numero totale di connessioni non riuscite|Nessuna dimensione|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|c2d.commands.egress.complete.success|Comandi completati|Conteggio|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Conteggio|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.reject.success|Comandi rifiutati|Conteggio|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|devices.totalDevices|Totale dispositivi|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|devices.connectedDevices.allProtocol|Dispositivi connessi|Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c.telemetry.egress.success|Messaggi telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint (totale)|Nessuna dimensione|
|d2c.telemetry.egress.dropped|Messaggi rimossi|Conteggio|Totale|Numero di messaggi eliminati per mancata attività dell'endpoint di recapito|Nessuna dimensione|
|d2c.telemetry.egress.orphaned|Messaggi orfani|Conteggio|Totale|Il numero di messaggi non corrispondenti ad alcuna route inclusa la route di fallback|Nessuna dimensione|
|d2c.telemetry.egress.invalid|Messaggi non validi|Conteggio|Totale|Il numero dei messaggi non recapitati per incompatibilità con l'endpoint|Nessuna dimensione|
|d2c.telemetry.egress.fallback|Messaggi corrispondenti alla condizione di fallback|Conteggio|Totale|Numero di messaggi scritti nell'endpoint di fallback|Nessuna dimensione|
|d2c.endpoints.egress.eventHubs|Messaggi recapitati agli endpoint dell'hub eventi|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'hub eventi|Nessuna dimensione|
|d2c.endpoints.latency.eventHubs|Latenza messaggi per gli endpoint di Hub eventi|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di Hub eventi, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusQueues|Messaggi recapitati agli endpoint della coda del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint della coda del bus di servizio|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusQueues|Latenza messaggi per gli endpoint della coda del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint della coda del bus di servizio, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusTopics|Messaggi recapitati agli endpoint dell'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'argomento del bus di servizio|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusTopics|Latenza messaggi per gli endpoint dell'argomento del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.builtIn.events|Messaggi recapitati all'endpoint predefinito (messaggi/eventi)|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti nell'endpoint predefinito (messaggi/eventi)|Nessuna dimensione|
|d2c.endpoints.latency.builtIn.events|Latenza dei messaggi per l'endpoint predefinito (messaggi/eventi)|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi), in millisecondi |Nessuna dimensione|
|d2c.endpoints.egress.storage|Messaggi recapitati agli endpoint di archiviazione|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint di archiviazione|Nessuna dimensione|
|d2c.endpoints.latency.storage|Latenza dei messaggi per gli endpoint di archiviazione|Millisecondi|Media|Latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di archiviazione, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.storage.bytes|Dati scritti nell'archivio|Byte|Totale|Quantità di dati, in byte, scritti negli endpoint di archiviazione|Nessuna dimensione|
|d2c.endpoints.egress.storage.blobs|BLOB scritti nell'archivio|Conteggio|Totale|Numero di BLOB scritti negli endpoint di archiviazione|Nessuna dimensione|
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

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|frequenza di utilizzo richiesto|Percentuale|Media|frequenza di utilizzo richiesto|Nessuna dimensione|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|frequenza di utilizzo richiesto|Percentuale|Media|frequenza di utilizzo richiesto|Nessuna dimensione|
|deviceDataUsage|Utilizzo totale devicedata|Conteggio|Totale|Byte trasferiti da e verso tutti i dispositivi connessi all'hub IOT|Nessuna dimensione|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|c2d.commands.egress.complete.success|Comandi completati|Conteggio|Totale|Numero di comandi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.abandon.success|Comandi abbandonati|Conteggio|Totale|Numero di comandi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.reject.success|Comandi rifiutati|Conteggio|Totale|Numero di comandi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|devices.totalDevices|Totale dispositivi|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|devices.connectedDevices.allProtocol|Dispositivi connessi|Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c.telemetry.egress.success|Messaggi telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint (totale)|Nessuna dimensione|
|d2c.telemetry.egress.dropped|Messaggi rimossi|Conteggio|Totale|Numero di messaggi eliminati per mancata attività dell'endpoint di recapito|Nessuna dimensione|
|d2c.telemetry.egress.orphaned|Messaggi orfani|Conteggio|Totale|Il numero di messaggi non corrispondenti ad alcuna route inclusa la route di fallback|Nessuna dimensione|
|d2c.telemetry.egress.invalid|Messaggi non validi|Conteggio|Totale|Il numero dei messaggi non recapitati per incompatibilità con l'endpoint|Nessuna dimensione|
|d2c.telemetry.egress.fallback|Messaggi corrispondenti alla condizione di fallback|Conteggio|Totale|Numero di messaggi scritti nell'endpoint di fallback|Nessuna dimensione|
|d2c.endpoints.egress.eventHubs|Messaggi recapitati agli endpoint dell'hub eventi|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'hub eventi|Nessuna dimensione|
|d2c.endpoints.latency.eventHubs|Latenza messaggi per gli endpoint di Hub eventi|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di Hub eventi, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusQueues|Messaggi recapitati agli endpoint della coda del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint della coda del bus di servizio|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusQueues|Latenza messaggi per gli endpoint della coda del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint della coda del bus di servizio, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusTopics|Messaggi recapitati agli endpoint dell'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint dell'argomento del bus di servizio|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusTopics|Latenza messaggi per gli endpoint dell'argomento del bus di servizio|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.builtIn.events|Messaggi recapitati all'endpoint predefinito (messaggi/eventi)|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti nell'endpoint predefinito (messaggi/eventi)|Nessuna dimensione|
|d2c.endpoints.latency.builtIn.events|Latenza dei messaggi per l'endpoint predefinito (messaggi/eventi)|Millisecondi|Media|La latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi), in millisecondi |Nessuna dimensione|
|d2c.endpoints.egress.storage|Messaggi recapitati agli endpoint di archiviazione|Conteggio|Totale|Numero di volte in cui i messaggi sono stati scritti negli endpoint di archiviazione|Nessuna dimensione|
|d2c.endpoints.latency.storage|Latenza dei messaggi per gli endpoint di archiviazione|Millisecondi|Media|Latenza media tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint di archiviazione, in millisecondi|Nessuna dimensione|
|d2c.endpoints.egress.storage.bytes|Dati scritti nell'archivio|Byte|Totale|Quantità di dati, in byte, scritti negli endpoint di archiviazione|Nessuna dimensione|
|d2c.endpoints.egress.storage.blobs|BLOB scritti nell'archivio|Conteggio|Totale|Numero di BLOB scritti negli endpoint di archiviazione|Nessuna dimensione|
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

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|MetadataRequests|Richieste di metadati|Conteggio|Conteggio|Numero di richieste di metadati. COSMOS DB mantiene insieme i metadati di sistema per ogni account, che consente di enumerare le raccolte, i database e così via, e le relative configurazioni, disponibile gratuitamente.|GlobalDatabaseAccountName, DatabaseName, NomeRaccolta, area geografica, codice di stato|
|MongoRequestCharge|Addebito di MONGO richiesta|Conteggio|Totale|Unità di richiesta di MONGO utilizzate|GlobalDatabaseAccountName, DatabaseName, NomeRaccolta, area geografica, CommandName, ErrorCode|
|MongoRequests|Richieste Mongo|Conteggio|Conteggio|Numero di richieste di Mongo|GlobalDatabaseAccountName, DatabaseName, NomeRaccolta, area geografica, CommandName, ErrorCode|
|TotalRequestUnits|Unità di richiesta totale|Conteggio|Totale|Richiesta di che unità di misura utilizzata|GlobalDatabaseAccountName, DatabaseName, NomeRaccolta, area geografica, codice di stato|
|TotalRequests|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|GlobalDatabaseAccountName, DatabaseName, NomeRaccolta, area geografica, codice di stato|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite (anteprima)|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub. (Anteprima)|EntityName|
|ServerErrors|Errori del server. (Anteprima)|Conteggio|Totale|Errori del server per Microsoft.EventHub. (Anteprima)|EntityName|
|UserErrors|Errori utente. (Anteprima)|Conteggio|Totale|Errori utente per Microsoft.EventHub. (Anteprima)|EntityName|
|QuotaExceededErrors|Errori di superamento quota. (Anteprima)|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub. (Anteprima)|EntityName|
|ThrottledRequests|Richieste limitate. (Anteprima)|Conteggio|Totale|Richieste limitate per Microsoft.EventHub. (Anteprima)|EntityName|
|IncomingRequests|Richieste in ingresso (anteprima)|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub. (Anteprima)|EntityName|
|IncomingMessages|Messaggi in ingresso (anteprima)|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub. (Anteprima)|EntityName|
|OutgoingMessages|Messaggi in uscita (anteprima)|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub. (Anteprima)|EntityName|
|IncomingBytes|Byte in ingresso. (Anteprima)|Byte|Totale|Byte in ingresso per Microsoft.EventHub. (Anteprima)|EntityName|
|OutgoingBytes|Byte in uscita. (Anteprima)|Byte|Totale|Byte in uscita per Microsoft.EventHub. (Anteprima)|EntityName|
|Connessioni attive|ActiveConnections (anteprima)|Conteggio|Totale|Totale connessioni attive per Microsoft.EventHub. (Anteprima)|EntityName|
|ConnectionsOpened|Connessioni aperte. (Anteprima)|Conteggio|Totale|Connessioni aperte for Microsoft.EventHub. (Anteprima)|EntityName|
|ConnectionsClosed|Connessioni chiuse. (Anteprima)|Conteggio|Totale|Connessioni chiuse for Microsoft.EventHub. (Anteprima)|EntityName|
|CaptureBacklog|Backlog acquisiti. (Anteprima)|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub. (Anteprima)|EntityName|
|CapturedMessages|Messaggi acquisiti. (Anteprima)|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub. (Anteprima)|EntityName|
|CapturedBytes|Byte acquisiti. (Anteprima)|Byte|Totale|Byte acquisiti per Microsoft.EventHub. (Anteprima)|EntityName|
|INREQS|Richieste in ingresso|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi|Nessuna dimensione|
|SUCCREQ|Richieste riuscite|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi|Nessuna dimensione|
|FAILREQ|Richieste non riuscite|Conteggio|Totale|Richieste non riuscite totali per uno spazio dei nomi|Nessuna dimensione|
|SVRBSY|Errori server occupato|Conteggio|Totale|Errori di server occupato totali per uno spazio dei nomi|Nessuna dimensione|
|INTERR|Errori interni server|Conteggio|Totale|Errori di server interni totali per uno spazio dei nomi|Nessuna dimensione|
|MISCERR|Altri errori|Conteggio|Totale|Richieste non riuscite totali per uno spazio dei nomi|Nessuna dimensione|
|INMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica Messaggi in ingresso|Nessuna dimensione|
|EHINMSGS|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso totali per uno spazio dei nomi|Nessuna dimensione|
|OUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica Messaggi in uscita|Nessuna dimensione|
|EHOUTMSGS|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita totali per uno spazio dei nomi|Nessuna dimensione|
|EHINMBS|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica Byte in ingresso|Nessuna dimensione|
|EHINBYTES|Byte in ingresso|Byte|Totale|Velocità effettiva dei messaggi in ingresso di Hub eventi per uno spazio dei nomi|Nessuna dimensione|
|EHOUTMBS|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare la metrica Byte in uscita|Nessuna dimensione|
|EHOUTBYTES|Byte in uscita|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi|Nessuna dimensione|
|EHABL|Messaggi backlog archiviati|Conteggio|Totale|Messaggi archiviati di Hub eventi nel backlog per uno spazio dei nomi|Nessuna dimensione|
|EHAMSGS|Messaggi archiviati|Conteggio|Totale|Messaggi archiviati di Hub eventi in uno spazio dei nomi|Nessuna dimensione|
|EHAMBS|Velocità effettiva messaggi archiviati|Byte|Totale|Velocità effettiva dei messaggi archiviati di Hub eventi in uno spazio dei nomi|Nessuna dimensione|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|Nessuna dimensione|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Latenza|Latenza|Millisecondi|Media|Durata delle chiamate API|OperationName, OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuna dimensione|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuna dimensione|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionsFailed|Azioni non riuscite|Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuna dimensione|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuna dimensione|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|Nessuna dimensione|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|Nessuna dimensione|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|Nessuna dimensione|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Conteggio|Totale|Numero di byte inviati l'interfaccia di rete|Nessuna dimensione|
|BytesReceivedRate|Byte ricevuti|Conteggio|Totale|Numero di byte ricevuti l'interfaccia di rete|Nessuna dimensione|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati l'interfaccia di rete|Nessuna dimensione|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti l'interfaccia di rete|Nessuna dimensione|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|VipAvailability|Disponibilità IP virtuale|Conteggio|Media|Disponibilità di endpoint IP virtuale, in base ai risultati di tipo probe|VipAddress, VipPort|
|DipAvailability|Disponibilità DIP|Conteggio|Media|Disponibilità di endpoint DIP, in base ai risultati di tipo probe|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|VipAddress, VipPort, Direction|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|VipAddress, VipPort, Direction|
|SYNCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|VipAddress, VipPort, Direction|
|SnatConnectionCount|Numero di connessioni SNAT|Conteggio|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|VipAddress, DipAddress, ConnectionState|

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
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Conteggio|Massima|Sotto attacco DDoS o no|Nessuna dimensione|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massima|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massima|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerSYNPackets|Pacchetti in ingresso SYN attivare DDoS attenuazione|Conteggio al secondo|Massima|Pacchetti in ingresso SYN attivare DDoS attenuazione|Nessuna dimensione|
|VipAvailability|Disponibilità|Conteggio|Media|Disponibilità IPAddress media nel periodo di tempo|Porta|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Port, Direction|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Port, Direction|
|SynCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Port, Direction|

## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|PacketsInDroppedVMProtection|Eliminazione di pacchetti in ingresso per la protezione della macchina virtuale|Conteggio al secondo|Media|Eliminazione di pacchetti in ingresso per la protezione della macchina virtuale|Nessuna dimensione|
|PacketsOutDroppedVMProtection|Eliminazione di pacchetti in uscita per la protezione delle macchine Virtuali|Conteggio al secondo|Media|Eliminazione di pacchetti in uscita per la protezione delle macchine Virtuali|Nessuna dimensione|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Totale|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuna dimensione|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Conteggio|Totale|Numero di pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Conteggio|Totale|Numero di pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Numero di rilascio dei pacchetti in uscita dalla mancata corrispondenza del selettore di traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio di rilascio di pacchetti in arrivo dalla mancata corrispondenza del selettore di traffico di un tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Conteggio|Massima|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|registration.all|Operazioni di registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |Nessuna dimensione|
|registration.create|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|Nessuna dimensione|
|registration.update|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|Nessuna dimensione|
|registration.get|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|Nessuna dimensione|
|registration.delete|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|Nessuna dimensione|
|incoming|Messaggi in ingresso|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |Nessuna dimensione|
|incoming.scheduled|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
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
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|Nessuna dimensione|
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
|ActiveListeners|ActiveListeners|Conteggio|Totale|Totale listener attivi per Microsoft.Relay.|EntityName|
|Byte trasferiti|Byte trasferiti|Conteggio|Totale|Totale byte trasferiti per Microsoft.Relay.|EntityName|
|Disconnessioni listener|Disconnessioni listener|Conteggio|Totale|Totale disconnessioni listener per Microsoft.Relay.|EntityName|
|Disconnessioni mittente|Disconnessioni mittente|Conteggio|Totale|Totale disconnessioni mittente per Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuna dimensione|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuna dimensione|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuna dimensione|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite (anteprima)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (anteprima)|EntityName|
|ServerErrors|Errori del server. (Anteprima)|Conteggio|Totale|Errori del server per Microsoft.ServiceBus. (Anteprima)|EntityName|
|UserErrors|Errori utente. (Anteprima)|Conteggio|Totale|Errori utente per Microsoft.ServiceBus. (Anteprima)|EntityName|
|ThrottledRequests|Richieste limitate. (Anteprima)|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingRequests|Richieste in ingresso (anteprima)|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|IncomingMessages|Messaggi in ingresso (anteprima)|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus. (Anteprima)|EntityName|
|OutgoingMessages|Messaggi in uscita (anteprima)|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus. (Anteprima)|EntityName|
|Connessioni attive|ActiveConnections (anteprima)|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus. (Anteprima)|EntityName|
|ConnectionsOpened|Connessioni aperte. (Anteprima)|Conteggio|Totale|Connessioni aperte for Microsoft.ServiceBus. (Anteprima)|EntityName|
|ConnectionsClosed|Connessioni chiuse. (Anteprima)|Conteggio|Totale|Connessioni chiuse for Microsoft.ServiceBus. (Anteprima)|EntityName|
|CPUXNS|Uso della CPU per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo CPU per spazio dei nomi Premium del bus di servizio|Nessuna dimensione|
|WSXNS|Uso delle dimensioni della memoria per spazio dei nomi|Percentuale|Massima|Metrica di utilizzo memoria per spazio dei nomi Premium del bus di servizio|Nessuna dimensione|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuna dimensione|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|Nessuna dimensione|
|storage|Dimensioni totali database|Byte|Massima|Dimensioni totali database|Nessuna dimensione|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuna dimensione|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|Nessuna dimensione|
|deadlock|Deadlock|Conteggio|Totale|Deadlock|Nessuna dimensione|
|storage_percent|Percentuale di dimensioni del database|Percentuale|Massima|Percentuale di dimensioni del database|Nessuna dimensione|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuna dimensione|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU|Nessuna dimensione|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|Nessuna dimensione|
|dwu_limit|Limite DWU|Conteggio|Massima|Limite DWU|Nessuna dimensione|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massima|Percentuale DWU|Nessuna dimensione|
|dwu_used|Uso DWU|Conteggio|Massima|Uso DWU|Nessuna dimensione|
|dw_cpu_percent|Percentuale CPU a livello di nodo DW|Percentuale|Media|Percentuale CPU a livello di nodo DW|dw_logical_node_id|
|dw_physical_data_read_percent|Percentuale I/O dati a livello di nodo DW|Percentuale|Media|Percentuale I/O dati a livello di nodo DW|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|database_cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|database_physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuna dimensione|
|database_log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|Nessuna dimensione|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|storage_percent|Percentuale archiviazione|Percentuale|Media|Percentuale archiviazione|Nessuna dimensione|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|database_workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuna dimensione|
|database_sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Conteggio|Media|Limite eDTU|Nessuna dimensione|
|storage_limit|Limite archiviazione|Byte|Media|Limite archiviazione|Nessuna dimensione|
|eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|Nessuna dimensione|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|
|database_storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|DatabaseResourceId|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|ElasticPoolResourceId|
|database_storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName|
|Ingress|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName|
|Ingress|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuna dimensione|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName|
|Ingress|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuna dimensione|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName|
|Ingress|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|Nessuna dimensione|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|Nessuna dimensione|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName|
|Ingress|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massima|% utilizzo unità di streaming|Nessuna dimensione|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|Nessuna dimensione|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|Nessuna dimensione|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|Nessuna dimensione|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|Nessuna dimensione|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|Nessuna dimensione|
|Errors|Errori di runtime|Conteggio|Totale|Errori di runtime|Nessuna dimensione|
|DroppedOrAdjustedEvents|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|Nessuna dimensione|
|AMLCalloutRequests|Richieste di funzioni|Conteggio|Totale|Richieste di funzioni|Nessuna dimensione|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste di funzioni non riuscite|Nessuna dimensione|
|AMLCalloutInputEvents|Eventi di funzioni|Conteggio|Totale|Eventi di funzioni|Nessuna dimensione|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Conteggio|Totale|Http 101|Istanza|
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
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Conteggio|Totale|Unità di esecuzione della funzione|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Conteggio|Totale|Http 101|Istanza|
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
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Conteggio|Media|Unità di esecuzione della funzione|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Conteggio|Media|Conteggio delle esecuzioni della funzione|Istanza|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|Requests|Requests|Conteggio|Totale|Requests|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http101|Http 101|Conteggio|Totale|Http 101|Istanza|
|Http2xx|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|ActiveRequests|Richieste attive|Conteggio|Totale|Richieste attive|Istanza|
|TotalFrontEnds|Front end totali|Conteggio|Media|Front end totali|Istanza|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|Istanza|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|Istanza|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|Istanza|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|DESCRIZIONE|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|Istanza|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|Istanza|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|Istanza|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|

## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](monitoring-overview-metrics.md)
* [Create alerts on metrics](insights-receive-alert-notifications.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](monitoring-overview-of-diagnostic-logs.md)
