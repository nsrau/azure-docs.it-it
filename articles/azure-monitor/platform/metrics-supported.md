---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659680"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure. Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. L'elenco riportato di seguito include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Le metriche sono organizzate in base allo spazio dei nomi. Per un elenco dei servizi e degli spazi dei nomi che li appartengono, vedere [provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Per eseguire una query e accedere a queste metriche a livello di codice, usare la [versione api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
> Per un elenco delle metriche della piattaforma esportabili tramite le impostazioni di diagnostica, vedere [questo articolo](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|private_bytes_metric|Byte privati|Byte|Media|Byte privati.|ServerResourceType|
|virtual_bytes_metric|Byte virtuali|Byte|Media|Byte virtuali.|ServerResourceType|
|TotalConnectionRequests|Numero totale di richieste di connessione|Conteggio|Media|Numero totale delle richieste di connessione in arrivo.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|ServerResourceType|
|TotalConnectionFailures|Numero totale di errori di connessione|Conteggio|Media|Numero totale dei tentativi di connessione non riusciti.|ServerResourceType|
|CurrentUserSessions|Sessioni utente correnti|Conteggio|Media|Numero corrente di sessioni utente attive.|ServerResourceType|
|QueryPoolBusyThreads|Thread occupati pool di query|Conteggio|Media|Numero dei thread occupati nel pool dei thread di query.|ServerResourceType|
|CommandPoolJobQueueLength|Lunghezza coda processi nel pool di comandi|Conteggio|Media|Numero dei processi nella coda del pool dei thread dei comandi.|ServerResourceType|
|ProcessingPoolJobQueueLength|Lunghezza coda processi nel pool di elaborazione|Conteggio|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|CurrentConnections|Connessione: connessioni correnti|Conteggio|Media|Numero corrente di connessioni client stabilite.|ServerResourceType|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Totale richieste Gateway (deprecato)|Conteggio|Totale|Numero di richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|SuccessfulRequests|Richieste gateway riuscite (deprecate)|Conteggio|Totale|Numero di richieste gateway riuscite: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|UnauthorizedRequests|Richieste del gateway non autorizzate (deprecate)|Conteggio|Totale|Numero di richieste del gateway non autorizzate: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|FailedRequests|Richieste gateway non riuscite (deprecate)|Conteggio|Totale|Numero di errori nelle richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|OtherRequests|Altre richieste del gateway (deprecate)|Conteggio|Totale|Numero di altre richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|Durata|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Località, nome host|
|BackendDuration|Durata delle richieste back-end|Millisecondi|Media|Durata delle richieste back-end in millisecondi|Località, nome host|
|Capacity|Capacity|Percentuale|Media|Metrica di utilizzo per il servizio ApiManagement|Location|
|EventHubTotalEvents|Totale eventi EventHub|Conteggio|Totale|Numero di eventi inviati a EventHub|Location|
|EventHubSuccessfulEvents|Eventi EventHub riusciti|Conteggio|Totale|Numero di eventi EventHub riusciti|Location|
|EventHubTotalFailedEvents|Eventi EventHub non riusciti|Conteggio|Totale|Numero di eventi EventHub non riusciti|Location|
|EventHubRejectedEvents|Eventi EventHub rifiutati|Conteggio|Totale|Numero di eventi EventHub rifiutati (configurazione errata o non autorizzata)|Location|
|EventHubThrottledEvents|Eventi EventHub limitati|Conteggio|Totale|Numero di eventi EventHub limitati|Location|
|EventHubTimedoutEvents|Timeout degli eventi di EventHub|Conteggio|Totale|Numero di eventi EventHub di timeout|Location|
|EventHubDroppedEvents|Eventi EventHub eliminati|Conteggio|Totale|Numero di eventi ignorati a causa del raggiungimento del limite delle dimensioni della coda|Location|
|EventHubTotalBytesSent|Dimensioni degli eventi di EventHub|Byte|Totale|Dimensioni totali degli eventi EventHub in byte|Location|
|Richieste|Richieste|Conteggio|Totale|Metriche delle richieste del gateway con più dimensioni|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Conteggio|Conteggio|Numero totale di richieste HTTP in ingresso.|None|
|FailedHttpRequestCount|FailedHttpRequestCount|Conteggio|Conteggio|Richieste HTTP non riuscite.|None|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Conteggio|Media|Latenza su una richiesta HTTP.|None|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentRuns|Totale esecuzioni di aggiornamenti della distribuzione|Conteggio|Totale|Totale esecuzioni di distribuzioni di aggiornamenti software|SoftwareUpdateConfigurationName, stato|
|TotalUpdateDeploymentMachineRuns|Totale esecuzioni computer di distribuzione aggiornamenti|Conteggio|Totale|Il computer di distribuzione degli aggiornamenti software totale viene eseguito in un'esecuzione di distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Numero di core dedicati|Conteggio|Totale|Numero totale di core dedicati nell'account Batch|None|
|TotalNodeCount|Numero di nodi dedicati|Conteggio|Totale|Numero totale di nodi dedicati nell'account Batch|None|
|LowPriorityCoreCount|Numero di core a bassa priorità|Conteggio|Totale|Numero totale di core a bassa priorità nell'account Batch|None|
|TotalLowPriorityNodeCount|Numero di nodi a bassa priorità|Conteggio|Totale|Numero totale di nodi a bassa priorità nell'account Batch|None|
|CreatingNodeCount|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|None|
|StartingNodeCount|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|None|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|None|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|None|
|IdleNodeCount|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|None|
|OfflineNodeCount|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|None|
|RebootingNodeCount|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|None|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|None|
|RunningNodeCount|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|None|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|None|
|UnusableNodeCount|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|None|
|PreemptedNodeCount|Numero di nodi annullati|Conteggio|Totale|Numero di nodi annullati|None|
|TaskStartEvent|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|None|
|TaskCompleteEvent|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|None|
|TaskFailEvent|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|None|
|PoolCreateEvent|Eventi pool creati|Conteggio|Totale|Il numero totale di pool che sono stati creati|None|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool avviati|None|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool completati|None|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Conteggio|Totale|Il numero totale di eliminazioni di pool avviate|None|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Conteggio|Totale|Il numero totale di eliminazioni di pool completate|None|
|JobDeleteCompleteEvent|Eventi eliminazione processi completati|Conteggio|Totale|Numero totale dei processi eliminati.|None|
|JobDeleteStartEvent|Eventi eliminazione processi avviati|Conteggio|Totale|Numero totale di processi da eliminare.|None|
|JobDisableCompleteEvent|Eventi disabilitazione processi completati|Conteggio|Totale|Numero totale dei processi disabilitati.|None|
|JobDisableStartEvent|Eventi disabilitazione processi avviati|Conteggio|Totale|Numero totale di processi da disabilitare.|None|
|JobStartEvent|Eventi di avvio processi|Conteggio|Totale|Numero totale di processi avviati.|None|
|JobTerminateCompleteEvent|Eventi terminazione processi completati|Conteggio|Totale|Numero totale di processi terminati.|None|
|JobTerminateStartEvent|Eventi terminazione processi avviati|Conteggio|Totale|Numero totale di processi da terminare.|None|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/Workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentuale di utilizzo CPU|Percentuale|Massimo|Percentuale di utilizzo CPU|Nodo|
|MemoryUsage|Utilizzo memoria|Byte|Media|Utilizzo memoria|Nodo|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Conteggio|Massimo||ShardId|
|totalcommandsprocessed|Totale operazioni|Conteggio|Totale||ShardId|
|cachehits|Riscontri cache|Conteggio|Totale||ShardId|
|cachemisses|Mancati riscontri nella cache|Conteggio|Totale||ShardId|
|cachemissrate|Frequenza di mancata memorizzazione nella cache|Percentuale|cachemissrate||ShardId|
|getcommands|Ottiene|Conteggio|Totale||ShardId|
|setcommands|Set|Conteggio|Totale||ShardId|
|operationsPerSecond|Operazioni al secondo|Conteggio|Massimo||ShardId|
|evictedkeys|Chiavi rimosse|Conteggio|Totale||ShardId|
|totalkeys|Totale chiavi|Conteggio|Massimo||ShardId|
|expiredkeys|Chiavi scadute|Conteggio|Totale||ShardId|
|usedmemory|Used Memory|Byte|Massimo||ShardId|
|usedmemorypercentage|Percentuale memoria usata|Percentuale|Massimo||ShardId|
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massimo||ShardId|
|serverLoad|Carico server|Percentuale|Massimo||ShardId|
|cacheWrite|Scrittura nella cache|Byte al secondo|Massimo||ShardId|
|cacheRead|Lettura da cache|Byte al secondo|Massimo||ShardId|
|percentProcessorTime|CPU|Percentuale|Massimo||ShardId|
|cacheLatency|Microsecondi di latenza della cache (anteprima)|Conteggio|Media||ShardId|
|errori|Errori|Conteggio|Massimo||ShardId, ErrorType|
|connectedclients0|Client connessi (partizione 0)|Conteggio|Massimo||None|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Conteggio|Totale||None|
|cachehits0|Riscontri cache (partizione 0)|Conteggio|Totale||None|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||None|
|getcommands0|Operazioni Get (partizione 0)|Conteggio|Totale||None|
|setcommands0|Operazioni Set (partizione 0)|Conteggio|Totale||None|
|operationsPerSecond0|Operazioni al secondo (partizione 0)|Conteggio|Massimo||None|
|evictedkeys0|Chiavi rimosse (partizione 0)|Conteggio|Totale||None|
|totalkeys0|Totale chiavi (partizione 0)|Conteggio|Massimo||None|
|expiredkeys0|Chiavi scadute (partizione 0)|Conteggio|Totale||None|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massimo||None|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massimo||None|
|serverLoad0|Carico server (partizione 0)|Percentuale|Massimo||None|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massimo||None|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massimo||None|
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massimo||None|
|connectedclients1|Client connessi (partizione 1)|Conteggio|Massimo||None|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Conteggio|Totale||None|
|cachehits1|Riscontri cache (partizione 1)|Conteggio|Totale||None|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||None|
|getcommands1|Operazioni Get (partizione 1)|Conteggio|Totale||None|
|setcommands1|Operazioni Set (partizione 1)|Conteggio|Totale||None|
|operationsPerSecond1|Operazioni al secondo (partizione 1)|Conteggio|Massimo||None|
|evictedkeys1|Chiavi rimosse (partizione 1)|Conteggio|Totale||None|
|totalkeys1|Totale chiavi (partizione 1)|Conteggio|Massimo||None|
|expiredkeys1|Chiavi scadute (partizione 1)|Conteggio|Totale||None|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massimo||None|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massimo||None|
|serverLoad1|Carico server (partizione 1)|Percentuale|Massimo||None|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massimo||None|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massimo||None|
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massimo||None|
|connectedclients2|Client connessi (partizione 2)|Conteggio|Massimo||None|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Conteggio|Totale||None|
|cachehits2|Riscontri cache (partizione 2)|Conteggio|Totale||None|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||None|
|getcommands2|Operazioni Get (partizione 2)|Conteggio|Totale||None|
|setcommands2|Operazioni Set (partizione 2)|Conteggio|Totale||None|
|operationsPerSecond2|Operazioni al secondo (partizione 2)|Conteggio|Massimo||None|
|evictedkeys2|Chiavi rimosse (partizione 2)|Conteggio|Totale||None|
|totalkeys2|Totale chiavi (partizione 2)|Conteggio|Massimo||None|
|expiredkeys2|Chiavi scadute (partizione 2)|Conteggio|Totale||None|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massimo||None|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massimo||None|
|serverLoad2|Carico server (partizione 2)|Percentuale|Massimo||None|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massimo||None|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massimo||None|
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massimo||None|
|connectedclients3|Client connessi (partizione 3)|Conteggio|Massimo||None|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Conteggio|Totale||None|
|cachehits3|Riscontri cache (partizione 3)|Conteggio|Totale||None|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||None|
|getcommands3|Operazioni Get (partizione 3)|Conteggio|Totale||None|
|setcommands3|Operazioni Set (partizione 3)|Conteggio|Totale||None|
|operationsPerSecond3|Operazioni al secondo (partizione 3)|Conteggio|Massimo||None|
|evictedkeys3|Chiavi rimosse (partizione 3)|Conteggio|Totale||None|
|totalkeys3|Totale chiavi (partizione 3)|Conteggio|Massimo||None|
|expiredkeys3|Chiavi scadute (partizione 3)|Conteggio|Totale||None|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massimo||None|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massimo||None|
|serverLoad3|Carico server (partizione 3)|Percentuale|Massimo||None|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massimo||None|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massimo||None|
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massimo||None|
|connectedclients4|Client connessi (partizione 4)|Conteggio|Massimo||None|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Conteggio|Totale||None|
|cachehits4|Riscontri cache (partizione 4)|Conteggio|Totale||None|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||None|
|getcommands4|Operazioni Get (partizione 4)|Conteggio|Totale||None|
|setcommands4|Operazioni Set (partizione 4)|Conteggio|Totale||None|
|operationsPerSecond4|Operazioni al secondo (partizione 4)|Conteggio|Massimo||None|
|evictedkeys4|Chiavi rimosse (partizione 4)|Conteggio|Totale||None|
|totalkeys4|Totale chiavi (partizione 4)|Conteggio|Massimo||None|
|expiredkeys4|Chiavi scadute (partizione 4)|Conteggio|Totale||None|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massimo||None|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massimo||None|
|serverLoad4|Carico server (partizione 4)|Percentuale|Massimo||None|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massimo||None|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massimo||None|
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massimo||None|
|connectedclients5|Client connessi (partizione 5)|Conteggio|Massimo||None|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Conteggio|Totale||None|
|cachehits5|Riscontri cache (partizione 5)|Conteggio|Totale||None|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||None|
|getcommands5|Operazioni Get (partizione 5)|Conteggio|Totale||None|
|setcommands5|Operazioni Set (partizione 5)|Conteggio|Totale||None|
|operationsPerSecond5|Operazioni al secondo (partizione 5)|Conteggio|Massimo||None|
|evictedkeys5|Chiavi rimosse (partizione 5)|Conteggio|Totale||None|
|totalkeys5|Totale chiavi (partizione 5)|Conteggio|Massimo||None|
|expiredkeys5|Chiavi scadute (partizione 5)|Conteggio|Totale||None|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massimo||None|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massimo||None|
|serverLoad5|Carico server (partizione 5)|Percentuale|Massimo||None|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massimo||None|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massimo||None|
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massimo||None|
|connectedclients6|Client connessi (partizione 6)|Conteggio|Massimo||None|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Conteggio|Totale||None|
|cachehits6|Riscontri cache (partizione 6)|Conteggio|Totale||None|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||None|
|getcommands6|Operazioni Get (partizione 6)|Conteggio|Totale||None|
|setcommands6|Operazioni Set (partizione 6)|Conteggio|Totale||None|
|operationsPerSecond6|Operazioni al secondo (partizione 6)|Conteggio|Massimo||None|
|evictedkeys6|Chiavi rimosse (partizione 6)|Conteggio|Totale||None|
|totalkeys6|Totale chiavi (partizione 6)|Conteggio|Massimo||None|
|expiredkeys6|Chiavi scadute (partizione 6)|Conteggio|Totale||None|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massimo||None|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massimo||None|
|serverLoad6|Carico server (partizione 6)|Percentuale|Massimo||None|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massimo||None|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massimo||None|
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massimo||None|
|connectedclients7|Client connessi (partizione 7)|Conteggio|Massimo||None|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Conteggio|Totale||None|
|cachehits7|Riscontri cache (partizione 7)|Conteggio|Totale||None|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||None|
|getcommands7|Operazioni Get (partizione 7)|Conteggio|Totale||None|
|setcommands7|Operazioni Set (partizione 7)|Conteggio|Totale||None|
|operationsPerSecond7|Operazioni al secondo (partizione 7)|Conteggio|Massimo||None|
|evictedkeys7|Chiavi rimosse (partizione 7)|Conteggio|Totale||None|
|totalkeys7|Totale chiavi (partizione 7)|Conteggio|Massimo||None|
|expiredkeys7|Chiavi scadute (partizione 7)|Conteggio|Totale||None|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massimo||None|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massimo||None|
|serverLoad7|Carico server (partizione 7)|Percentuale|Massimo||None|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massimo||None|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massimo||None|
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massimo||None|
|connectedclients8|Client connessi (partizione 8)|Conteggio|Massimo||None|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Conteggio|Totale||None|
|cachehits8|Riscontri cache (partizione 8)|Conteggio|Totale||None|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||None|
|getcommands8|Operazioni Get (partizione 8)|Conteggio|Totale||None|
|setcommands8|Operazioni Set (partizione 8)|Conteggio|Totale||None|
|operationsPerSecond8|Operazioni al secondo (partizione 8)|Conteggio|Massimo||None|
|evictedkeys8|Chiavi rimosse (partizione 8)|Conteggio|Totale||None|
|totalkeys8|Totale chiavi (partizione 8)|Conteggio|Massimo||None|
|expiredkeys8|Chiavi scadute (partizione 8)|Conteggio|Totale||None|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massimo||None|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massimo||None|
|serverLoad8|Carico server (partizione 8)|Percentuale|Massimo||None|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massimo||None|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massimo||None|
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massimo||None|
|connectedclients9|Client connessi (partizione 9)|Conteggio|Massimo||None|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Conteggio|Totale||None|
|cachehits9|Riscontri cache (partizione 9)|Conteggio|Totale||None|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||None|
|getcommands9|Operazioni Get (partizione 9)|Conteggio|Totale||None|
|setcommands9|Operazioni Set (partizione 9)|Conteggio|Totale||None|
|operationsPerSecond9|Operazioni al secondo (partizione 9)|Conteggio|Massimo||None|
|evictedkeys9|Chiavi rimosse (partizione 9)|Conteggio|Totale||None|
|totalkeys9|Totale chiavi (partizione 9)|Conteggio|Massimo||None|
|expiredkeys9|Chiavi scadute (partizione 9)|Conteggio|Totale||None|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massimo||None|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massimo||None|
|serverLoad9|Carico server (partizione 9)|Percentuale|Massimo||None|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massimo||None|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massimo||None|
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massimo||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, ruleName, azione|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|None|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|None|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|None|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|None|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|None|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|None|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|None|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|None|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|None|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/FileService

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|None|
|FileShareSnapshotCount|Conteggio degli snapshot di condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot della condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareQuota|Dimensioni della quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|None|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|None|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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
|TotalTransactions|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|None|
|TotalTokenCalls|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|None|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|None|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|None|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|None|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|None|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|None|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|None|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|None|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|None|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|None|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità della coda del disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Profondità coda del disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|None|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|None|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|None|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|None|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|None|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|None|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|None|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|None|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|VMName|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|VMName|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|None|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|None|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|None|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Profondità della coda del disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Profondità coda del disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|None|
|Rete in ingresso|Rete in fatturabile (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|None|
|Rete in uscita|Rete in uscita fatturabile (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|None|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|None|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|None|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|None|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|None|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|None|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|None|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec (deprecate)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec (deprecate)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|None|
|Byte letti dal disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità della coda del disco dati|Profondità coda del disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti dal disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Byte scritti disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di lettura disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Operazioni di scrittura disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|None|
|Profondità coda del disco del sistema operativo|Profondità coda del disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|None|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|None|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|None|
|Velocità di creazione massima dei flussi in ingresso|Velocità di creazione massima dei flussi in ingresso (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|None|
|Velocità di creazione massima dei flussi in uscita|Velocità di creazione massima dei flussi in uscita (anteprima)|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|None|
|Hit lettura cache disco dati Premium|Hit Read cache del disco dati Premium (anteprima)|Percentuale|Media|Hit lettura cache disco dati Premium|LUN|
|Lettura della cache del disco dati Premium|Lettura della cache del disco dati Premium (anteprima)|Percentuale|Media|Lettura della cache del disco dati Premium|LUN|
|Hit lettura cache del disco del sistema operativo Premium|Hit lettura cache del disco del sistema operativo Premium (anteprima)|Percentuale|Media|Hit lettura cache del disco del sistema operativo Premium|None|
|Cache del disco del sistema operativo Premium Read Miss|Cache del disco del sistema operativo Premium lettura mancata (anteprima)|Percentuale|Media|Cache del disco del sistema operativo Premium Read Miss|None|
|Rete totale|Rete totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|None|
|Totale rete in uscita|Totale rete in uscita|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo CPU|Conteggio|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|None|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TotalPullCount|Conteggio pull totale|Conteggio|Media|Numero totale di pull di immagini|None|
|SuccessfulPullCount|Conteggio pull riuscito|Conteggio|Media|Numero di pull di immagini riuscite|None|
|TotalPushCount|Numero totale di push|Conteggio|Media|Numero totale di push di immagini|None|
|SuccessfulPushCount|Conteggio push riuscito|Conteggio|Media|Numero di push di immagini riusciti|None|
|RunDuration|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Media|Numero totale di core CPU disponibili in un cluster gestito|None|
|kube_node_status_allocatable_memory_bytes|Numero totale di memoria disponibile in un cluster gestito|Byte|Media|Numero totale di memoria disponibile in un cluster gestito|None|
|kube_pod_status_ready|Numero di pod in stato Pronto|Conteggio|Media|Numero di pod in stato Pronto|spazio dei nomi, Pod|
|kube_node_status_condition|Stati per diverse condizioni dei nodi|Conteggio|Media|Stati per diverse condizioni dei nodi|Condition, status, status2, node|
|kube_pod_status_phase|Numero di pod per fase|Conteggio|Media|Numero di pod per fase|fase, spazio dei nomi, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SuccessfullRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite effettuate dal provider personalizzato|HttpMethod, CallPath, StatusCode|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Ottiene i log disponibili per i provider di risorse personalizzati|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva di lettura (rete)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettiva di download del cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|CloudUploadThroughputPerShare|Velocità effettiva di caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|BytesUploadedToCloudPerShare|Byte cloud caricati (condivisione)|Byte|Media|Il numero totale di byte caricati in Azure da una condivisione durante il periodo di Reporting.|Condividi|
|Archiviazione|Capacità totale:|Byte|Media|Capacità totale:|None|
|AvailableCapacity|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di Reporting.|None|
|CloudUploadThroughput|Velocità effettiva di caricamento cloud|Byte al secondo|Media|La velocità effettiva di caricamento nel cloud in Azure durante il periodo di Reporting.|None|
|CloudReadThroughput|Velocità effettiva di download del cloud|Byte al secondo|Media|La velocità effettiva di download del cloud in Azure durante il periodo di Reporting.|None|
|BytesUploadedToCloud|Byte cloud caricati (dispositivo)|Byte|Media|Il numero totale di byte caricati in Azure da un dispositivo durante il periodo di Reporting.|None|
|HyperVVirtualProcessorUtilization|Calcolo Edge-percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|HyperVMemoryUtilization|Calcolo Edge-utilizzo memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale||PipelineName, ActivityName|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale||PipelineName, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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
|MaxAllowedResourceCount|Conteggio entità massime consentite|Conteggio|Massimo||None|
|MaxAllowedFactorySizeInGbUnits|Dimensioni massime consentite per le factory (unità GB)|Conteggio|Massimo||None|
|ResourceCount|Conteggio entità totali|Conteggio|Massimo||None|
|FactorySizeInGbUnits|Dimensioni totali Factory (unità GB)|Conteggio|Massimo||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|None|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|None|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati|None|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|None|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|None|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massimo|Quantità totale di dati archiviati nell'account|None|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|None|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|None|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|None|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|None|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|None|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|None|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|None|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|None|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|None|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|None|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|None|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|None|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|None|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|None|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|None|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|None|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|None|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|None|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|None|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|None|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|None|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|None|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|None|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|None|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|None|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|None|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|None|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|None|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|None|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|None|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|None|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|None|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|None|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|None|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|None|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|None|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|None|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|None|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|None|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|None|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|None|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|None|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massimo|Ritardo replica in secondi|None|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massimo|Ritardo in byte della replica più in ritardo|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|None|
|IOPS|IOPS|Conteggio|Media|Operazioni di i/o al secondo|None|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|None|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|None|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|None|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|None|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|None|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|None|
|c2d.commands.egress.complete.success|Recapito messaggi C2D completati|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|None|
|c2d.commands.egress.abandon.success|Messaggi C2D abbandonati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|None|
|c2d.commands.egress.reject.success|Messaggi C2D rifiutati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|None|
|C2DMessagesExpired|Messaggi C2D scaduti (anteprima)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|None|
|devices.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|None|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|None|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|None|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|None|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |None|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|None|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|None|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|None|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|None|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|None|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|None|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|None|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|None|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|None|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|None|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|None|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|None|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|None|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|None|
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento (https://aka.ms/ioteventgrid).|ResourceId, result, EventType|
|EventGridLatency|Latenza griglia di eventi (anteprima)|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|ResourceId, EventType|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|None|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|None|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|None|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|None|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|None|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|None|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|None|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|None|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|None|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|None|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|None|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|None|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|None|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|None|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|None|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|None|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|None|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|None|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|None|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|None|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|None|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|None|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|None|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|None|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|None|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|None|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|None|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|None|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|None|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|None|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|None|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|None|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna|None|
|deviceDataUsage|Utilizzo totale dei dati del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|None|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|None|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|None|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|None|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, stato|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, stato, protocollo|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|AddRegion|Area aggiunta|Conteggio|Conteggio|Area aggiunta|Region|
|AvailableStorage|Archiviazione disponibile|Byte|Totale|Totale spazio di archiviazione disponibile segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni Cassandra chiuse, segnalate a una granularità di 1 minuto|APIType, Region, ClosureReason|
|CassandraRequestCharges|Addebiti richieste Cassandra|Conteggio|Totale|Ur utilizzate per le richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CreateAccount|Account creato|Conteggio|Conteggio|Account creato|None|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DeleteAccount|Account eliminato|Conteggio|Conteggio|Account eliminato|None|
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
|ProvisionedThroughput|Velocità effettiva sottoposta a provisioning|Conteggio|Massimo|Velocità effettiva sottoposta a provisioning|DatabaseName, CollectionName|
|RegionFailover|Area sottoposta a failover|Conteggio|Conteggio|Area sottoposta a failover|None|
|RemoveRegion|Area rimossa|Conteggio|Conteggio|Area rimossa|Region|
|ReplicationLatency|Latenza di replica P99|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion,TargetRegion|
|ServiceAvailability|Disponibilità dei servizi|Percentuale|Media|L'account richiede disponibilità a una granularità di un'ora, giorno o mese|None|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|UpdateAccountKeys|Chiavi dell'account aggiornate|Conteggio|Conteggio|Chiavi dell'account aggiornate|KeyType|
|UpdateAccountNetworkSettings|Impostazioni di rete account aggiornate|Conteggio|Conteggio|Impostazioni di rete account aggiornate|None|
|UpdateAccountReplicationSettings|Impostazioni di replica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di replica dell'account aggiornate|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/Services

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TransactionCount|Conteggio transazioni|Conteggio|Conteggio|Conteggio totale transazioni|TransactionCount|
|SuccessCount|Conteggio completo|Conteggio|Conteggio|Conteggio transazioni riuscite|SuccessCount|
|FailureCount|Conteggio errori|Conteggio|Conteggio|Conteggio transazioni non riuscite|FailureCount|
|SuccessLatency|Latenza riuscita|Millisecondi|Media|Latenza delle transazioni riuscite|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|None|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|None|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|None|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|None|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|None|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|None|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|None|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|None|
|PublishFailCount|Pubblica eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|None|
|PublishSuccessLatencyInMs|Latenza pubblicazione riuscita|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Errori del server|Conteggio|Totale|Errori del server per Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|None|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (deprecate)|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (deprecato)|None|
|SUCCREQ|Richieste riuscite (deprecate)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (deprecato)|None|
|FAILREQ|Richieste non riuscite (deprecate)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|None|
|SVRBSY|Errori di server occupato (deprecato)|Conteggio|Totale|Totale errori server occupati per uno spazio dei nomi (deprecato)|None|
|INTERR|Errori interni del server (deprecati)|Conteggio|Totale|Totale errori interni del server per uno spazio dei nomi (deprecato)|None|
|MISCERR|Altri errori (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|None|
|INMSGS|Messaggi in ingresso (obsoleti) (deprecato)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in ingresso (deprecata)|None|
|EHINMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi (deprecato)|None|
|OUTMSGS|Messaggi in uscita (obsoleti) (deprecato)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in uscita (deprecata)|None|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecato)|None|
|EHINMBS|Byte in ingresso (obsoleti) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in ingresso (deprecata)|None|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva del messaggio in ingresso dell'hub eventi per uno spazio dei nomi (deprecato)|None|
|EHOUTMBS|Byte in uscita (obsoleti) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in uscita (deprecata)|None|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva del messaggio in uscita dell'hub eventi per uno spazio dei nomi (deprecato)|None|
|EHABL|Archivia messaggi backlog (deprecato)|Conteggio|Totale|Messaggi di archiviazione dell'hub eventi nel backlog per uno spazio dei nomi (deprecato)|None|
|EHAMSGS|Messaggi di archivio (deprecati)|Conteggio|Totale|Messaggi archiviati dell'hub eventi in uno spazio dei nomi (deprecato)|None|
|EHAMBS|Archiviazione della velocità effettiva del messaggio (deprecata)|Byte|Totale|Velocità effettiva del messaggio archiviato nell'hub eventi in uno spazio dei nomi (deprecato)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|OperationResult|
|ServerErrors|Errori del server|Conteggio|Totale|Errori del server per Microsoft.EventHub.|OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|None|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|None|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|None|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|None|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|None|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|None|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|None|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|None|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|None|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|None|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|None|
|CPU|CPU|Percentuale|Massimo|Uso CPU per il cluster dell'hub eventi in percentuale|Role|
|AvailableMemory|Memoria disponibile|Percentuale|Massimo|Memoria disponibile per il cluster di hub eventi come percentuale della memoria totale.|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Numero di thread di lavoro attivi|Conteggio|Massimo|Numero di thread di lavoro attivi|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|None|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult/Name, availabilityResult/location|
|availabilityResults/count|Test della disponibilità|Conteggio|Conteggio|Conteggio dei test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|availabilityResults/duration|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|None|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|None|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|None|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|None|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|None|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totale dei risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturazione complessiva dell'insieme di credenziali|Percentuale|Media|Capacità dell'insieme di credenziali utilizzata|ActivityType, ActivityName, TransactionType|
|Disponibilità|Disponibilità complessiva dell'insieme di credenziali|Percentuale|Media|Disponibilità richieste di archiviazione|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CacheUtilization|Utilizzo della cache|Percentuale|Media|Livello di uso nell'ambito del cluster|None|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query in secondi|QueryStatus|
|IngestionUtilization|Utilizzo dell'inserimento|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|None|
|KeepAlive|Keep-Alive|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|None|
|IngestionVolumeInMB|Volume di inserimento (in MB)|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Database|
|IngestionLatencyInSeconds|Latenza inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|None|
|EventsProcessedForEventHubs|Eventi elaborati (per gli hub eventi/tutto)|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi/tutto|EventStatus|
|IngestionResult|Risultato inserimento|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|CPU|CPU|Percentuale|Media|Livello di uso della CPU|None|
|ContinuousExportNumOfRecordsExported|Esportazione continua: numero di record esportati|Conteggio|Totale|Numero di record esportati, generati per ogni artefatto di archiviazione scritto durante l'operazione di esportazione|None|
|ExportUtilization|Esporta utilizzo|Percentuale|Massimo|Esporta utilizzo|None|
|ContinuousExportPendingCount|Conteggio in sospeso esportazione continua|Conteggio|Massimo|Numero di processi di esportazione continua in sospeso pronti per l'esecuzione|None|
|ContinuousExportMaxLatenessMinutes|Minuti di ritardo massimo esportazione continua|Conteggio|Massimo|Ritardo massimo in minuti di tutte le esportazioni continue in sospeso e pronte per l'esecuzione|None|
|ContinuousExportResult|Risultato dell'esportazione continua|Conteggio|Conteggio|Indica se l'esportazione continua ha avuto esito positivo o negativo|ContinuousExportName, result, database|
|StreamingIngestDuration|Durata inserimento flusso|Millisecondi|Media|Durata di inserimento del flusso in millisecondi|None|
|StreamingIngestDataRate|Velocità dati di inserimento dei flussi|Conteggio|Media|Velocità dati di inserimento dei flussi (MB al secondo)|None|
|SteamingIngestRequestRate|Frequenza delle richieste di inserimento di flussi|Conteggio|RateRequestsPerSecond|Frequenza delle richieste di inserimento in streaming (richieste al secondo)|None|
|StreamingIngestResults|Risultato inserimento flusso|Conteggio|Media|Risultato inserimento flusso|Risultato|
|TotalNumberOfConcurrentQueries|Numero totale di query simultanee|Conteggio|Totale|Numero totale di query simultanee|None|
|TotalNumberOfThrottledQueries|Numero totale di query limitate|Conteggio|Totale|Numero totale di query limitate|None|
|TotalNumberOfThrottledCommands|Numero totale di comandi limitati|Conteggio|Totale|Numero totale di comandi limitati|CommandType|
|TotalNumberOfExtents|Numero totale di extent|Conteggio|Totale|Numero totale di extent dei dati|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|None|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|None|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|None|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|None|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|None|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|None|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|None|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|None|
|RunStartThrottledEvents|Eseguire gli eventi di avvio limitato|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|None|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|None|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|None|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|None|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|None|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|None|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|None|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|None|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|None|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|None|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|None|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|None|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|None|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|None|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|None|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|None|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|None|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|None|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|None|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|None|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|None|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|None|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|None|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|None|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|None|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|None|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|None|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|None|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|None|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|None|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|None|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|None|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|None|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|None|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|None|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|None|
|RunStartThrottledEvents|Eseguire gli eventi di avvio limitato|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|None|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|None|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|None|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|None|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|None|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|None|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|None|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|None|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|None|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|None|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|None|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|None|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|None|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|None|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|None|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|None|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|None|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|None|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|None|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente di Integration Services.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente di Integration Services.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo memoria connettore per ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|Esecuzioni completate|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni completate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni avviate|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni avviate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Registrazione del modello completata|Registrazione del modello completata|Conteggio|Totale|Numero di registrazioni del modello riuscite in questa area di lavoro|Scenario|
|Registrazione modello non riuscita|Registrazione modello non riuscita|Conteggio|Totale|Numero di registrazioni del modello non riuscite in questa area di lavoro|Scenario, StatusCode|
|Distribuzione modello avviata|Distribuzione modello avviata|Conteggio|Totale|Numero di distribuzioni di modelli avviate in questa area di lavoro|Scenario|
|Distribuzione modello completata|Distribuzione modello completata|Conteggio|Totale|Numero di distribuzioni di modelli riuscite in questa area di lavoro|Scenario|
|Distribuzione modello non riuscito|Distribuzione modello non riuscito|Conteggio|Totale|Numero di distribuzioni di modelli non riuscite in questa area di lavoro|Scenario, StatusCode|
|Totale nodi|Totale nodi|Conteggio|Media|Numero totale di nodi. Il totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi con precedenza, lasciando nodi|Scenario, clustername|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|Utilizzo|Utilizzo|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilità|Disponibilità|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/le entità streamingendpoint

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte.|OutputFormat|
|SuccessE2ELatency|Latenza end-to-end riuscita|Millisecondi|Media|Latenza media per le richieste con esito positivo in millisecondi.|OutputFormat|
|Richieste|Richieste|Conteggio|Totale|Richieste a un endpoint di streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|AssetQuota|Quota asset|Conteggio|Media|Numero di asset consentiti per l'account del servizio multimediale corrente|None|
|AssetCount|Conteggio asset|Conteggio|Media|Numero di asset già creati nell'account del servizio multimediale corrente|None|
|AssetQuotaUsedPercentage|Percentuale di utilizzo della quota di asset|Percentuale|Media|Percentuale di risorse usate nell'account del servizio multimediale corrente|None|
|ContentKeyPolicyQuota|Quota dei criteri della chiave simmetrica|Conteggio|Media|Numero di criteri di chiave simmetrica consentiti per l'account del servizio multimediale corrente|None|
|ContentKeyPolicyCount|Conteggio dei criteri della chiave simmetrica|Conteggio|Media|Quanti criteri chiave simmetrica sono già stati creati nell'account del servizio multimediale corrente|None|
|ContentKeyPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri della chiave simmetrica|Percentuale|Media|Percentuale di utilizzo del criterio della chiave simmetrica nell'account del servizio multimediale corrente|None|
|StreamingPolicyQuota|Quota dei criteri di streaming|Conteggio|Media|Quanti criteri di streaming sono consentiti per l'account del servizio multimediale corrente|None|
|StreamingPolicyCount|Conteggio dei criteri di flusso|Conteggio|Media|Quanti criteri di streaming sono già stati creati nell'account del servizio multimediale corrente|None|
|StreamingPolicyQuotaUsedPercentage|Percentuale di utilizzo della quota dei criteri di flusso|Percentuale|Media|Percentuale di utilizzo dei criteri di flusso nell'account del servizio multimediale corrente|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/volumes

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|AverageReadLatency|Latenza media in lettura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di lettura|None|
|AverageWriteLatency|Latenza media in scrittura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di scrittura|None|
|VolumeLogicalSize|Dimensioni logiche del volume|Byte|Media|Dimensioni logiche del volume (byte usati)|None|
|VolumeSnapshotSize|Dimensioni dello snapshot del volume|Byte|Media|Dimensioni di tutti gli snapshot nel volume|None|
|ReadIops|Operazioni di I/O in lettura|Conteggio al secondo|Media|Operazioni di I/O in lettura al secondo|None|
|WriteIops|Operazioni di I/O in scrittura|Conteggio al secondo|Media|Operazioni di I/O in scrittura al secondo|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Dimensioni usate del pool di volumi|Byte|Media|Dimensioni usate del pool|None|
|VolumePoolTotalLogicalSize|Dimensioni logiche totale del pool di volumi|Byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Byte|Totale|Numero di byte inviati dall'interfaccia di rete|None|
|BytesReceivedRate|Byte ricevuti|Byte|Totale|Numero di byte ricevuti dall'interfaccia di rete|None|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|None|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query per zona DNS|None|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS|None|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità di set di record usata da una zona DNS|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS pacchetti in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso|None|
|PacketsDroppedDDoS|DDoS pacchetti in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso eliminati|None|
|PacketsForwardedDDoS|DDoS pacchetti in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso inoltrati|None|
|TCPPacketsInDDoS|DDoS pacchetti TCP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso|None|
|TCPPacketsDroppedDDoS|DDoS pacchetti TCP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso eliminati|None|
|TCPPacketsForwardedDDoS|DDoS pacchetti TCP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso inoltrati|None|
|UDPPacketsInDDoS|DDoS pacchetti UDP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso|None|
|UDPPacketsDroppedDDoS|DDoS pacchetti UDP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso eliminati|None|
|UDPPacketsForwardedDDoS|DDoS pacchetti UDP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso inoltrati|None|
|BytesInDDoS|DDoS byte in ingresso|Byte al secondo|Massimo|DDoS byte in ingresso|None|
|BytesDroppedDDoS|DDoS byte in ingresso eliminati|Byte al secondo|Massimo|DDoS byte in ingresso eliminati|None|
|BytesForwardedDDoS|DDoS byte in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte in ingresso inoltrati|None|
|TCPBytesInDDoS|DDoS byte TCP in ingresso|Byte al secondo|Massimo|DDoS byte TCP in ingresso|None|
|TCPBytesDroppedDDoS|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte TCP in ingresso eliminati|None|
|TCPBytesForwardedDDoS|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte TCP in ingresso inoltrati|None|
|UDPBytesInDDoS|DDoS byte UDP in ingresso|Byte al secondo|Massimo|DDoS byte UDP in ingresso|None|
|UDPBytesDroppedDDoS|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte UDP in ingresso eliminati|None|
|UDPBytesForwardedDDoS|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte UDP in ingresso inoltrati|None|
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Conteggio|Massimo|Sotto attacco DDoS o no|None|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|None|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|None|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Conteggio al secondo|Massimo|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|None|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Porta|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Porta, direzione|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Porta, direzione|
|SynCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Porta, direzione|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo di round trip per i ping a una macchina virtuale|Millisecondi|Media|Tempo di round trip per i ping inviati a una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ping non riusciti a una macchina virtuale|Percentuale|Media|Percentuale del numero di ping non riusciti in totale ping inviati di una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ApplicationRuleHit|Numero di passaggi delle regole dell'applicazione|Conteggio|Totale|Numero di volte in cui sono state raggiunte le regole applicazione|Stato, motivo, protocollo|
|NetworkRuleHit|Numero di passaggi delle regole di rete|Conteggio|Totale|Numero di volte in cui sono state eseguite le regole di rete|Stato, motivo, protocollo|
|FirewallHealth|Stato di integrità del firewall|Percentuale|Media|Stato di integrità del firewall|Stato, motivo|
|DataProcessing|Dati elaborati|Byte|Totale|Quantità totale di dati elaborati dal firewall|None|
|SNATPortUtilization|Utilizzo delle porte SNAT|Percentuale|Media|Utilizzo delle porte SNAT|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media|Numero di byte al secondo distribuiti dal gateway applicazione|None|
|UnhealthyHostCount|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Totale richieste|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Richieste al minuto per host integro|Conteggio|Media|Numero medio di richieste al minuto per host back-end integro in un pool|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Connessioni correnti|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|None|
|CpuUtilization|Utilizzo CPU|Percentuale|Media|Utilizzo corrente della CPU del gateway applicazione|None|
|CapacityUnits|Unità di capacità correnti|Conteggio|Media|Unità di capacità utilizzate|None|
|ComputeUnits|Unità di calcolo correnti|Conteggio|Media|Unità di calcolo utilizzate|None|
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
|BlockedReqCount|Conteggio richieste bloccate del Web Application Firewall|Conteggio|Totale|Conteggio richieste bloccate del Web Application Firewall|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|None|
|P2SBandwidth|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|None|
|P2SConnectionCount|Numero di connessioni da punto a sito|Conteggio|Massimo|Numero di connessioni da punto a sito di un gateway|Protocollo|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Conteggio|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Conteggio|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Conteggio|Media|Livello di luce RX in dBm|Collegamento, corsia|
|TxLightLevel|TxLightLevel|Conteggio|Media|Livello di luce TX in dBm|Collegamento, corsia|
|AdminState|AdminState|Conteggio|Media|Stato amministratore della porta|Collegamento|
|LineProtocol|LineProtocol|Conteggio|Media|Stato del protocollo di riga della porta|Collegamento|
|PortBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Collegamento|
|PortBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Collegamento|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeredCircuitSKey|
|BgpAvailability|Disponibilità BGP|Percentuale|Media|Disponibilità BGP da MSEE verso tutti i peer.|Peering, peer|
|ArpAvailability|Disponibilità ARP|Percentuale|Media|Disponibilità ARP da MSEE verso tutti i peer.|Peering, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|Conteggio al secondo|Media|Bit in ingresso dei dati eliminati al secondo|None|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|Conteggio al secondo|Media|Bit di dati in uscita eliminati al secondo|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|None|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|None|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Conteggio|Massimo|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|None|
|AverageRoundtripMs|Tempo medio di round trip (MS)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|None|
|ChecksFailedPercent|Verifica percentuale non riuscita (anteprima)|Percentuale|Media|% dei controlli di monitoraggio della connettività non riusciti|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tempo di round trip (MS) (anteprima)|Millisecondi|Media|Tempo di round trip in millisecondi per i controlli di monitoraggio della connettività|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|registration.all|Operazioni di registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |None|
|registration.create|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|None|
|registration.update|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|None|
|registration.get|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|None|
|registration.delete|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|None|
|incoming|Messaggi in ingresso|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |None|
|incoming.scheduled|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche push pianificate annullate|None|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Conteggio|Totale|Notifiche push pianificate annullate|None|
|scheduled.pending|Notifiche pianificate in sospeso|Conteggio|Totale|Notifiche pianificate in sospeso|None|
|installation.all|Operazioni di gestione installazione|Conteggio|Totale|Operazioni di gestione installazione|None|
|installation.get|Ottieni operazioni di installazione|Conteggio|Totale|Ottieni operazioni di installazione|None|
|installation.upsert|Crea o aggiorna operazioni di installazione|Conteggio|Totale|Crea o aggiorna operazioni di installazione|None|
|installation.patch|Operazioni di installazione patch|Conteggio|Totale|Operazioni di installazione patch|None|
|installation.delete|Elimina operazioni di installazione|Conteggio|Totale|Elimina operazioni di installazione|None|
|outgoing.allpns.success|Notifiche riuscite|Conteggio|Totale|Numero di tutte le notifiche riuscite.|None|
|outgoing.allpns.invalidpayload|Errori payload|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|None|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Conteggio|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|None|
|outgoing.allpns.channelerror|Errori canale|Conteggio|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|None|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Conteggio|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|None|
|outgoing.wns.success|Notifiche WNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|None|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|None|
|outgoing.wns.badchannel|Errore canale WNS non valido|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|None|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|None|
|outgoing.wns.throttled|Notifiche WNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|None|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Conteggio|Totale|Windows Live non è raggiungibile.|None|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Conteggio|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|None|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|None|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Conteggio|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|None|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Conteggio|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|None|
|outgoing.wns.channelthrottled|Canale WNS limitato|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|None|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.dropped|Notifiche WNS eliminate|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|None|
|outgoing.wns.pnserror|Errori WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|None|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|None|
|outgoing.apns.success|Notifiche APNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|None|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|None|
|outgoing.apns.badchannel|Errore canale APNS non valido|Conteggio|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|None|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Conteggio|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|None|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|None|
|outgoing.apns.pnserror|Errori APNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|None|
|outgoing.gcm.success|Notifiche GCM completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|None|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|None|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|None|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|None|
|outgoing.gcm.throttled|Notifiche GCM limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|None|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Conteggio|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|None|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|None|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|None|
|outgoing.gcm.pnserror|Errori GCM|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|None|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|None|
|outgoing.mpns.success|Notifiche MPNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|None|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|None|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|None|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|None|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Conteggio|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|None|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|None|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Conteggio|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|None|
|outgoing.mpns.pnserror|Errori MPNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|None|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|None|
|notificationhub.pushes|Tutte le notifiche in uscita|Conteggio|Totale|Tutte le notifiche in uscita dell'hub di notifica|None|
|incoming.all.requests|Tutte le richieste in ingresso|Conteggio|Totale|Totale richieste in ingresso per un hub di notifica|None|
|incoming.all.failedrequests|Tutte le richieste in ingresso non riuscite|Conteggio|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Conteggio|Media|Average_% Free Inodes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Free Space|% di spazio libero|Conteggio|Media|Average_% Free Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Conteggio|Media|Average_% Used Inodes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Conteggio|Media|Average_% Used Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Byte letti da disco/sec|Conteggio|Media|Average_Disk Read Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture da disco/sec|Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Byte scritti su disco/sec|Conteggio|Media|Average_Disk Write Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Conteggio|Media|Average_Logical Disk Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Conteggio|Media|Average_% Available Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Conteggio|Media|Average_% Available Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Conteggio|Media|Average_% Used Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Conteggio|Media|Average_% Used Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Conteggio|Media|Average_Available MBytes Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Conteggio|Media|Average_Available MBytes Swap|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Page Reads/sec|Letture pagina/sec|Conteggio|Media|Average_Page Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Page Writes/sec|Scritture pagina/sec|Conteggio|Media|Average_Page Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Conteggio|Media|Average_Pages/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Conteggio|Media|Average_Used MBytes Swap Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Conteggio|Media|Average_Used Memory MBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Conteggio|Media|Average_Total Bytes Transmitted|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Conteggio|Media|Average_Total Bytes Received|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Bytes|Totale byte|Conteggio|Media|Average_Total Bytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Conteggio|Media|Average_Total Packets Transmitted|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Conteggio|Media|Average_Total Packets Received|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Conteggio|Media|Average_Total Rx Errors|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Conteggio|Media|Average_Total Tx Errors|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Conteggio|Media|Average_Total Collisions|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. letture disco/sec|Media letture disco/sec|Conteggio|Media|Average_Avg. letture disco/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. trasferimenti disco/sec disco fisico|Media sec/trasferimento disco|Conteggio|Media|Average_Avg. trasferimenti disco/sec disco fisico|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. scritture disco/sec. disco fisico|Media scritture disco/sec|Conteggio|Media|Average_Avg. scritture disco/sec. disco fisico|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Conteggio|Media|Average_Physical Disk Bytes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Conteggio|Media|Average_Pct Privileged Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Conteggio|Media|Average_Pct User Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Conteggio|Media|Average_Used Memory kBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Conteggio|Media|Average_Virtual Shared Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Conteggio|Media|Average_% DPC Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Idle Time|% Tempo inattività|Conteggio|Media|Average_% Idle Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Conteggio|Media|Average_% Interrupt Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Conteggio|Media|Average_% IO Wait Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Conteggio|Media|Average_% Nice Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Conteggio|Media|Average_% Privileged Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo del processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Conteggio|Media|Average_% User Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Conteggio|Media|Average_Free Physical Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Conteggio|Media|Average_Free Space in Paging Files|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Conteggio|Media|Average_Free Virtual Memory|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Processes|Processi|Conteggio|Media|Average_Processes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Uptime|Tempo di attività|Conteggio|Media|Average_Uptime|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Users|Utenti|Conteggio|Media|Average_Users|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. letture disco/sec|Media letture disco/sec|Conteggio|Media|Average_Avg. letture disco/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. scritture disco/sec. disco fisico|Media scritture disco/sec|Conteggio|Media|Average_Avg. scritture disco/sec. disco fisico|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Lunghezza coda del disco attuale|Conteggio|Media|Average_Current Disk Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Letture da disco/sec|Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Free Space|% di spazio libero|Conteggio|Media|Average_% Free Space|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Processor Time|% di tempo del processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Processor Queue Length|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Conteggio|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aggiornamento|Aggiornamento|Conteggio|Media|Aggiornamento|Computer, prodotto, classificazione, UpdateState, facoltativo, approvato|
|Evento|Evento|Conteggio|Media|Evento|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, EventId|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|PrefixLatency|Latenza prefisso|Millisecondi|Media|Latenza mediana del prefisso|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilità sessione V4|Percentuale|Media|Disponibilità della sessione V4|ConnectionId|
|SessionAvailabilityV6|Disponibilità della sessione V6|Percentuale|Media|Disponibilità della sessione V6|ConnectionId|
|IngressTrafficRate|Velocità di traffico in ingresso|BitsPerSecond|Media|Velocità di traffico in ingresso in bit al secondo|ConnectionId|
|EgressTrafficRate|Frequenza traffico in uscita|BitsPerSecond|Media|Velocità di traffico in uscita in bit al secondo|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|None|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|None|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi|EntityName, OperationResult|
|ServerErrors|Errori del server|Conteggio|Totale|Errori del server per Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus.|None|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.ServiceBus.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di una coda o di un argomento in byte.|EntityName|
|Messages|Numero di messaggi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi contenuti in una coda o in un argomento.|EntityName|
|ActiveMessages|Numero di messaggi attivi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi attivi contenuti in una coda o in un argomento.|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda o in un argomento.|Conteggio|Media|Numero di messaggi non recapitabili in una coda o in un argomento.|EntityName|
|ScheduledMessages|Numero di messaggi pianificati in una coda o un argomento.|Conteggio|Media|Numero di messaggi pianificati in una coda o un argomento.|EntityName|
|NamespaceCpuUsage|CPU|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio.|Replica|
|NamespaceMemoryUsage|Utilizzo memoria|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio.|Replica|
|CPUXNS|CPU (deprecata)|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio. Questa metrica è depricated. Usare invece la metrica CPU (NamespaceCpuUsage).|Replica|
|WSXNS|Utilizzo memoria (obsoleto)|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio. Questa metrica è deprecata. Usare invece la metrica utilizzo memoria (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Conteggio|Massimo|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|None|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|None|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|None|
|UserErrors|Errori utente|Percentuale|Massimo|Percentuale di errori utente|None|
|SystemErrors|Errori di sistema|Percentuale|Massimo|Percentuale di errori di sistema|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|None|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale di i/o del log. Non applicabile ai data warehouse.|None|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai database basati su DTU.|None|
|storage|Spazio dati utilizzato|Byte|Massimo|Spazio dati utilizzato. Non applicabile ai data warehouse.|None|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|None|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|None|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|None|
|deadlock|Deadlock|Conteggio|Totale|Deadlock. Non applicabile ai data warehouse.|None|
|storage_percent|Percentuale spazio dati utilizzato|Percentuale|Massimo|Percentuale di utilizzo dello spazio dei dati. Non applicabile a data warehouse o database con iperscalabilità.|None|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria. Non applicabile ai data warehouse.|None|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro. Non applicabile ai data warehouse.|None|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni. Non applicabile ai data warehouse.|None|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU. Si applica ai database basati su DTU.|None|
|dtu_used|Uso DTU|Conteggio|Media|DTU usato. Si applica ai database basati su DTU.|None|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai database basati su vCore.|None|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai database basati su vCore.|None|
|dwu_limit|Limite DWU|Conteggio|Massimo|Limite DWU. Si applica solo ai data warehouse.|None|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massimo|Percentuale di DWU. Si applica solo ai data warehouse.|None|
|dwu_used|Uso DWU|Conteggio|Massimo|DWU usato. Si applica solo ai data warehouse.|None|
|cache_hit_percent|Percentuale dei riscontri nella cache|Percentuale|Massimo|Percentuale riscontri cache. Si applica solo ai data warehouse.|None|
|cache_used_percent|Percentuale della cache utilizzata|Percentuale|Massimo|Percentuale di utilizzo della cache. Si applica solo ai data warehouse.|None|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della CPU per il processo di SQL Server, misurata dal sistema operativo. Attualmente disponibile solo per database senza server.|None|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della memoria per il processo di SQL Server, misurato dal sistema operativo. Attualmente disponibile solo per database senza server.|None|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Conteggio|Massimo|Dimensioni del file di dati tempdb. Non applicabile ai data warehouse.|None|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse.|None|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massimo|Log percentuale tempdb utilizzato. Non applicabile ai data warehouse.|None|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|None|
|app_cpu_billed|CPU app fatturata|Conteggio|Totale|CPU app fatturata. Si applica ai database senza server.|None|
|app_cpu_percent|Percentuale CPU app|Percentuale|Media|Percentuale CPU app. Si applica ai database senza server.|None|
|app_memory_percent|Percentuale memoria app|Percentuale|Media|Percentuale di memoria dell'app. Si applica ai database senza server.|None|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Archiviazione dati allocata. Non applicabile ai data warehouse.|None|
|memory_usage_percent|Percentuale di memoria|Percentuale|Massimo|Percentuale di memoria. Si applica solo ai data warehouse.|None|
|full_backup_size_bytes|Dimensioni di archiviazione del backup completo|Byte|Massimo|Dimensioni di archiviazione dei backup completi cumulativi. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|None|
|diff_backup_size_bytes|Dimensioni di archiviazione del backup differenziale|Byte|Massimo|Dimensioni di archiviazione del backup differenziale cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|None|
|log_backup_size_bytes|Dimensioni di archiviazione dei backup del log|Byte|Massimo|Dimensioni di archiviazione dei backup del log cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|None|
|database_cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|None|
|database_physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|None|
|database_log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai pool elastici basati su DTU.|None|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|storage_percent|Percentuale spazio dati utilizzato|Percentuale|Media|Percentuale spazio dati utilizzato|None|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|None|
|database_workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|None|
|database_sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Conteggio|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|None|
|storage_limit|Dimensioni massime dati|Byte|Media|Dimensioni massime dati|None|
|eDTU_used|Uso eDTU|Conteggio|Media|eDTU usato. Si applica ai pool elastici basati su DTU.|None|
|database_eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|DatabaseResourceId|
|storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|None|
|database_storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|DatabaseResourceId|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|None|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai pool elastici basati su vCore.|None|
|database_cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|DatabaseResourceId|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|None|
|database_cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata|DatabaseResourceId|
|sqlserver_process_core_percent|Percentuale Core processo SQL Server|Percentuale|Massimo|Utilizzo della CPU come percentuale del processo del database SQL. Si applica ai pool elastici.|None|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Utilizzo della memoria come percentuale del processo del database SQL. Si applica ai pool elastici.|None|
|tempdb_data_size|Kilobyte dimensioni file di dati tempdb|Conteggio|Massimo|Kilobyte dimensioni file di dati tempdb|None|
|tempdb_log_size|Kilobyte dimensioni file di log tempdb|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb|None|
|tempdb_log_used_percent|Log percentuale tempdb utilizzato|Percentuale|Massimo|Log percentuale tempdb utilizzato|None|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|None|
|database_allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|DatabaseResourceId|
|allocated_data_storage_percent|Percentuale allocata spazio dati|Percentuale|Massimo|Percentuale allocata spazio dati|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|ElasticPoolResourceId|
|database_storage_used|Spazio dati utilizzato|Byte|Media|Spazio dati utilizzato|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|None|
|avg_cpu_percent|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|None|
|reserved_storage_mb|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|None|
|storage_space_used_mb|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|None|
|io_requests|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|None|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|None|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|None|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|None|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|None|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|None|
|FileShareSnapshotCount|Conteggio degli snapshot di condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot della condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareQuota|Dimensioni della quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|None|
|QueueCount|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|None|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|None|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/Caches

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ClientIOPS|Totale IOPS client|Conteggio|Media|Frequenza delle operazioni dei file client elaborate dalla cache.|None|
|ClientLatency|Latenza media del client|Millisecondi|Media|Latenza media delle operazioni dei file client nella cache di archiviazione.|None|
|ClientReadIOPS|IOPS lettura client|Conteggio al secondo|Media|Operazioni di lettura client al secondo.|None|
|ClientReadThroughput|Velocità effettiva di lettura cache media|Byte al secondo|Media|Velocità di trasferimento dati in lettura client.|None|
|ClientWriteIOPS|IOPS scrittura client|Conteggio al secondo|Media|Operazioni di scrittura client al secondo.|None|
|ClientWriteThroughput|Velocità effettiva media scrittura cache|Byte al secondo|Media|Velocità di trasferimento dati in scrittura client.|None|
|ClientMetadataReadIOPS|IOPS lettura metadati client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le letture dei dati, che non modificano lo stato persistente.|None|
|ClientMetadataWriteIOPS|IOPS di scrittura dei metadati client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le scritture dei dati, che modificano lo stato persistente.|None|
|ClientLockIOPS|IOPS blocco client|Conteggio al secondo|Media|Operazioni di blocco del file client al secondo.|None|
|StorageTargetHealth|Stato destinazione archiviazione|Conteggio|Media|Risultati booleani del test di connettività tra la cache e le destinazioni di archiviazione.|None|
|Tempo di attività|Tempo di attività|Conteggio|Media|Risultati booleani del test di connettività tra la cache e il sistema di monitoraggio.|None|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Risultato della sessione di sincronizzazione|Conteggio|Media|Metrica che registra il valore 1 ogni volta che l'endpoint del server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|NomeServer|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|NomeServer|
|StorageSyncRecalledTotalNetworkBytes|Dimensioni del richiamo di suddivisione in livelli nel cloud|Byte|Totale|Dimensione dei dati richiamati|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Velocità effettiva di richiamo di suddivisione in livelli nel cloud|Byte al secondo|Media|Dimensioni della velocità effettiva di richiamo dei dati|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Dimensioni del richiamo di suddivisione in livelli nel cloud per applicazione|Byte|Totale|Dimensioni dei dati richiamati dall'applicazione|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. StorageSync/storageSyncServices/syncGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. StorageSync/storageSyncServices/syncGroups/serverEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|ServerEndpointName, SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. StorageSync/storageSyncServices/registeredServers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massimo|% utilizzo unità di streaming|LogicalName, PartitionId|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|LogicalName, PartitionId|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName, PartitionId|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|LogicalName, PartitionId|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|LogicalName, PartitionId|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|LogicalName, PartitionId|
|Errori|Errori di runtime|Conteggio|Totale|Errori di runtime|LogicalName, PartitionId|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|None|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|None|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|None|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|None|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|None|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|None|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|None|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|None|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|None|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|None|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|None|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|None|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|None|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|None|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|None|
|WarmStorageMaxProperties|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|None|
|WarmStorageUsedProperties|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di lettura nel periodo di campionamento.|None|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di scrittura nel periodo di campionamento.|None|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco dovuta a operazioni di lettura nel periodo di campionamento.|None|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|None|
|DiskReadOperations|Operazioni di lettura su disco|Conteggio|Totale|Numero di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|None|
|DiskWriteOperations|Operazioni di scrittura su disco|Conteggio|Totale|Numero di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|None|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|None|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|None|
|DiskReadLatency|Latenza di lettura da disco|Millisecondi|Media|Latenza di lettura totale. Somma delle latenze di lettura del dispositivo e del kernel.|None|
|DiskWriteLatency|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. Somma delle latenze di scrittura del dispositivo e del kernel.|None|
|NetworkInBytesPerSecond|Rete in byte/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|None|
|NetworkOutBytesPerSecond|Byte di rete in uscita/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|None|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|None|
|Rete in uscita|Rete in uscita|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|None|
|MemoryUsed|Memoria utilizzata|Byte|Media|Quantità di memoria del computer utilizzata dalla VM.|None|
|MemoryGranted|Memoria concessa|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host fino a quando non viene toccata una sola volta e la memoria concessa potrebbe essere invertita o rimossa se il VMkernel richiede la memoria.|None|
|MemoryActive|Memoria attiva|Byte|Media|Quantità di memoria usata dalla macchina virtuale in un intervallo di tempo ridotto. Questo è il numero "vero" della quantità di memoria attualmente necessaria per la macchina virtuale. È possibile che venga scambiata una memoria aggiuntiva inutilizzata o che non abbia alcun effetto sulle prestazioni del Guest.|None|
|CPU percentuale|CPU percentuale|Percentuale|Media|Utilizzo della CPU. Questo valore viene segnalato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale a 2 vie che usa il 50% di un sistema a quattro core USA completamente due core.|None|
|PercentageCpuReady|Percentuale di disponibilità CPU|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento precedente.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda del disco|Conteggio|Media|Lunghezza coda del disco|Istanza|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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
|Selettori|Numero degli handle|Conteggio|Media|Numero degli handle|Istanza|
|Thread|Numero dei thread|Conteggio|Media|Numero dei thread|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly correnti|Conteggio|Media|Assembly correnti|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|None|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|MB/millisecondi|Totale|[Unità di esecuzione funzioni](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
|FunctionExecutionCount|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly correnti|Conteggio|Media|Assembly correnti|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|None|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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
|Selettori|Numero degli handle|Conteggio|Media|Numero degli handle|Istanza|
|Thread|Numero dei thread|Conteggio|Media|Numero dei thread|Istanza|
|PrivateBytes|Byte privati|Byte|Media|Byte privati|Istanza|
|IoReadBytesPerSecond|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoWriteBytesPerSecond|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoOtherBytesPerSecond|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoReadOperationsPerSecond|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteOperationsPerSecond|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|IoOtherOperationsPerSecond|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|RequestsInApplicationQueue|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|CurrentAssemblies|Assembly correnti|Conteggio|Media|Assembly correnti|Istanza|
|TotalAppDomains|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|
|Gen0Collections|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Stato controllo integrità|Conteggio|Media|Stato controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
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
|DiskQueueLength|Lunghezza coda del disco|Conteggio|Media|Lunghezza coda del disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|ActiveRequests|Richieste attive|Conteggio|Totale|Richieste attive|Istanza|
|TotalFrontEnds|Front end totali|Conteggio|Media|Front end totali|None|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|None|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|None|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|None|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|None|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|None|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](platform-logs-overview.md)
