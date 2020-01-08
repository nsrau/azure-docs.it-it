---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: af0c5a42b71829bbfd51d77c047af45c1df6fd4b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363643"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure. Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. L'elenco riportato di seguito include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Per cercare metriche e per accedervi, usare la [versione API 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
> Per un elenco delle metriche della piattaforma esportabili tramite le impostazioni di diagnostica, vedere [questo articolo](metrics-supported-export-diagnostic-settings.md).




## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|qpu_metric|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|private_bytes_metric|Private Bytes|Byte|Media|Byte privati.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes|Byte|Media|Byte virtuali.|ServerResourceType|
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
|mashup_engine_private_bytes_metric|Byte privati motore M|Byte|Media|Utilizzo di byte privati da processi del motore mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore M|Byte|Media|Utilizzo dei byte virtuali da processi del motore mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TotalRequests|Totale richieste Gateway (deprecato)|Conteggio|Totale|Numero di richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|SuccessfulRequests|Richieste gateway riuscite (deprecate)|Conteggio|Totale|Numero di richieste gateway riuscite: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|UnauthorizedRequests|Richieste del gateway non autorizzate (deprecate)|Conteggio|Totale|Numero di richieste del gateway non autorizzate: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|FailedRequests|Richieste gateway non riuscite (deprecate)|Conteggio|Totale|Numero di errori nelle richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|OtherRequests|Altre richieste del gateway (deprecate)|Conteggio|Totale|Numero di altre richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|Durata|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Località, nome host|
|BackendDuration|Durata delle richieste back-end|Millisecondi|Media|Durata delle richieste back-end in millisecondi|Località, nome host|
|Capacità|Capacità|Percentuale|Media|Metrica di utilizzo per il servizio ApiManagement|Percorso|
|EventHubTotalEvents|Totale eventi EventHub|Conteggio|Totale|Numero di eventi inviati a EventHub|Percorso|
|EventHubSuccessfulEvents|Eventi EventHub riusciti|Conteggio|Totale|Numero di eventi EventHub riusciti|Percorso|
|EventHubTotalFailedEvents|Eventi EventHub non riusciti|Conteggio|Totale|Numero di eventi EventHub non riusciti|Percorso|
|EventHubRejectedEvents|Eventi EventHub rifiutati|Conteggio|Totale|Numero di eventi EventHub rifiutati (configurazione errata o non autorizzata)|Percorso|
|EventHubThrottledEvents|Eventi EventHub limitati|Conteggio|Totale|Numero di eventi EventHub limitati|Percorso|
|EventHubTimedoutEvents|Timeout degli eventi di EventHub|Conteggio|Totale|Numero di eventi EventHub di timeout|Percorso|
|EventHubDroppedEvents|Eventi EventHub eliminati|Conteggio|Totale|Numero di eventi ignorati a causa del raggiungimento del limite delle dimensioni della coda|Percorso|
|EventHubTotalBytesSent|Dimensioni degli eventi di EventHub|Byte|Totale|Dimensioni totali degli eventi EventHub in byte|Percorso|
|Richieste|Richieste|Conteggio|Totale|Metriche delle richieste del gateway con più dimensioni|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Conteggio|Conteggio|Numero totale di richieste HTTP in ingresso.|Nessuno|
|FailedHttpRequestCount|FailedHttpRequestCount|Conteggio|Conteggio|Richieste HTTP non riuscite.|Nessuno|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Conteggio|Media|Latenza su una richiesta HTTP.|Nessuno|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentuale di utilizzo CPU di sistema|Percentuale|Media|Utilizzo della CPU recente per l'intero sistema|AppName, Pod|
|AppCpuUsagePercentage|Percentuale di utilizzo della CPU dell'app|Percentuale|Media|Percentuale di utilizzo CPU JVM app|AppName, Pod|
|AppMemoryCommitted|Memoria app assegnata|Byte|Media|Memoria assegnata a JVM in byte|AppName, Pod|
|AppMemoryUsed|Memoria app utilizzata|Byte|Media|Memoria app utilizzata in byte|AppName, Pod|
|AppMemoryMax|Memoria app Max|Byte|Massimo|Quantità massima di memoria in byte che può essere utilizzata per la gestione della memoria|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Dimensioni massime dei dati di generazione obsoleti disponibili|Byte|Media|Dimensioni massime del pool di memoria di generazione precedente|AppName, Pod|
|OldGenMemoryPoolBytes|Dimensioni dati di generazione precedenti|Byte|Media|Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo|AppName, Pod|
|OldGenPromotedBytes|Innalzamento di livello alle dimensioni dei dati di generazione precedente|Byte|Massimo|Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC|AppName, Pod|
|YoungGenPromotedBytes|Innalzamento di livello a dimensioni dati di generazione giovani|Byte|Massimo|Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo|AppName, Pod|
|GCPauseTotalCount|Conteggio delle pause GC|Conteggio|Totale|Conteggio delle pause GC|AppName, Pod|
|GCPauseTotalTime|Tempo totale pausa GC|Millisecondi|Totale|Tempo totale pausa GC|AppName, Pod|
|TomcatSentBytes|Totale byte inviati Tomcat|Byte|Totale|Totale byte inviati Tomcat|AppName, Pod|
|TomcatReceivedBytes|Totale byte ricevuti Tomcat|Byte|Totale|Totale byte ricevuti Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Tempo totale richiesta Tomcat|Millisecondi|Totale|Tempo totale richiesta Tomcat|AppName, Pod|
|TomcatRequestTotalCount|Conteggio totale richieste Tomcat|Conteggio|Totale|Conteggio totale richieste Tomcat|AppName, Pod|
|TomcatResponseAvgTime|Tempo medio richiesta Tomcat|Millisecondi|Media|Tempo medio richiesta Tomcat|AppName, Pod|
|TomcatRequestMaxTime|Tempo massimo richiesta Tomcat|Millisecondi|Massimo|Tempo massimo richiesta Tomcat|AppName, Pod|
|TomcatErrorCount|Errore globale Tomcat|Conteggio|Totale|Errore globale Tomcat|AppName, Pod|
|TomcatSessionActiveMaxCount|Numero massimo attivo sessione Tomcat|Conteggio|Totale|Numero massimo attivo sessione Tomcat|AppName, Pod|
|TomcatSessionAliveMaxTime|Tempo massimo attivo della sessione Tomcat|Millisecondi|Massimo|Tempo massimo attivo della sessione Tomcat|AppName, Pod|
|TomcatSessionActiveCurrentCount|Conteggio attivo della sessione Tomcat|Conteggio|Totale|Conteggio attivo della sessione Tomcat|AppName, Pod|
|TomcatSessionCreatedCount|Conteggio creazione sessione Tomcat|Conteggio|Totale|Conteggio creazione sessione Tomcat|AppName, Pod|
|TomcatSessionExpiredCount|Conteggio scaduto sessione Tomcat|Conteggio|Totale|Conteggio scaduto sessione Tomcat|AppName, Pod|
|TomcatSessionRejectedCount|Conteggio rifiutato sessione Tomcat|Conteggio|Totale|Conteggio rifiutato sessione Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentRuns|Totale esecuzioni di aggiornamenti della distribuzione|Conteggio|Totale|Totale esecuzioni di distribuzioni di aggiornamenti software|SoftwareUpdateConfigurationName, stato|
|TotalUpdateDeploymentMachineRuns|Totale esecuzioni computer di distribuzione aggiornamenti|Conteggio|Totale|Il computer di distribuzione degli aggiornamenti software totale viene eseguito in un'esecuzione di distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CoreCount|Numero di core dedicati|Conteggio|Totale|Numero totale di core dedicati nell'account Batch|Nessuno|
|TotalNodeCount|Numero di nodi dedicati|Conteggio|Totale|Numero totale di nodi dedicati nell'account Batch|Nessuno|
|LowPriorityCoreCount|Numero di core a bassa priorità|Conteggio|Totale|Numero totale di core a bassa priorità nell'account Batch|Nessuno|
|TotalLowPriorityNodeCount|Numero di nodi a bassa priorità|Conteggio|Totale|Numero totale di nodi a bassa priorità nell'account Batch|Nessuno|
|CreatingNodeCount|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|Nessuno|
|StartingNodeCount|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|Nessuno|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|Nessuno|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|Nessuno|
|IdleNodeCount|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|Nessuno|
|OfflineNodeCount|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|Nessuno|
|RebootingNodeCount|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|Nessuno|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|Nessuno|
|RunningNodeCount|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|Nessuno|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|Nessuno|
|UnusableNodeCount|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|Nessuno|
|PreemptedNodeCount|Numero di nodi annullati|Conteggio|Totale|Numero di nodi annullati|Nessuno|
|TaskStartEvent|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|Nessuno|
|TaskCompleteEvent|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|Nessuno|
|TaskFailEvent|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|Nessuno|
|PoolCreateEvent|Eventi pool creati|Conteggio|Totale|Il numero totale di pool che sono stati creati|Nessuno|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool avviati|Nessuno|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool completati|Nessuno|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Conteggio|Totale|Il numero totale di eliminazioni di pool avviate|Nessuno|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Conteggio|Totale|Il numero totale di eliminazioni di pool completate|Nessuno|
|JobDeleteCompleteEvent|Eventi eliminazione processi completati|Conteggio|Totale|Numero totale dei processi eliminati.|Nessuno|
|JobDeleteStartEvent|Eventi eliminazione processi avviati|Conteggio|Totale|Numero totale di processi da eliminare.|Nessuno|
|JobDisableCompleteEvent|Eventi disabilitazione processi completati|Conteggio|Totale|Numero totale dei processi disabilitati.|Nessuno|
|JobDisableStartEvent|Eventi disabilitazione processi avviati|Conteggio|Totale|Numero totale di processi da disabilitare.|Nessuno|
|JobStartEvent|Eventi di avvio processi|Conteggio|Totale|Numero totale di processi avviati.|Nessuno|
|JobTerminateCompleteEvent|Eventi terminazione processi completati|Conteggio|Totale|Numero totale di processi terminati.|Nessuno|
|JobTerminateStartEvent|Eventi terminazione processi avviati|Conteggio|Totale|Numero totale di processi da terminare.|Nessuno|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/Workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Processo inviato|Processo inviato|Conteggio|Totale|Numero di processi inviati|Scenario, clustername|
|Processo completato|Processo completato|Conteggio|Totale|Numero di processi completati|Scenario, clustername, ResultType|
|Totale nodi|Totale nodi|Conteggio|Media|Numero di nodi totali|Scenario, clustername|
|Nodi attivi|Nodi attivi|Conteggio|Media|Il numero di nodi in esecuzione|Scenario, clustername|
|Nodi inattivi|Nodi inattivi|Conteggio|Media|Il numero di nodi inattivi|Scenario, clustername|
|Nodi inutilizzabili|Nodi inutilizzabili|Conteggio|Media|Il numero di nodi che non possono essere usati|Scenario, clustername|
|Nodi con precedenza|Nodi con precedenza|Conteggio|Media|Numero di nodi annullati|Scenario, clustername|
|Uscita da nodi|Uscita da nodi|Conteggio|Media|Numero di nodi in uscita|Scenario, clustername|
|Core totali|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Core attivi|Core attivi|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Core inattivi|Core inattivi|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Core inutilizzabili|Core inutilizzabili|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Core con precedenza|Core con precedenza|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Uscita da Core|Uscita da Core|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Percentuale di utilizzo della quota|Percentuale di utilizzo della quota|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentuale di utilizzo CPU|Percentuale|Massimo|Percentuale di utilizzo CPU|Nodo|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Utilizzo della memoria|Nodo|
|MemoryLimit|Limite di memoria|Byte|Media|Limite di memoria|Nodo|
|MemoryUsagePercentageInDouble|Percentuale di utilizzo della memoria|Percentuale|Media|Percentuale di utilizzo della memoria|Nodo|
|StorageUsage|Utilizzo dell'archiviazione|Byte|Media|Utilizzo dell'archiviazione|Nodo|
|IOReadBytes|Byte lettura IO|Byte|Totale|Byte lettura IO|Nodo|
|IOWriteBytes|Byte scritti IO|Byte|Totale|Byte scritti IO|Nodo|
|ConnectionAccepted|Connessioni accettate|Conteggio|Totale|Connessioni accettate|Nodo|
|ConnectionHandled|Connessioni gestite|Conteggio|Totale|Connessioni gestite|Nodo|
|ConnectionActive|Connessioni attive|Conteggio|Media|Connessioni attive|Nodo|
|RequestHandled|Richieste gestite|Conteggio|Totale|Richieste gestite|Nodo|
|ProcessedBlocks|Blocchi elaborati|Conteggio|Totale|Blocchi elaborati|Nodo|
|ProcessedTransactions|Transazioni elaborate|Conteggio|Totale|Transazioni elaborate|Nodo|
|PendingTransactions|Transazioni in sospeso|Conteggio|Media|Transazioni in sospeso|Nodo|
|QueuedTransactions|Transazioni in coda|Conteggio|Media|Transazioni in coda|Nodo|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Conteggio|Massimo||ShardId|
|totalcommandsprocessed|Totale operazioni|Conteggio|Totale||ShardId|
|cachehits|Riscontri cache|Conteggio|Totale||ShardId|
|cachemisses|Mancati riscontri nella cache|Conteggio|Totale||ShardId|
|cachemissrate|Frequenza di mancata memorizzazione nella cache|Percentuale|cachemissrate||ShardId|
|getcommands|Operazioni Get|Conteggio|Totale||ShardId|
|setcommands|Set|Conteggio|Totale||ShardId|
|operationsPerSecond|Operazioni al secondo|Conteggio|Massimo||ShardId|
|evictedkeys|Chiavi rimosse|Conteggio|Totale||ShardId|
|totalkeys|Totale chiavi|Conteggio|Massimo||ShardId|
|expiredkeys|Chiavi scadute|Conteggio|Totale||ShardId|
|usedmemory|Memoria utilizzata|Byte|Massimo||ShardId|
|usedmemorypercentage|Percentuale memoria usata|Percentuale|Massimo||ShardId|
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massimo||ShardId|
|serverLoad|Carico server|Percentuale|Massimo||ShardId|
|cacheWrite|Scrittura nella cache|Byte al secondo|Massimo||ShardId|
|cacheRead|Lettura da cache|Byte al secondo|Massimo||ShardId|
|percentProcessorTime|CPU|Percentuale|Massimo||ShardId|
|cacheLatency|Microsecondi di latenza della cache (anteprima)|Conteggio|Media||ShardId|
|errori|Errors|Conteggio|Massimo||ShardId, ErrorType|
|connectedclients0|Client connessi (partizione 0)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Conteggio|Totale||Nessuno|
|cachehits0|Riscontri cache (partizione 0)|Conteggio|Totale||Nessuno|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||Nessuno|
|getcommands0|Operazioni Get (partizione 0)|Conteggio|Totale||Nessuno|
|setcommands0|Operazioni Set (partizione 0)|Conteggio|Totale||Nessuno|
|operationsPerSecond0|Operazioni al secondo (partizione 0)|Conteggio|Massimo||Nessuno|
|evictedkeys0|Chiavi rimosse (partizione 0)|Conteggio|Totale||Nessuno|
|totalkeys0|Totale chiavi (partizione 0)|Conteggio|Massimo||Nessuno|
|expiredkeys0|Chiavi scadute (partizione 0)|Conteggio|Totale||Nessuno|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massimo||Nessuno|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massimo||Nessuno|
|serverLoad0|Carico server (partizione 0)|Percentuale|Massimo||Nessuno|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massimo||Nessuno|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massimo||Nessuno|
|connectedclients1|Client connessi (partizione 1)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Conteggio|Totale||Nessuno|
|cachehits1|Riscontri cache (partizione 1)|Conteggio|Totale||Nessuno|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||Nessuno|
|getcommands1|Operazioni Get (partizione 1)|Conteggio|Totale||Nessuno|
|setcommands1|Operazioni Set (partizione 1)|Conteggio|Totale||Nessuno|
|operationsPerSecond1|Operazioni al secondo (partizione 1)|Conteggio|Massimo||Nessuno|
|evictedkeys1|Chiavi rimosse (partizione 1)|Conteggio|Totale||Nessuno|
|totalkeys1|Totale chiavi (partizione 1)|Conteggio|Massimo||Nessuno|
|expiredkeys1|Chiavi scadute (partizione 1)|Conteggio|Totale||Nessuno|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massimo||Nessuno|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massimo||Nessuno|
|serverLoad1|Carico server (partizione 1)|Percentuale|Massimo||Nessuno|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massimo||Nessuno|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massimo||Nessuno|
|connectedclients2|Client connessi (partizione 2)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Conteggio|Totale||Nessuno|
|cachehits2|Riscontri cache (partizione 2)|Conteggio|Totale||Nessuno|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||Nessuno|
|getcommands2|Operazioni Get (partizione 2)|Conteggio|Totale||Nessuno|
|setcommands2|Operazioni Set (partizione 2)|Conteggio|Totale||Nessuno|
|operationsPerSecond2|Operazioni al secondo (partizione 2)|Conteggio|Massimo||Nessuno|
|evictedkeys2|Chiavi rimosse (partizione 2)|Conteggio|Totale||Nessuno|
|totalkeys2|Totale chiavi (partizione 2)|Conteggio|Massimo||Nessuno|
|expiredkeys2|Chiavi scadute (partizione 2)|Conteggio|Totale||Nessuno|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massimo||Nessuno|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massimo||Nessuno|
|serverLoad2|Carico server (partizione 2)|Percentuale|Massimo||Nessuno|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massimo||Nessuno|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massimo||Nessuno|
|connectedclients3|Client connessi (partizione 3)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Conteggio|Totale||Nessuno|
|cachehits3|Riscontri cache (partizione 3)|Conteggio|Totale||Nessuno|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||Nessuno|
|getcommands3|Operazioni Get (partizione 3)|Conteggio|Totale||Nessuno|
|setcommands3|Operazioni Set (partizione 3)|Conteggio|Totale||Nessuno|
|operationsPerSecond3|Operazioni al secondo (partizione 3)|Conteggio|Massimo||Nessuno|
|evictedkeys3|Chiavi rimosse (partizione 3)|Conteggio|Totale||Nessuno|
|totalkeys3|Totale chiavi (partizione 3)|Conteggio|Massimo||Nessuno|
|expiredkeys3|Chiavi scadute (partizione 3)|Conteggio|Totale||Nessuno|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massimo||Nessuno|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massimo||Nessuno|
|serverLoad3|Carico server (partizione 3)|Percentuale|Massimo||Nessuno|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massimo||Nessuno|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massimo||Nessuno|
|connectedclients4|Client connessi (partizione 4)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Conteggio|Totale||Nessuno|
|cachehits4|Riscontri cache (partizione 4)|Conteggio|Totale||Nessuno|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||Nessuno|
|getcommands4|Operazioni Get (partizione 4)|Conteggio|Totale||Nessuno|
|setcommands4|Operazioni Set (partizione 4)|Conteggio|Totale||Nessuno|
|operationsPerSecond4|Operazioni al secondo (partizione 4)|Conteggio|Massimo||Nessuno|
|evictedkeys4|Chiavi rimosse (partizione 4)|Conteggio|Totale||Nessuno|
|totalkeys4|Totale chiavi (partizione 4)|Conteggio|Massimo||Nessuno|
|expiredkeys4|Chiavi scadute (partizione 4)|Conteggio|Totale||Nessuno|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massimo||Nessuno|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massimo||Nessuno|
|serverLoad4|Carico server (partizione 4)|Percentuale|Massimo||Nessuno|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massimo||Nessuno|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massimo||Nessuno|
|connectedclients5|Client connessi (partizione 5)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Conteggio|Totale||Nessuno|
|cachehits5|Riscontri cache (partizione 5)|Conteggio|Totale||Nessuno|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||Nessuno|
|getcommands5|Operazioni Get (partizione 5)|Conteggio|Totale||Nessuno|
|setcommands5|Operazioni Set (partizione 5)|Conteggio|Totale||Nessuno|
|operationsPerSecond5|Operazioni al secondo (partizione 5)|Conteggio|Massimo||Nessuno|
|evictedkeys5|Chiavi rimosse (partizione 5)|Conteggio|Totale||Nessuno|
|totalkeys5|Totale chiavi (partizione 5)|Conteggio|Massimo||Nessuno|
|expiredkeys5|Chiavi scadute (partizione 5)|Conteggio|Totale||Nessuno|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massimo||Nessuno|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massimo||Nessuno|
|serverLoad5|Carico server (partizione 5)|Percentuale|Massimo||Nessuno|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massimo||Nessuno|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massimo||Nessuno|
|connectedclients6|Client connessi (partizione 6)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Conteggio|Totale||Nessuno|
|cachehits6|Riscontri cache (partizione 6)|Conteggio|Totale||Nessuno|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||Nessuno|
|getcommands6|Operazioni Get (partizione 6)|Conteggio|Totale||Nessuno|
|setcommands6|Operazioni Set (partizione 6)|Conteggio|Totale||Nessuno|
|operationsPerSecond6|Operazioni al secondo (partizione 6)|Conteggio|Massimo||Nessuno|
|evictedkeys6|Chiavi rimosse (partizione 6)|Conteggio|Totale||Nessuno|
|totalkeys6|Totale chiavi (partizione 6)|Conteggio|Massimo||Nessuno|
|expiredkeys6|Chiavi scadute (partizione 6)|Conteggio|Totale||Nessuno|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massimo||Nessuno|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massimo||Nessuno|
|serverLoad6|Carico server (partizione 6)|Percentuale|Massimo||Nessuno|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massimo||Nessuno|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massimo||Nessuno|
|connectedclients7|Client connessi (partizione 7)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Conteggio|Totale||Nessuno|
|cachehits7|Riscontri cache (partizione 7)|Conteggio|Totale||Nessuno|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||Nessuno|
|getcommands7|Operazioni Get (partizione 7)|Conteggio|Totale||Nessuno|
|setcommands7|Operazioni Set (partizione 7)|Conteggio|Totale||Nessuno|
|operationsPerSecond7|Operazioni al secondo (partizione 7)|Conteggio|Massimo||Nessuno|
|evictedkeys7|Chiavi rimosse (partizione 7)|Conteggio|Totale||Nessuno|
|totalkeys7|Totale chiavi (partizione 7)|Conteggio|Massimo||Nessuno|
|expiredkeys7|Chiavi scadute (partizione 7)|Conteggio|Totale||Nessuno|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massimo||Nessuno|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massimo||Nessuno|
|serverLoad7|Carico server (partizione 7)|Percentuale|Massimo||Nessuno|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massimo||Nessuno|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massimo||Nessuno|
|connectedclients8|Client connessi (partizione 8)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Conteggio|Totale||Nessuno|
|cachehits8|Riscontri cache (partizione 8)|Conteggio|Totale||Nessuno|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||Nessuno|
|getcommands8|Operazioni Get (partizione 8)|Conteggio|Totale||Nessuno|
|setcommands8|Operazioni Set (partizione 8)|Conteggio|Totale||Nessuno|
|operationsPerSecond8|Operazioni al secondo (partizione 8)|Conteggio|Massimo||Nessuno|
|evictedkeys8|Chiavi rimosse (partizione 8)|Conteggio|Totale||Nessuno|
|totalkeys8|Totale chiavi (partizione 8)|Conteggio|Massimo||Nessuno|
|expiredkeys8|Chiavi scadute (partizione 8)|Conteggio|Totale||Nessuno|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massimo||Nessuno|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massimo||Nessuno|
|serverLoad8|Carico server (partizione 8)|Percentuale|Massimo||Nessuno|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massimo||Nessuno|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massimo||Nessuno|
|connectedclients9|Client connessi (partizione 9)|Conteggio|Massimo||Nessuno|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Conteggio|Totale||Nessuno|
|cachehits9|Riscontri cache (partizione 9)|Conteggio|Totale||Nessuno|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||Nessuno|
|getcommands9|Operazioni Get (partizione 9)|Conteggio|Totale||Nessuno|
|setcommands9|Operazioni Set (partizione 9)|Conteggio|Totale||Nessuno|
|operationsPerSecond9|Operazioni al secondo (partizione 9)|Conteggio|Massimo||Nessuno|
|evictedkeys9|Chiavi rimosse (partizione 9)|Conteggio|Totale||Nessuno|
|totalkeys9|Totale chiavi (partizione 9)|Conteggio|Massimo||Nessuno|
|expiredkeys9|Chiavi scadute (partizione 9)|Conteggio|Totale||Nessuno|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massimo||Nessuno|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massimo||Nessuno|
|serverLoad9|Carico server (partizione 9)|Percentuale|Massimo||Nessuno|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massimo||Nessuno|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massimo||Nessuno|
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massimo||Nessuno|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, ruleName, azione|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|Nessuno|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuno|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/FileService

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|Condivisione file|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|Condivisione file|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuno|
|FileShareSnapshotCount|Conteggio degli snapshot di condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|Condivisione file|
|FileShareSnapshotSize|Dimensioni snapshot della condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|Condivisione file|
|FileShareQuota|Dimensioni della quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|Condivisione file|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TotalCalls|Totale chiamate|Conteggio|Totale|Numero totale di chiamate.|ApiName, OperationName, Region|
|SuccessfulCalls|Chiamate riuscite|Conteggio|Totale|Numero di chiamate riuscite.|ApiName, OperationName, Region|
|TotalErrors|Totale errori|Conteggio|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Chiamate bloccate|Conteggio|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|ApiName, OperationName, Region|
|ServerErrors|Errori server|Conteggio|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|ApiName, OperationName, Region|
|ClientErrors|Errori client|Conteggio|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|ApiName, OperationName, Region|
|DataOut|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|ApiName, OperationName, Region|
|Latenza|Latenza|Millisecondi|Media|Latenza in millisecondi.|ApiName, OperationName, Region|
|CharactersTranslated|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|ApiName, OperationName, Region|
|CharactersTrained|Caratteri sottoposti a training|Conteggio|Totale|Numero totale di caratteri sottoposti a training.|ApiName, OperationName, Region|
|SpeechSessionDuration|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|ApiName, OperationName, Region|
|TotalTransactions|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|Nessuno|
|TotalTokenCalls|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuno|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuno|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|Nessuno|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|Nessuno|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|VMName|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|VMName|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Profondità coda per un disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|VMName|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|VMName|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN, VMName|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN, VMName|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|VMName|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|VMName|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuno|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuno|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|Nessuno|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|Nessuno|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo di CPU|Conteggio|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|Nessuno|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|Nessuno|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TotalPullCount|Conteggio pull totale|Conteggio|Media|Numero totale di pull di immagini|Nessuno|
|SuccessfulPullCount|Conteggio pull riuscito|Conteggio|Media|Numero di pull di immagini riuscite|Nessuno|
|TotalPushCount|Numero totale di push|Conteggio|Media|Numero totale di push di immagini|Nessuno|
|SuccessfulPushCount|Conteggio push riuscito|Conteggio|Media|Numero di push di immagini riusciti|Nessuno|
|RunDuration|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|Nessuno|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Media|Numero totale di core CPU disponibili in un cluster gestito|Nessuno|
|kube_node_status_allocatable_memory_bytes|Numero totale di memoria disponibile in un cluster gestito|Byte|Media|Numero totale di memoria disponibile in un cluster gestito|Nessuno|
|kube_pod_status_ready|Numero di pod in stato Pronto|Conteggio|Media|Numero di pod in stato Pronto|spazio dei nomi, Pod|
|kube_node_status_condition|Stati per diverse condizioni dei nodi|Conteggio|Media|Stati per diverse condizioni dei nodi|Condition, status, status2, node|
|kube_pod_status_phase|Numero di pod per fase|Conteggio|Media|Numero di pod per fase|fase, spazio dei nomi, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SuccessfullRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite effettuate dal provider personalizzato|HttpMethod, CallPath, StatusCode|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Ottiene i log disponibili per i provider di risorse personalizzati|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva di lettura (rete)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettiva di download del cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|CloudUploadThroughputPerShare|Velocità effettiva di caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|BytesUploadedToCloudPerShare|Byte cloud caricati (condivisione)|Byte|Media|Il numero totale di byte caricati in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|Archiviazione|Capacità totale:|Byte|Media|Capacità totale:|Nessuno|
|AvailableCapacity|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di Reporting.|Nessuno|
|CloudUploadThroughput|Velocità effettiva di caricamento cloud|Byte al secondo|Media|La velocità effettiva di caricamento nel cloud in Azure durante il periodo di Reporting.|Nessuno|
|CloudReadThroughput|Velocità effettiva di download del cloud|Byte al secondo|Media|La velocità effettiva di download del cloud in Azure durante il periodo di Reporting.|Nessuno|
|BytesUploadedToCloud|Byte cloud caricati (dispositivo)|Byte|Media|Il numero totale di byte caricati in Azure da un dispositivo durante il periodo di Reporting.|Nessuno|
|HyperVVirtualProcessorUtilization|Calcolo Edge-percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|HyperVMemoryUtilization|Calcolo Edge-utilizzo memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale||PipelineName, ActivityName|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale||PipelineName, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||FailureType, nome|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||FailureType, nome|
|PipelineCancelledRuns|Metriche delle esecuzioni di pipeline annullate|Conteggio|Totale||FailureType, nome|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|ActivityCancelledRuns|Metriche delle esecuzioni di attività annullate|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Nome, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Nome, FailureType|
|TriggerCancelledRuns|Il trigger annullato esegue le metriche|Conteggio|Totale||Nome, FailureType|
|IntegrationRuntimeCpuPercentage|Uso della CPU di Integration runtime|Percentuale|Media||IntegrationRuntimeName, nodeName|
|IntegrationRuntimeAvailableMemory|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName, nodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Durata coda di Integration Runtime|Secondi|Media||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Lunghezza coda runtime di integrazione|Conteggio|Media||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Numero di nodi disponibili in Integration Runtime|Conteggio|Media||IntegrationRuntimeName|
|MaxAllowedResourceCount|Conteggio entità massime consentite|Conteggio|Massimo||Nessuno|
|MaxAllowedFactorySizeInGbUnits|Dimensioni massime consentite per le factory (unità GB)|Conteggio|Massimo||Nessuno|
|ResourceCount|Conteggio entità totali|Conteggio|Massimo||Nessuno|
|FactorySizeInGbUnits|Dimensioni totali Factory (unità GB)|Conteggio|Massimo||Nessuno|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|Nessuno|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|Nessuno|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati|Nessuno|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|Nessuno|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|Nessuno|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati|Nessuno|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massimo|Quantità totale di dati archiviati nell'account|Nessuno|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuno|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuno|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|Nessuno|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|Nessuno|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Risorse di archiviazione usate|Byte|Media|Risorse di archiviazione usate|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Risorse di archiviazione usate|Byte|Media|Risorse di archiviazione usate|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Risorse di archiviazione usate|Byte|Media|Risorse di archiviazione usate|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massimo|Ritardo replica in secondi|Nessuno|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massimo|Ritardo in byte della replica più in ritardo|Nessuno|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|IOPS|Operazioni di I/O al secondo|Conteggio|Media|Operazioni di i/o al secondo|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Risorse di archiviazione usate|Byte|Media|Risorse di archiviazione usate|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuno|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuno|
|c2d.commands.egress.complete.success|Recapito messaggi C2D completati|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|Nessuno|
|c2d.commands.egress.abandon.success|Messaggi C2D abbandonati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|Nessuno|
|c2d.commands.egress.reject.success|Messaggi C2D rifiutati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|Nessuno|
|C2DMessagesExpired|Messaggi C2D scaduti (anteprima)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|Nessuno|
|devices.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuno|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuno|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|Nessuno|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|Nessuno|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |Nessuno|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|Nessuno|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|Nessuno|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|Nessuno|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|Nessuno|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|Nessuno|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|Nessuno|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|Nessuno|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|Nessuno|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|Nessuno|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|Nessuno|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|Nessuno|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|Nessuno|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|Nessuno|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|Nessuno|
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento (https://aka.ms/ioteventgrid).|ResourceId, result, EventType|
|EventGridLatency|Latenza griglia di eventi (anteprima)|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|ResourceId, EventType|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuno|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|Nessuno|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuno|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuno|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|Nessuno|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuno|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|Nessuno|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|Nessuno|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|Nessuno|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|Nessuno|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|Nessuno|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|Nessuno|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|Nessuno|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuno|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|Nessuno|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuno|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|Nessuno|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|Nessuno|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|Nessuno|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|Nessuno|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|Nessuno|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|Nessuno|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|Nessuno|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|Nessuno|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|Nessuno|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|Nessuno|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|Nessuno|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|Nessuno|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|Nessuno|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|Nessuno|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|Nessuno|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|Nessuno|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna|Nessuno|
|deviceDataUsage|Utilizzo totale dei dati del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuno|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuno|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuno|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|Nessuno|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|Nessuno|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, stato|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, stato, protocollo|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|AddRegion|Area aggiunta|Conteggio|Conteggio|Area aggiunta|Area|
|AvailableStorage|Spazio di archiviazione disponibile|Byte|Totale|Totale spazio di archiviazione disponibile segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni Cassandra chiuse, segnalate a una granularità di 1 minuto|APIType, Region, ClosureReason|
|CassandraRequestCharges|Addebiti richieste Cassandra|Conteggio|Totale|Ur utilizzate per le richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CreateAccount|Account creato|Conteggio|Conteggio|Account creato|Nessuno|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DeleteAccount|Account eliminato|Conteggio|Conteggio|Account eliminato|Nessuno|
|DocumentCount|Conteggio documenti|Conteggio|Totale|Numero totale di documenti segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DocumentQuota|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalata con granularità di 5 minuti|CollectionName, DatabaseName, Region|
|IndexUsage|Utilizzo indice|Byte|Totale|Totale utilizzo di indici restituiti a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests (Richieste di metadati)|Conteggio|Conteggio|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, consentendo di enumerare le raccolte, i database e così via e le relative configurazioni gratuitamente.|DatabaseName, CollectionName, Region, StatusCode, Role|
|MongoRequestCharge|Mongo Request Charge (Addebito richiesta Mongo)|Conteggio|Totale|Unità richiesta Mongo utilizzate|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Richieste Mongo|Conteggio|Conteggio|Numero di richieste Mongo eseguite|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsCount|Frequenza di richieste Mongo|Conteggio al secondo|Media|Numero di richieste Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsDelete|Frequenza delle richieste di eliminazione Mongo|Conteggio al secondo|Media|Richiesta di eliminazione Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsInsert|Frequenza di richieste di inserimento Mongo|Conteggio al secondo|Media|Numero di inserimento di Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsQuery|Frequenza di richieste di query Mongo|Conteggio al secondo|Media|Richieste di query Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsUpdate|Frequenza di richieste di aggiornamento Mongo|Conteggio al secondo|Media|Richiesta di aggiornamento Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Velocità effettiva con provisioning|Conteggio|Massimo|Velocità effettiva con provisioning|DatabaseName, CollectionName|
|RegionFailover|Area sottoposta a failover|Conteggio|Conteggio|Area sottoposta a failover|Nessuno|
|RemoveRegion|Area rimossa|Conteggio|Conteggio|Area rimossa|Area|
|ReplicationLatency|Latenza di replica P99|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion,TargetRegion|
|ServiceAvailability|Disponibilità del servizio|Percentuale|Media|L'account richiede disponibilità a una granularità di un'ora, giorno o mese|Nessuno|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|UpdateAccountKeys|Chiavi dell'account aggiornate|Conteggio|Conteggio|Chiavi dell'account aggiornate|KeyType|
|UpdateAccountNetworkSettings|Impostazioni di rete account aggiornate|Conteggio|Conteggio|Impostazioni di rete account aggiornate|Nessuno|
|UpdateAccountReplicationSettings|Impostazioni di replica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di replica dell'account aggiornate|Nessuno|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TransactionCount|Conteggio transazioni|Conteggio|Conteggio|Conteggio totale transazioni|TransactionCount|
|SuccessCount|Conteggio completo|Conteggio|Conteggio|Conteggio transazioni riuscite|SuccessCount|
|FailureCount|Failure Count|Conteggio|Conteggio|Conteggio transazioni non riuscite|FailureCount|
|SuccessLatency|Latenza riuscita|Millisecondi|Media|Latenza delle transazioni riuscite|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuno|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuno|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuno|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuno|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuno|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuno|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuno|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|Dimensioni|Dimensioni|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (deprecate)|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (deprecato)|Nessuno|
|SUCCREQ|Richieste riuscite (deprecate)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|FAILREQ|Richieste non riuscite (deprecate)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|SVRBSY|Errori di server occupato (deprecato)|Conteggio|Totale|Totale errori server occupati per uno spazio dei nomi (deprecato)|Nessuno|
|INTERR|Errori interni del server (deprecati)|Conteggio|Totale|Totale errori interni del server per uno spazio dei nomi (deprecato)|Nessuno|
|MISCERR|Altri errori (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|INMSGS|Messaggi in ingresso (obsoleti) (deprecato)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in ingresso (deprecata)|Nessuno|
|EHINMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi (deprecato)|Nessuno|
|OUTMSGS|Messaggi in uscita (obsoleti) (deprecato)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in uscita (deprecata)|Nessuno|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecato)|Nessuno|
|EHINMBS|Byte in ingresso (obsoleti) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in ingresso (deprecata)|Nessuno|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva del messaggio in ingresso dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuno|
|EHOUTMBS|Byte in uscita (obsoleti) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in uscita (deprecata)|Nessuno|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva del messaggio in uscita dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuno|
|EHABL|Archivia messaggi backlog (deprecato)|Conteggio|Totale|Messaggi di archiviazione dell'hub eventi nel backlog per uno spazio dei nomi (deprecato)|Nessuno|
|EHAMSGS|Messaggi di archivio (deprecati)|Conteggio|Totale|Messaggi archiviati dell'hub eventi in uno spazio dei nomi (deprecato)|Nessuno|
|EHAMBS|Archiviazione della velocità effettiva del messaggio (deprecata)|Byte|Totale|Velocità effettiva del messaggio archiviato nell'hub eventi in uno spazio dei nomi (deprecato)|Nessuno|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|OperationResult|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|Nessuno|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|Nessuno|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|Nessuno|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|Nessuno|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|Nessuno|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|Nessuno|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|Nessuno|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|Nessuno|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|Nessuno|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|Nessuno|
|CPU|CPU|Percentuale|Massimo|Uso CPU per il cluster dell'hub eventi in percentuale|Ruolo|
|AvailableMemory|Memoria disponibile|Percentuale|Massimo|Memoria disponibile per il cluster di hub eventi come percentuale della memoria totale.|Ruolo|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Numero di thread di lavoro attivi|Conteggio|Massimo|Numero di thread di lavoro attivi|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|Nessuno|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult/Name, availabilityResult/location|
|availabilityResults/count|Test della disponibilità|Conteggio|Conteggio|Conteggio dei test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|availabilityResults/duration|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|Nessuno|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|Nessuno|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|Nessuno|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|Nessuno|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|Nessuno|
|dependencies/count|Chiamate di dipendenza|Conteggio|Conteggio|Numero delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Errori delle chiamate di dipendenza|Conteggio|Conteggio|Numero di chiamate di dipendenza non riuscite eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|pageViews/count|Visualizzazioni pagina|Conteggio|Conteggio|Numero delle visualizzazioni di pagina.|operazione/sintetica, cloud/roleName|
|pageViews/duration|Tempo di caricamento della visualizzazione pagina|Millisecondi|Media|Tempo di caricamento della visualizzazione pagina|operazione/sintetica, cloud/roleName|
|performanceCounters/requestExecutionTime|Tempo di esecuzione della richiesta HTTP|Millisecondi|Media|Tempo di esecuzione della richiesta più recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Richieste HTTP nella coda dell'applicazione|Conteggio|Media|Lunghezza della coda di richieste dell'applicazione.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Frequenza delle richieste HTTP|Conteggio al secondo|Media|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Frequenza di eccezioni|Conteggio al secondo|Media|Conteggio delle eccezioni gestite e non gestite restituite in Windows, incluse le eccezioni .NET e non gestite che vengono convertite in eccezioni .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocità di I/O di elaborazione|Byte al secondo|Media|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU processo|Percentuale|Media|Percentuale di tempo trascorso per l'uso del processore da parte di tutti i thread di processo per eseguire le istruzioni. Il valore può variare tra 0 e 100. Questa metrica indica le prestazioni del solo processo w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo processore|Percentuale|Media|Percentuale di tempo che il processore dedica a thread non inattivi.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memoria disponibile|Byte|Media|Memoria fisica immediatamente disponibile per l'allocazione a un processo o usabile dal sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Byte privati processo|Byte|Media|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.|cloud/roleInstance|
|requests/duration|Tempo di risposta del server|Millisecondi|Media|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.|Request/performanceBucket, Request/resultCode, Operation/Synthetic, cloud/roleInstance, Request/Success, cloud/roleName|
|requests/count|Richieste server|Conteggio|Conteggio|Numero delle richieste HTTP completate.|Request/performanceBucket, Request/resultCode, Operation/Synthetic, cloud/roleInstance, Request/Success, cloud/roleName|
|requests/failed|Richieste non riuscite|Conteggio|Conteggio|Numero delle richieste HTTP contrassegnate come non riuscite. Nella maggior parte dei casi si tratta di richieste con un codice di risposta maggiore o uguale a 400 e diverso da 401.|Request/performanceBucket, Request/resultCode, Request/Success, Operation/Synthetic, cloud/roleInstance, cloud/roleName|
|richieste/frequenza|Frequenza richieste server|Conteggio al secondo|Media|Frequenza delle richieste server al secondo|Request/performanceBucket, Request/resultCode, Operation/Synthetic, cloud/roleInstance, Request/Success, cloud/roleName|
|exceptions/count|Eccezioni|Conteggio|Conteggio|Numero combinato di tutte le eccezioni non rilevate.|Cloud/roleName, cloud/roleInstance, client/tipo|
|exceptions/browser|Eccezioni del browser|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nel browser.|client/server, cloud/roleName|
|exceptions/server|Eccezioni del server|Conteggio|Conteggio|Numero delle eccezioni non rilevate generate nell'applicazione server.|client/server, cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Conteggio|Conteggio|Numero documenti di analisi|Trace/severityLevel, Operation/Synthetic, cloud/roleName, cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totale dei risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturazione complessiva dell'insieme di credenziali|Percentuale|Media|Capacità dell'insieme di credenziali utilizzata|ActivityType, ActivityName, TransactionType|
|Disponibilità|Disponibilità complessiva dell'insieme di credenziali|Percentuale|Media|Disponibilità richieste di archiviazione|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CacheUtilization|Utilizzo della cache|Percentuale|Media|Livello di uso nell'ambito del cluster|Nessuno|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query in secondi|QueryStatus|
|IngestionUtilization|Utilizzo dell'inserimento|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|Nessuno|
|KeepAlive|Keep-Alive|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|Nessuno|
|IngestionVolumeInMB|Volume di inserimento (in MB)|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Database|
|IngestionLatencyInSeconds|Latenza inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|Nessuno|
|EventsProcessedForEventHubs|Eventi elaborati (per gli hub eventi/tutto)|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi/tutto|EventStatus|
|IngestionResult|Risultato inserimento|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|CPU|CPU|Percentuale|Media|Livello di uso della CPU|Nessuno|
|ContinuousExportNumOfRecordsExported|Esportazione continua: numero di record esportati|Conteggio|Totale|Numero di record esportati, generati per ogni artefatto di archiviazione scritto durante l'operazione di esportazione|Nessuno|
|ExportUtilization|Esporta utilizzo|Percentuale|Massimo|Esporta utilizzo|Nessuno|
|ContinuousExportPendingCount|Conteggio in sospeso esportazione continua|Conteggio|Massimo|Numero di processi di esportazione continua in sospeso pronti per l'esecuzione|Nessuno|
|ContinuousExportMaxLatenessMinutes|Minuti di ritardo massimo esportazione continua|Conteggio|Massimo|Ritardo massimo in minuti di tutte le esportazioni continue in sospeso e pronte per l'esecuzione|Nessuno|
|ContinuousExportResult|Risultato dell'esportazione continua|Conteggio|Conteggio|Indica se l'esportazione continua ha avuto esito positivo o negativo|ContinuousExportName, result, database|
|StreamingIngestDuration|Durata inserimento flusso|Millisecondi|Media|Durata di inserimento del flusso in millisecondi|Nessuno|
|StreamingIngestDataRate|Velocità dati di inserimento dei flussi|Conteggio|Media|Velocità dati di inserimento dei flussi (MB al secondo)|Nessuno|
|SteamingIngestRequestRate|Frequenza delle richieste di inserimento di flussi|Conteggio|RateRequestsPerSecond|Frequenza delle richieste di inserimento in streaming (richieste al secondo)|Nessuno|
|StreamingIngestResults|Risultato inserimento flusso|Conteggio|Media|Risultato inserimento flusso|Risultato|
|TotalNumberOfConcurrentQueries|Numero totale di query simultanee|Conteggio|Totale|Numero totale di query simultanee|Nessuno|
|TotalNumberOfThrottledQueries|Numero totale di query limitate|Conteggio|Totale|Numero totale di query limitate|Nessuno|
|TotalNumberOfThrottledCommands|Numero totale di comandi limitati|Conteggio|Totale|Numero totale di comandi limitati|CommandType|
|TotalNumberOfExtents|Numero totale di extent|Conteggio|Totale|Numero totale di extent dei dati|Nessuno|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuno|
|RunStartThrottledEvents|Eseguire gli eventi di avvio limitato|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuno|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuno|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuno|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuno|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuno|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuno|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuno|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuno|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuno|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuno|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuno|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuno|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuno|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuno|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuno|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuno|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuno|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuno|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuno|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|Nessuno|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|Nessuno|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|Nessuno|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuno|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuno|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|Nessuno|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuno|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuno|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|Nessuno|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuno|
|RunStartThrottledEvents|Eseguire gli eventi di avvio limitato|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuno|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuno|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuno|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuno|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuno|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuno|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuno|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuno|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuno|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuno|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuno|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuno|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuno|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuno|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuno|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuno|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuno|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuno|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuno|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente di Integration Services.|Nessuno|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente di Integration Services.|Nessuno|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|Nessuno|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo memoria connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|Nessuno|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Esecuzioni completate|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni completate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni avviate|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni avviate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Registrazione del modello completata|Registrazione del modello completata|Conteggio|Totale|Numero di registrazioni del modello riuscite in questa area di lavoro|Scenario|
|Registrazione modello non riuscita|Registrazione modello non riuscita|Conteggio|Totale|Numero di registrazioni del modello non riuscite in questa area di lavoro|Scenario, StatusCode|
|Distribuzione modello avviata|Distribuzione modello avviata|Conteggio|Totale|Numero di distribuzioni di modelli avviate in questa area di lavoro|Scenario|
|Distribuzione modello completata|Distribuzione modello completata|Conteggio|Totale|Numero di distribuzioni di modelli riuscite in questa area di lavoro|Scenario|
|Distribuzione modello non riuscito|Distribuzione modello non riuscito|Conteggio|Totale|Numero di distribuzioni di modelli non riuscite in questa area di lavoro|Scenario, StatusCode|
|Totale nodi|Totale nodi|Conteggio|Media|Numero totale di nodi. Il totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi Premepted, lasciando nodi|Scenario, clustername|
|Nodi attivi|Nodi attivi|Conteggio|Media|Numero di nodi Active. Questi sono i nodi che eseguono attivamente un processo.|Scenario, clustername|
|Nodi inattivi|Nodi inattivi|Conteggio|Media|Numero di nodi inattivi. I nodi inattivi sono i nodi che non eseguono alcun processo, ma possono accettare un nuovo processo, se disponibile.|Scenario, clustername|
|Nodi inutilizzabili|Nodi inutilizzabili|Conteggio|Media|Numero di nodi inutilizzabili. I nodi inutilizzabili non sono funzionali a causa di un problema irrisolvibile. Azure ricicla questi nodi.|Scenario, clustername|
|Nodi con precedenza|Nodi con precedenza|Conteggio|Media|Numero di nodi con precedenza. Questi nodi sono i nodi con priorità bassa che vengono sottratti dal pool di nodi disponibile.|Scenario, clustername|
|Uscita da nodi|Uscita da nodi|Conteggio|Media|Numero di nodi in uscita. Lasciando i nodi sono i nodi che hanno appena terminato l'elaborazione di un processo e passano allo stato inattivo.|Scenario, clustername|
|Core totali|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Core attivi|Core attivi|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Core inattivi|Core inattivi|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Core inutilizzabili|Core inutilizzabili|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Core con precedenza|Core con precedenza|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Uscita da Core|Uscita da Core|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Percentuale di utilizzo della quota|Percentuale di utilizzo della quota|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Conteggio|Media|CPU (anteprima)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Conteggio|Media|GPU (anteprima)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Uso|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilità|Disponibilità|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/le entità streamingendpoint

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte.|OutputFormat|
|SuccessE2ELatency|Latenza end-to-end riuscita|Millisecondi|Media|Latenza media per le richieste con esito positivo in millisecondi.|OutputFormat|
|Richieste|Richieste|Conteggio|Totale|Richieste a un endpoint di streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|AssetQuota|Quota asset|Conteggio|Media|Numero di asset consentiti per l'account del servizio multimediale corrente|Nessuno|
|AssetCount|Conteggio asset|Conteggio|Media|Numero di asset già creati nell'account del servizio multimediale corrente|Nessuno|
|AssetQuotaUsedPercentage|Percentuale di utilizzo della quota di asset|Percentuale|Media|Percentuale di risorse usate nell'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyQuota|Quota dei criteri della chiave simmetrica|Conteggio|Media|Numero di criteri di chiave simmetrica consentiti per l'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyCount|Conteggio dei criteri della chiave simmetrica|Conteggio|Media|Quanti criteri chiave simmetrica sono già stati creati nell'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri della chiave simmetrica|Percentuale|Media|Percentuale di utilizzo del criterio della chiave simmetrica nell'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyQuota|Quota dei criteri di streaming|Conteggio|Media|Quanti criteri di streaming sono consentiti per l'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyCount|Conteggio dei criteri di flusso|Conteggio|Media|Quanti criteri di streaming sono già stati creati nell'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri di flusso|Percentuale|Media|Percentuale di utilizzo dei criteri di flusso nell'account del servizio multimediale corrente|Nessuno|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/volumes

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|AverageReadLatency|Latenza media in lettura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di lettura|Nessuno|
|AverageWriteLatency|Latenza media in scrittura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di scrittura|Nessuno|
|VolumeLogicalSize|Dimensioni logiche del volume|Byte|Media|Dimensioni logiche del volume (byte usati)|Nessuno|
|VolumeSnapshotSize|Dimensioni dello snapshot del volume|Byte|Media|Dimensioni di tutti gli snapshot nel volume|Nessuno|
|ReadIops|Operazioni di I/O in lettura|Conteggio al secondo|Media|Operazioni di I/O in lettura al secondo|Nessuno|
|WriteIops|Operazioni di I/O in scrittura|Conteggio al secondo|Media|Operazioni di I/O in scrittura al secondo|Nessuno|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Dimensioni usate del pool di volumi|Byte|Media|Dimensioni usate del pool|Nessuno|
|VolumePoolTotalLogicalSize|Dimensioni logiche totale del pool di volumi|Byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|Nessuno|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Byte|Totale|Numero di byte inviati dall'interfaccia di rete|Nessuno|
|BytesReceivedRate|Byte ricevuti|Byte|Totale|Numero di byte ricevuti dall'interfaccia di rete|Nessuno|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|Nessuno|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|Nessuno|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media del percorso dati di Load Balancer per periodo di tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Stato probe di integrità|Conteggio|Media|Stato probe di integrità media di Load Balancer per periodo di tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, direzione|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, direzione|
|SYNCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, direzione|
|SnatConnectionCount|Numero di connessioni SNAT|Conteggio|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Porte SNAT allocate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT allocate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Porte SNAT usate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT usate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query per zona DNS|Nessuno|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS|Nessuno|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità di set di record usata da una zona DNS|Nessuno|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS pacchetti in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso|Nessuno|
|PacketsDroppedDDoS|DDoS pacchetti in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso eliminati|Nessuno|
|PacketsForwardedDDoS|DDoS pacchetti in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso inoltrati|Nessuno|
|TCPPacketsInDDoS|DDoS pacchetti TCP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso|Nessuno|
|TCPPacketsDroppedDDoS|DDoS pacchetti TCP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso eliminati|Nessuno|
|TCPPacketsForwardedDDoS|DDoS pacchetti TCP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso inoltrati|Nessuno|
|UDPPacketsInDDoS|DDoS pacchetti UDP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso|Nessuno|
|UDPPacketsDroppedDDoS|DDoS pacchetti UDP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso eliminati|Nessuno|
|UDPPacketsForwardedDDoS|DDoS pacchetti UDP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso inoltrati|Nessuno|
|BytesInDDoS|DDoS byte in ingresso|Byte al secondo|Massimo|DDoS byte in ingresso|Nessuno|
|BytesDroppedDDoS|DDoS byte in ingresso eliminati|Byte al secondo|Massimo|DDoS byte in ingresso eliminati|Nessuno|
|BytesForwardedDDoS|DDoS byte in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte in ingresso inoltrati|Nessuno|
|TCPBytesInDDoS|DDoS byte TCP in ingresso|Byte al secondo|Massimo|DDoS byte TCP in ingresso|Nessuno|
|TCPBytesDroppedDDoS|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte TCP in ingresso eliminati|Nessuno|
|TCPBytesForwardedDDoS|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte TCP in ingresso inoltrati|Nessuno|
|UDPBytesInDDoS|DDoS byte UDP in ingresso|Byte al secondo|Massimo|DDoS byte UDP in ingresso|Nessuno|
|UDPBytesDroppedDDoS|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte UDP in ingresso eliminati|Nessuno|
|UDPBytesForwardedDDoS|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte UDP in ingresso inoltrati|Nessuno|
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Conteggio|Massimo|Sotto attacco DDoS o no|Nessuno|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Nessuno|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Nessuno|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Conteggio al secondo|Massimo|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Nessuno|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Porta|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Porta, direzione|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Porta, direzione|
|SynCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Porta, direzione|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo di round trip per i ping a una macchina virtuale|Millisecondi|Media|Tempo di round trip per i ping inviati a una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ping non riusciti a una macchina virtuale|Percentuale|Media|Percentuale del numero di ping non riusciti in totale ping inviati di una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ApplicationRuleHit|Numero di passaggi delle regole dell'applicazione|Conteggio|Totale|Numero di volte in cui sono state raggiunte le regole applicazione|Stato, motivo, protocollo|
|NetworkRuleHit|Numero di passaggi delle regole di rete|Conteggio|Totale|Numero di volte in cui sono state eseguite le regole di rete|Stato, motivo, protocollo|
|FirewallHealth|Stato di integrità del firewall|Percentuale|Media|Stato di integrità del firewall|Stato, motivo|
|DataProcessing|Dati elaborati|Byte|Totale|Quantità totale di dati elaborati dal firewall|Nessuno|
|SNATPortUtilization|Utilizzo delle porte SNAT|Percentuale|Media|Utilizzo delle porte SNAT|Nessuno|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuno|
|UnhealthyHostCount|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Totale richieste|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Richieste al minuto per host integro|Conteggio|Media|Numero medio di richieste al minuto per host back-end integro in un pool|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Current Connections (Connessioni correnti)|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|Nessuno|
|CpuUtilization|Utilizzo CPU|Percentuale|Media|Utilizzo corrente della CPU del gateway applicazione|Nessuno|
|CapacityUnits|Unità di capacità correnti|Conteggio|Media|Unità di capacità utilizzate|Nessuno|
|ComputeUnits|Unità di calcolo correnti|Conteggio|Media|Unità di calcolo utilizzate|Nessuno|
|BackendResponseStatus|Stato della risposta back-end|Conteggio|Totale|Numero di codici di risposta HTTP generati dai membri back-end. Non sono inclusi i codici di risposta generati dal gateway applicazione.|BackendServer, integri, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protocollo TLS client|Conteggio|Totale|Numero di richieste TLS e non TLS avviate dal client che hanno stabilito la connessione al gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base al protocollo TLS della dimensione.|Listener, TlsProtocol|
|BytesSent|Byte inviati|Byte|Totale|Numero totale di byte inviati dal gateway applicazione ai client|Listener|
|BytesReceived|Byte ricevuti|Byte|Totale|Numero totale di byte ricevuti dal gateway applicazione dai client|Listener|
|ClientRtt|RTT client|Millisecondi|Media|Tempo medio round trip tra i client e il gateway applicazione. Questa metrica indica il tempo necessario per stabilire le connessioni e restituire i riconoscimenti|Listener|
|ApplicationGatewayTotalTime|Tempo totale del gateway applicazione|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta e la relativa risposta da inviare. Viene calcolato come media dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al momento in cui termina l'operazione di invio della risposta. È importante tenere presente che questo in genere include il tempo di elaborazione del gateway applicazione, il tempo in cui i pacchetti di richiesta e risposta vengono spostati in rete e il tempo impiegato dal server back-end per rispondere.|Listener|
|BackendConnectTime|Tempo di connessione back-end|Millisecondi|Media|Tempo impiegato per stabilire una connessione con un server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo di risposta primo byte back-end|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta, il tempo di elaborazione approssimativo del server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendLastByteResponseTime|Tempo di risposta ultimo byte back-end|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta|Listener, BackendServer, integri, BackendHttpSetting|
|MatchedCount|Distribuzione della regola totale del firewall applicazione Web|Conteggio|Totale|Distribuzione della regola totale del firewall applicazione Web per il traffico in ingresso|RuleGroup, RuleId|
|BlockedCount|Distribuzione della regola richieste bloccate del Web Application Firewall|Conteggio|Totale|Distribuzione della regola richieste bloccate del Web Application Firewall|RuleGroup, RuleId|
|BlockedReqCount|Conteggio richieste bloccate del Web Application Firewall|Conteggio|Totale|Conteggio richieste bloccate del Web Application Firewall|Nessuno|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|AverageBandwidth|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|Nessuno|
|P2SBandwidth|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|Nessuno|
|P2SConnectionCount|Numero di connessioni da punto a sito|Conteggio|Massimo|Numero di connessioni da punto a sito di un gateway|Protocollo|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Conteggio|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Conteggio|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Conteggio|Media|Livello di luce RX in dBm|Collegamento, corsia|
|TxLightLevel|TxLightLevel|Conteggio|Media|Livello di luce TX in dBm|Collegamento, corsia|
|AdminState|AdminState|Conteggio|Media|Stato amministratore della porta|Collegamento|
|LineProtocol|LineProtocol|Conteggio|Media|Stato del protocollo di riga della porta|Collegamento|
|PortBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Collegamento|
|PortBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Collegamento|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeredCircuitSKey|
|BgpAvailability|Disponibilità BGP|Percentuale|Media|Disponibilità BGP da MSEE verso tutti i peer.|Peering, peer|
|ArpAvailability|Disponibilità ARP|Percentuale|Media|Disponibilità ARP da MSEE verso tutti i peer.|Peering, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|Conteggio al secondo|Media|Bit in ingresso dei dati eliminati al secondo|Nessuno|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|Conteggio al secondo|Media|Bit di dati in uscita eliminati al secondo|Nessuno|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuno|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuno|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Conteggio|Massimo|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ProbesFailedPercent|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|Nessuno|
|AverageRoundtripMs|Tempo medio di round trip (MS)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|Nessuno|
|ChecksFailedPercent|Verifica percentuale non riuscita (anteprima)|Percentuale|Media|% dei controlli di monitoraggio della connettività non riusciti|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tempo di round trip (MS) (anteprima)|Millisecondi|Media|Tempo di round trip in millisecondi per i controlli di monitoraggio della connettività|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|RequestCount|Conteggio richieste|Conteggio|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Dimensioni risposta fatturabile|Byte|Totale|Numero di byte fatturabili (2 KB minimo per richiesta) inviati come risposte dal proxy HTTP/S ai client.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Conteggio delle richieste del back-end|Conteggio|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latenza della richiesta del back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|TotalLatency|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentuale di integrità del back-end|Percentuale|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Back-end, integri|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, ruleName, azione|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|registration.all|Operazioni di registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |Nessuno|
|registration.create|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|Nessuno|
|registration.update|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|Nessuno|
|registration.get|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|Nessuno|
|registration.delete|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|Nessuno|
|incoming|Messaggi in ingresso|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |Nessuno|
|incoming.scheduled|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuno|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuno|
|scheduled.pending|Notifiche pianificate in sospeso|Conteggio|Totale|Notifiche pianificate in sospeso|Nessuno|
|installation.all|Operazioni di gestione installazione|Conteggio|Totale|Operazioni di gestione installazione|Nessuno|
|installation.get|Ottieni operazioni di installazione|Conteggio|Totale|Ottieni operazioni di installazione|Nessuno|
|installation.upsert|Crea o aggiorna operazioni di installazione|Conteggio|Totale|Crea o aggiorna operazioni di installazione|Nessuno|
|installation.patch|Operazioni di installazione patch|Conteggio|Totale|Operazioni di installazione patch|Nessuno|
|installation.delete|Elimina operazioni di installazione|Conteggio|Totale|Elimina operazioni di installazione|Nessuno|
|outgoing.allpns.success|Notifiche riuscite|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuno|
|outgoing.allpns.invalidpayload|Errori payload|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|Nessuno|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Conteggio|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|Nessuno|
|outgoing.allpns.channelerror|Errori canale|Conteggio|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|Nessuno|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Conteggio|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|Nessuno|
|outgoing.wns.success|Notifiche WNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuno|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|Nessuno|
|outgoing.wns.badchannel|Errore canale WNS non valido|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|Nessuno|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|Nessuno|
|outgoing.wns.throttled|Notifiche WNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|Nessuno|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Conteggio|Totale|Windows Live non è raggiungibile.|Nessuno|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Conteggio|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|Nessuno|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|Nessuno|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Conteggio|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|Nessuno|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Conteggio|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|Nessuno|
|outgoing.wns.channelthrottled|Canale WNS limitato|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nessuno|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nessuno|
|outgoing.wns.dropped|Notifiche WNS eliminate|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|Nessuno|
|outgoing.wns.pnserror|Errori WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|Nessuno|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|Nessuno|
|outgoing.apns.success|Notifiche APNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuno|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuno|
|outgoing.apns.badchannel|Errore canale APNS non valido|Conteggio|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|Nessuno|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Conteggio|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|Nessuno|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|Nessuno|
|outgoing.apns.pnserror|Errori APNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|Nessuno|
|outgoing.gcm.success|Notifiche GCM completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuno|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuno|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|Nessuno|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|Nessuno|
|outgoing.gcm.throttled|Notifiche GCM limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|Nessuno|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Conteggio|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|Nessuno|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|Nessuno|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|Nessuno|
|outgoing.gcm.pnserror|Errori GCM|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|Nessuno|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|Nessuno|
|outgoing.mpns.success|Notifiche MPNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuno|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuno|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|Nessuno|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|Nessuno|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Conteggio|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|Nessuno|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|Nessuno|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Conteggio|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|Nessuno|
|outgoing.mpns.pnserror|Errori MPNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|Nessuno|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuno|
|notificationhub.pushes|Tutte le notifiche in uscita|Conteggio|Totale|Tutte le notifiche in uscita dell'hub di notifica|Nessuno|
|incoming.all.requests|Tutte le richieste in ingresso|Conteggio|Totale|Totale richieste in ingresso per un hub di notifica|Nessuno|
|incoming.all.failedrequests|Tutte le richieste in ingresso non riuscite|Conteggio|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|Nessuno|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Conteggio|Media|Average_% Free Inodes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Conteggio|Media|Average_% Free Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Conteggio|Media|Average_% Used Inodes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Conteggio|Media|Average_% Used Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Byte letti da disco/sec|Conteggio|Media|Average_Disk Read Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec|Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Byte scritti su disco/sec|Conteggio|Media|Average_Disk Write Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Conteggio|Media|Average_Logical Disk Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Conteggio|Media|Average_% Available Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Conteggio|Media|Average_% Available Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Conteggio|Media|Average_% Used Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Conteggio|Media|Average_% Used Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Conteggio|Media|Average_Available MBytes Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Conteggio|Media|Average_Available MBytes Swap|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Conteggio|Media|Average_Page Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Conteggio|Media|Average_Page Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Conteggio|Media|Average_Pages/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Conteggio|Media|Average_Used MBytes Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Conteggio|Media|Average_Used Memory MBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Conteggio|Media|Average_Total Bytes Transmitted|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Conteggio|Media|Average_Total Bytes Received|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Conteggio|Media|Average_Total Bytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Conteggio|Media|Average_Total Packets Transmitted|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Conteggio|Media|Average_Total Packets Received|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Conteggio|Media|Average_Total Rx Errors|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Conteggio|Media|Average_Total Tx Errors|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Conteggio|Media|Average_Total Collisions|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Media letture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Media sec/trasferimento disco|Conteggio|Media|Average_Avg. Disk sec/Transfer|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Media scritture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Conteggio|Media|Average_Physical Disk Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Conteggio|Media|Average_Pct Privileged Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Conteggio|Media|Average_Pct User Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Conteggio|Media|Average_Used Memory kBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Conteggio|Media|Average_Virtual Shared Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Conteggio|Media|Average_% DPC Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Conteggio|Media|Average_% Idle Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Conteggio|Media|Average_% Interrupt Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Conteggio|Media|Average_% IO Wait Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Conteggio|Media|Average_% Nice Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Conteggio|Media|Average_% Privileged Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Conteggio|Media|Average_% User Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Conteggio|Media|Average_Free Physical Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Conteggio|Media|Average_Free Space in Paging Files|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Conteggio|Media|Average_Free Virtual Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Processes|Procedure|Conteggio|Media|Average_Processes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Conteggio|Media|Average_Uptime|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Users|Utenti|Conteggio|Media|Average_Users|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Media letture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Media scritture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Lunghezza corrente coda del disco|Conteggio|Media|Average_Current Disk Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture disco/sec|Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Conteggio|Media|Average_% Free Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Processor Queue Length|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Conteggio|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aggiornamento|Aggiornamento|Conteggio|Media|Aggiornamento|Computer, prodotto, classificazione, UpdateState, facoltativo, approvato|
|Evento|Evento|Conteggio|Media|Evento|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, EventId|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|PrefixLatency|Latenza prefisso|Millisecondi|Media|Latenza mediana del prefisso|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilità sessione V4|Percentuale|Media|Disponibilità della sessione V4|ConnectionId|
|SessionAvailabilityV6|Disponibilità della sessione V6|Percentuale|Media|Disponibilità della sessione V6|ConnectionId|
|IngressTrafficRate|Velocità di traffico in ingresso|BitsPerSecond|Media|Velocità di traffico in ingresso in bit al secondo|ConnectionId|
|EgressTrafficRate|Frequenza traffico in uscita|BitsPerSecond|Media|Velocità di traffico in uscita in bit al secondo|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Conteggio|Totale|Connessioni listener riuscite per Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Conteggio|Totale|Errore del client su connessioni listener per Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Conteggio|Totale|Errore del server su connessioni listener per Microsoft.Relay.|EntityName, OperationResult|
|Connessioni mittente - Operazione riuscita|Connessioni mittente - Operazione riuscita|Conteggio|Totale|Connessioni mittente riuscite per Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Conteggio|Totale|Errore del client su connessioni mittente per Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Conteggio|Totale|Errore del server su connessioni mittente per Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Conteggio|Totale|Totale connessioni listener per Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Conteggio|Totale|Totale richieste connessioni mittente per Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Conteggio|Totale|Totale listener attivi per Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Conteggio|Totale|Totale byte trasferiti per Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Conteggio|Totale|Totale disconnessioni listener per Microsoft.Relay.|EntityName|
|Disconnessioni mittente|Disconnessioni mittente|Conteggio|Totale|Totale disconnessioni mittente per Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuno|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuno|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuno|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi|EntityName, OperationResult|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus.|Nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.ServiceBus.|EntityName|
|Dimensioni|Dimensioni|Byte|Media|Dimensioni di una coda o di un argomento in byte.|EntityName|
|Messaggi|Numero di messaggi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi contenuti in una coda o in un argomento.|EntityName|
|ActiveMessages|Numero di messaggi attivi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi attivi contenuti in una coda o in un argomento.|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda o in un argomento.|Conteggio|Media|Numero di messaggi non recapitabili in una coda o in un argomento.|EntityName|
|ScheduledMessages|Numero di messaggi pianificati in una coda o un argomento.|Conteggio|Media|Numero di messaggi pianificati in una coda o un argomento.|EntityName|
|NamespaceCpuUsage|CPU|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio.|Replica|
|NamespaceMemoryUsage|Utilizzo della memoria|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio.|Replica|
|CPUXNS|CPU (deprecata)|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio. Questa metrica è depricated. Usare invece la metrica CPU (NamespaceCpuUsage).|Replica|
|WSXNS|Utilizzo memoria (obsoleto)|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio. Questa metrica è deprecata. Usare invece la metrica utilizzo memoria (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Conteggio|Media|CPU allocata a questo contenitore in Milli Core|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Byte|Media|Memoria allocata a questo contenitore in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ActualCpu|ActualCpu|Conteggio|Media|Utilizzo effettivo della CPU in Milli Core|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ActualMemory|ActualMemory|Byte|Media|Utilizzo effettivo della memoria in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Conteggio|Media|Stato dell'applicazione Service Fabric mesh|ApplicationName, stato|
|ServiceStatus|ServiceStatus|Conteggio|Media|Stato di integrità di un servizio nell'applicazione Service Fabric mesh|ApplicationName, stato, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Conteggio|Media|Stato di integrità di una replica del servizio nell'applicazione Service Fabric mesh|ApplicationName, status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Conteggio|Media|Stato del contenitore nell'applicazione Service Fabric mesh|ApplicationName, ServiceName, codepackagename, ServiceReplicaName, stato|
|RestartCount|RestartCount|Conteggio|Media|Riavviare il conteggio di un contenitore nell'applicazione Service Fabric mesh|ApplicationName, status, ServiceName, ServiceReplicaName, codepackagename|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Conteggio|Massimo|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|Nessuno|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|Nessuno|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|Nessuno|
|UserErrors|Errori utente|Percentuale|Massimo|Percentuale di errori utente|Nessuno|
|SystemErrors|Errori di sistema|Percentuale|Massimo|Percentuale di errori di sistema|Nessuno|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuno|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale di i/o del log. Non applicabile ai data warehouse.|Nessuno|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai database basati su DTU.|Nessuno|
|archiviazione|Spazio dati utilizzato|Byte|Massimo|Spazio dati utilizzato. Non applicabile ai data warehouse.|Nessuno|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuno|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|Nessuno|
|deadlock|Deadlock|Conteggio|Totale|Deadlock. Non applicabile ai data warehouse.|Nessuno|
|storage_percent|Percentuale spazio dati utilizzato|Percentuale|Massimo|Percentuale di utilizzo dello spazio dei dati. Non applicabile a data warehouse o database con iperscalabilità.|Nessuno|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria. Non applicabile ai data warehouse.|Nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro. Non applicabile ai data warehouse.|Nessuno|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni. Non applicabile ai data warehouse.|Nessuno|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU. Si applica ai database basati su DTU.|Nessuno|
|dtu_used|Uso DTU|Conteggio|Media|DTU usato. Si applica ai database basati su DTU.|Nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai database basati su vCore.|Nessuno|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai database basati su vCore.|Nessuno|
|dwu_limit|Limite DWU|Conteggio|Massimo|Limite DWU. Si applica solo ai data warehouse.|Nessuno|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massimo|Percentuale di DWU. Si applica solo ai data warehouse.|Nessuno|
|dwu_used|Uso DWU|Conteggio|Massimo|DWU usato. Si applica solo ai data warehouse.|Nessuno|
|cache_hit_percent|Percentuale dei riscontri nella cache|Percentuale|Massimo|Percentuale riscontri cache. Si applica solo ai data warehouse.|Nessuno|
|cache_used_percent|Percentuale della cache utilizzata|Percentuale|Massimo|Percentuale di utilizzo della cache. Si applica solo ai data warehouse.|Nessuno|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massimo|Utilizzo della CPU come percentuale del processo del database SQL. Non applicabile ai data warehouse.|Nessuno|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Utilizzo della memoria come percentuale del processo del database SQL. Non applicabile ai data warehouse.|Nessuno|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Conteggio|Massimo|Dimensioni del file di dati tempdb. Non applicabile ai data warehouse.|Nessuno|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse.|Nessuno|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massimo|Log percentuale tempdb utilizzato. Non applicabile ai data warehouse.|Nessuno|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|Nessuno|
|app_cpu_billed|CPU app fatturata|Conteggio|Totale|CPU app fatturata. Si applica ai database senza server.|Nessuno|
|app_cpu_percent|Percentuale CPU app|Percentuale|Media|Percentuale CPU app. Si applica ai database senza server.|Nessuno|
|app_memory_percent|Percentuale memoria app|Percentuale|Media|Percentuale di memoria dell'app. Si applica ai database senza server.|Nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Archiviazione dati allocata. Non applicabile ai data warehouse.|Nessuno|
|memory_usage_percent|Percentuale di memoria|Percentuale|Massimo|Percentuale di memoria. Si applica solo ai data warehouse.|Nessuno|
|full_backup_size_bytes|Dimensioni di archiviazione del backup completo|Byte|Massimo|Dimensioni di archiviazione dei backup completi cumulativi. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuno|
|diff_backup_size_bytes|Dimensioni di archiviazione del backup differenziale|Byte|Massimo|Dimensioni di archiviazione del backup differenziale cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuno|
|log_backup_size_bytes|Dimensioni di archiviazione dei backup del log|Byte|Massimo|Dimensioni di archiviazione dei backup del log cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuno|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|database_cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuno|
|database_physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuno|
|database_log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai pool elastici basati su DTU.|Nessuno|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|storage_percent|Percentuale spazio dati utilizzato|Percentuale|Media|Percentuale spazio dati utilizzato|Nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuno|
|database_workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuno|
|database_sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Conteggio|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|Nessuno|
|storage_limit|Dimensioni massime dati|Byte|Media|Dimensioni massime dati|Nessuno|
|eDTU_used|Uso eDTU|Conteggio|Media|eDTU usato. Si applica ai pool elastici basati su DTU.|Nessuno|
|database_eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|DatabaseResourceId|
|storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|Nessuno|
|database_storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|DatabaseResourceId|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai pool elastici basati su vCore.|Nessuno|
|database_cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|DatabaseResourceId|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|Nessuno|
|database_cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata|DatabaseResourceId|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massimo|Utilizzo della CPU come percentuale del processo del database SQL. Si applica ai pool elastici.|Nessuno|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Utilizzo della memoria come percentuale del processo del database SQL. Si applica ai pool elastici.|Nessuno|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Conteggio|Massimo|Kilobyte dimensioni file di dati tempdb|Nessuno|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb|Nessuno|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massimo|Log percentuale tempdb utilizzato|Nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuno|
|database_allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|DatabaseResourceId|
|allocated_data_storage_percent|Percentuale allocata spazio dati|Percentuale|Massimo|Percentuale allocata spazio dati|Nessuno|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|ElasticPoolResourceId|
|database_storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|virtual_core_count|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|Nessuno|
|avg_cpu_percent|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|Nessuno|
|reserved_storage_mb|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|Nessuno|
|storage_space_used_mb|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|Nessuno|
|io_requests|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|Nessuno|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|Nessuno|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|Nessuno|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuno|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|Condivisione file|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|Condivisione file|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuno|
|FileShareSnapshotCount|Conteggio degli snapshot di condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|Condivisione file|
|FileShareSnapshotSize|Dimensioni snapshot della condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|Condivisione file|
|FileShareQuota|Dimensioni della quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|Condivisione file|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Ingresso|Ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/Caches

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ClientIOPS|Totale IOPS client|Conteggio|Media|Frequenza delle operazioni dei file client elaborate dalla cache.|Nessuno|
|ClientLatency|Latenza media del client|Millisecondi|Media|Latenza media delle operazioni dei file client nella cache di archiviazione.|Nessuno|
|ClientReadIOPS|IOPS lettura client|Conteggio al secondo|Media|Operazioni di lettura client al secondo.|Nessuno|
|ClientReadThroughput|Velocità effettiva di lettura cache media|Byte al secondo|Media|Velocità di trasferimento dati in lettura client.|Nessuno|
|ClientWriteIOPS|IOPS scrittura client|Conteggio al secondo|Media|Operazioni di scrittura client al secondo.|Nessuno|
|ClientWriteThroughput|Velocità effettiva media scrittura cache|Byte al secondo|Media|Velocità di trasferimento dati in scrittura client.|Nessuno|
|ClientMetadataReadIOPS|IOPS lettura metadati client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le letture dei dati, che non modificano lo stato persistente.|Nessuno|
|ClientMetadataWriteIOPS|IOPS di scrittura dei metadati client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le scritture dei dati, che modificano lo stato persistente.|Nessuno|
|ClientLockIOPS|IOPS blocco client|Conteggio al secondo|Media|Operazioni di blocco del file client al secondo.|Nessuno|
|StorageTargetHealth|Stato destinazione archiviazione|Conteggio|Media|Risultati booleani del test di connettività tra la cache e le destinazioni di archiviazione.|Nessuno|
|Uptime|Uptime|Conteggio|Media|Risultati booleani del test di connettività tra la cache e il sistema di monitoraggio.|Nessuno|
|StorageTargetIOPS|IOPS StorageTarget totali|Conteggio|Media|Frequenza di tutte le operazioni sui file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|IOPS scrittura StorageTarget|Conteggio|Media|Frequenza delle operazioni di scrittura di file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Velocità effettiva di scrittura asincrona StorageTarget|Byte al secondo|Media|Frequenza con cui la cache scrive in modo asincrono i dati in un particolare StorageTarget. Si tratta di scritture opportunistiche che non provocano il blocco dei client.|StorageTarget|
|StorageTargetSyncWriteThroughput|Velocità effettiva di scrittura sincrona StorageTarget|Byte al secondo|Media|Frequenza con cui la cache scrive in modo sincrono i dati in un particolare StorageTarget. Si tratta di scritture che fanno sì che i client si blocchino.|StorageTarget|
|StorageTargetTotalWriteThroughput|Velocità effettiva di scrittura totale StorageTarget|Byte al secondo|Media|Velocità totale di scrittura dei dati nella cache in un particolare StorageTarget.|StorageTarget|
|StorageTargetLatency|Latenza StorageTarget|Millisecondi|Media|Latenza media round trip di tutte le operazioni sui file inviate dalla cache a una StorageTarget partricular.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS lettura metadati StorageTarget|Conteggio al secondo|Media|Frequenza delle operazioni sui file che non modificano lo stato persistente ed escludendo l'operazione di lettura, che la cache Invia a un determinato StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS scrittura metadati StorageTarget|Conteggio al secondo|Media|Frequenza delle operazioni sui file che modificano lo stato persistente ed escludono l'operazione di scrittura, che la cache Invia a un determinato StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|IOPS lettura StorageTarget|Conteggio al secondo|Media|Frequenza delle operazioni di lettura del file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Velocità effettiva read-ahead StorageTarget|Byte al secondo|Media|Frequenza con cui la cache opportunisticly legge i dati da StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Velocità effettiva riempimento StorageTarget|Byte al secondo|Media|Frequenza con cui la cache legge i dati da StorageTarget per gestire un mancato riscontro nella cache.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget totale velocità effettiva di lettura|Byte al secondo|Media|Velocità totale di lettura dei dati da un particolare StorageTarget da parte della cache.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Risultato della sessione di sincronizzazione|Conteggio|Media|Metrica che registra il valore 1 ogni volta che l'endpoint del server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Dimensioni del richiamo di suddivisione in livelli nel cloud|Byte|Totale|Dimensione dei dati richiamati|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Velocità effettiva di richiamo di suddivisione in livelli nel cloud|Byte al secondo|Media|Dimensioni della velocità effettiva di richiamo dei dati|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Dimensioni del richiamo di suddivisione in livelli nel cloud per applicazione|Byte|Totale|Dimensioni dei dati richiamati dall'applicazione|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. StorageSync/storageSyncServices/syncGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. StorageSync/storageSyncServices/syncGroups/serverEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|ServerEndpointName, SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. StorageSync/storageSyncServices/registeredServers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massimo|% utilizzo unità di streaming|LogicalName, PartitionId|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|LogicalName, PartitionId|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName, PartitionId|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|LogicalName, PartitionId|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|LogicalName, PartitionId|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|LogicalName, PartitionId|
|Errors|Errori di runtime|Conteggio|Totale|Errori di runtime|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|LogicalName, PartitionId|
|AMLCalloutRequests|Richieste di funzioni|Conteggio|Totale|Richieste di funzioni|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste di funzioni non riuscite|LogicalName, PartitionId|
|AMLCalloutInputEvents|Eventi di funzioni|Conteggio|Totale|Eventi di funzioni|LogicalName, PartitionId|
|DeserializationError|Errori di deserializzazione dell'input|Conteggio|Totale|Errori di deserializzazione dell'input|LogicalName, PartitionId|
|EarlyInputEvents|Eventi di input anticipati|Conteggio|Totale|Eventi di input anticipati|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Ritardo limite|Secondi|Massimo|Ritardo limite|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Eventi di input con backlog|Conteggio|Massimo|Eventi di input con backlog|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Origini di input ricevute|Conteggio|Totale|Origini di input ricevute|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuno|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuno|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|Nessuno|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuno|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuno|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuno|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|Nessuno|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuno|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuno|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|Nessuno|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|Nessuno|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|Nessuno|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuno|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuno|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuno|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|Nessuno|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuno|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuno|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuno|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di scrittura nel periodo di campionamento.|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|Nessuno|
|DiskReadOperations|Operazioni di lettura su disco|Conteggio|Totale|Numero di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|DiskWriteOperations|Operazioni di scrittura su disco|Conteggio|Totale|Numero di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|DiskReadLatency|Latenza di lettura da disco|Millisecondi|Media|Latenza di lettura totale. Somma delle latenze di lettura del dispositivo e del kernel.|Nessuno|
|DiskWriteLatency|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. Somma delle latenze di scrittura del dispositivo e del kernel.|Nessuno|
|NetworkInBytesPerSecond|Rete in byte/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|Nessuno|
|NetworkOutBytesPerSecond|Byte di rete in uscita/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|Nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|Nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|Nessuno|
|MemoryUsed|Memoria utilizzata|Byte|Media|Quantità di memoria del computer utilizzata dalla VM.|Nessuno|
|MemoryGranted|Memoria concessa|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host fino a quando non viene toccata una sola volta e la memoria concessa potrebbe essere invertita o rimossa se il VMkernel richiede la memoria.|Nessuno|
|MemoryActive|Memoria attiva|Byte|Media|Quantità di memoria usata dalla macchina virtuale in un intervallo di tempo ridotto. Questo è il numero "vero" della quantità di memoria attualmente necessaria per la macchina virtuale. È possibile che venga scambiata una memoria aggiuntiva inutilizzata o che non abbia alcun effetto sulle prestazioni del Guest.|Nessuno|
|CPU percentuale|CPU percentuale|Percentuale|Media|Utilizzo della CPU. Questo valore viene segnalato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale a 2 vie che usa il 50% di un sistema a quattro core USA completamente due core.|Nessuno|
|PercentageCpuReady|Percentuale di disponibilità CPU|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento precedente.|Nessuno|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|TcpSynSent|TCP SYN inviato|Conteggio|Media|TCP SYN inviato|Istanza|
|TcpSynReceived|TCP SYN ricevuto|Conteggio|Media|TCP SYN ricevuto|Istanza|
|TcpEstablished|TCP stabilito|Conteggio|Media|TCP stabilito|Istanza|
|TcpFinWait1|Attesa pinna TCP 1|Conteggio|Media|Attesa pinna TCP 1|Istanza|
|TcpFinWait2|Attesa pinna TCP 2|Conteggio|Media|Attesa pinna TCP 2|Istanza|
|TcpClosing|Chiusura TCP|Conteggio|Media|Chiusura TCP|Istanza|
|TcpCloseWait|Attesa chiusura TCP|Conteggio|Media|Attesa chiusura TCP|Istanza|
|TcpLastAck|Ultimo ACK TCP|Conteggio|Media|Ultimo ACK TCP|Istanza|
|TcpTimeWait|Tempo di attesa TCP|Conteggio|Media|Tempo di attesa TCP|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Richieste|Richieste|Conteggio|Totale|Richieste|Istanza|
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
|AppConnections|Connessioni|Conteggio|Media|Connessioni|Istanza|
|Selettori|Numero di handle|Conteggio|Media|Numero di handle|Istanza|
|Threads|Thread Count|Conteggio|Media|Thread Count|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|MB/millisecondi|Totale|[Unità di esecuzione funzioni](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|Richieste|Richieste|Conteggio|Totale|Richieste|Istanza|
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
|HttpResponseTime|Tempo di risposta|Secondi|Media|Tempo di risposta|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|Conteggio|Totale|Unità di esecuzione della funzione|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|AppConnections|Connessioni|Conteggio|Media|Connessioni|Istanza|
|Selettori|Numero di handle|Conteggio|Media|Numero di handle|Istanza|
|Threads|Thread Count|Conteggio|Media|Thread Count|Istanza|
|PrivateBytes|Private Bytes|Byte|Media|Private Bytes|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|Richieste|Richieste|Conteggio|Totale|Richieste|Istanza|
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
|TotalFrontEnds|Front end totali|Conteggio|Media|Front end totali|Nessuno|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|Nessuno|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|Nessuno|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|Nessuno|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|Nessuno|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|Nessuno|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|Nessuno|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](resource-logs-overview.md)
