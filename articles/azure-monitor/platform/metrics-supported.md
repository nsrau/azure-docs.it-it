---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: e8bae2062051156d6de378e54bc354b3f785e403
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515462"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

> [!NOTE]
> Questo elenco è in gran parte generato automaticamente dall'API REST delle metriche di monitoraggio di Azure. Tutte le modifiche apportate a questo elenco tramite GitHub possono essere scritte senza preavviso. Per informazioni dettagliate su come effettuare gli aggiornamenti permanenti, contattare l'autore di questo articolo.

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. 

Questo articolo è un elenco completo di metriche di tutte le piattaforme (ovvero raccolte automaticamente) attualmente disponibili con la pipeline metrica consolidata di monitoraggio di Azure. L'ultimo aggiornamento dell'elenco è stato eseguito il 27 marzo 2020. Le metriche modificate o aggiunte dopo questa data potrebbero non essere visualizzate di seguito. Per eseguire una query e accedere all'elenco di metriche a livello di codice, usare la [versione api 2018-01-01](/rest/api/monitor/metricdefinitions). Altre metriche non presenti in questo elenco possono essere disponibili nel portale o usando le API legacy.

Le metriche sono organizzate in base ai provider di risorse e al tipo di risorsa. Per un elenco dei servizi e dei provider di risorse appartenenti, vedere [provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Metriche del sistema operativo guest

Le metriche per il sistema operativo guest (sistema operativo guest) che viene eseguito in macchine virtuali di Azure, Service Fabric e servizi cloud **non** sono elencate qui. Al contrario, le metriche delle prestazioni del sistema operativo Guest devono essere raccolte tramite uno o più agenti eseguiti in o come parte del sistema operativo guest.  Le metriche del sistema operativo guest includono contatori delle prestazioni che tengono traccia della percentuale di CPU Guest o dell'utilizzo della memoria, entrambi usati spesso per la scalabilità automatica o gli avvisi.  Usando l' [estensione diagnostica di Azure](diagnostics-extension-overview.md), è possibile inviare le metriche delle prestazioni del sistema operativo guest nello stesso database in cui sono archiviate le metriche della piattaforma. Instrada le metriche del sistema operativo guest tramite l'API [metrica personalizzata](metrics-custom-overview.md) . Sarà quindi possibile tracciare, avvisare e usare le metriche del sistema operativo guest come le metriche della piattaforma. Per altre informazioni, vedere [Panoramica degli agenti di monitoraggio](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Metriche della piattaforma di routing in altre posizioni

È possibile usare [le impostazioni di diagnostica](diagnostic-settings.md) per instradare le metriche della piattaforma ad archiviazione di Azure, i log di monitoraggio di Azure (e quindi log Analytics) e gli hub eventi.  

Esistono alcune limitazioni per gli elementi che possono essere indirizzati e il modulo in cui sono archiviati. 
- Non tutte le metriche sono esportabili in altre posizioni. Per un elenco delle metriche della piattaforma esportabili tramite le impostazioni di diagnostica, vedere [questo articolo](metrics-supported-export-diagnostic-settings.md).

- L'invio di metriche multidimensionali ad altre posizioni tramite le impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
*Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|MemoryUsage|Memoria: Utilizzo della memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria xVelocity (VertiPaq) in eccesso rispetto al limite di memoria del motore xVelocity.|ServerResourceType|
|MemoryLimitHard|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitHigh|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitLow|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitVertiPaq|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|ServerResourceType|
|Quota|Memoria: Quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|ServerResourceType|
|QuotaBlocked|Memoria: quota bloccata|Conteggio|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|ServerResourceType|
|VertiPaqNonpaged|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|ServerResourceType|
|VertiPaqPaged|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|ServerResourceType|
|RowsReadPerSec|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|ServerResourceType|
|RowsConvertedPerSec|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|ServerResourceType|
|RowsWrittenPerSec|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|ServerResourceType|
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
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|ServerResourceType|
|mashup_engine_qpu_metric|QPU Motore M|Conteggio|Media|Utilizzo QPU da parte dei processi motore mashup|ServerResourceType|
|mashup_engine_memory_metric|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Byte privati del motore M|Byte|Media|Utilizzo di byte privati da processi del motore mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore M|Byte|Media|Utilizzo dei byte virtuali da processi del motore mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests (Totale richieste gateway) (deprecato)|Conteggio|Totale|Numero di richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|SuccessfulRequests|Successful Gateway Requests (Richieste gateway riuscite) (deprecato)|Conteggio|Totale|Numero di richieste gateway riuscite: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|UnauthorizedRequests|Unauthorized Gateway Requests (Richieste del gateway non autorizzate) (deprecato)|Conteggio|Totale|Numero di richieste del gateway non autorizzate: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|FailedRequests|Failed Gateway Requests (Richieste gateway non riuscite) (deprecato)|Conteggio|Totale|Numero di errori nelle richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|OtherRequests|Other Gateway Requests (Altre richieste del gateway) (deprecato)|Conteggio|Totale|Numero di altre richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Località, nome host|
|Duration|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Località, nome host|
|BackendDuration|Duration of Backend Requests (Durata delle richieste back-end)|Millisecondi|Media|Durata delle richieste back-end in millisecondi|Località, nome host|
|Capacità|Capacità|Percentuale|Media|Metrica di utilizzo per il servizio ApiManagement|Location|
|EventHubTotalEvents|Total EventHub Events (Eventi EventHub totali)|Conteggio|Totale|Numero di eventi inviati a EventHub|Location|
|EventHubSuccessfulEvents|Successful EventHub Events (Eventi EventHub riusciti)|Conteggio|Totale|Numero di eventi EventHub riusciti|Location|
|EventHubTotalFailedEvents|Failed EventHub Events (Eventi EventHub non riusciti)|Conteggio|Totale|Numero di eventi EventHub non riusciti|Location|
|EventHubRejectedEvents|Rejected EventHub Events (Eventi EventHub rifiutati)|Conteggio|Totale|Numero di eventi EventHub rifiutati (configurazione errata o non autorizzata)|Location|
|EventHubThrottledEvents|Throttled EventHub Events (Eventi EventHub limitati)|Conteggio|Totale|Numero di eventi EventHub limitati|Location|
|EventHubTimedoutEvents|Timed Out EventHub Events (Timeout eventi EventHub)|Conteggio|Totale|Numero di eventi EventHub di timeout|Location|
|EventHubDroppedEvents|Dropped EventHub Events (Eventi EventHub rimossi)|Conteggio|Totale|Numero di eventi ignorati a causa del raggiungimento del limite delle dimensioni della coda|Location|
|EventHubTotalBytesSent|Size of EventHub Events (Dimensione degli eventi EventHub)|Byte|Totale|Dimensioni totali degli eventi EventHub in byte|Location|
|Requests|Requests|Conteggio|Totale|Metriche delle richieste del gateway con più dimensioni|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|Problemidi|Stato connettività di rete delle risorse (anteprima)|Conteggio|Totale|Stato della connettività di rete dei tipi di risorsa dipendenti dal servizio gestione API|Località, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Conteggio|Conteggio|Numero totale di richieste HTTP in ingresso.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Conteggio|Media|Latenza su una richiesta HTTP.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|System CPU Usage Percentage (Percentuale di utilizzo CPU di sistema)|Percentuale|Media|Utilizzo della CPU recente per l'intero sistema|AppName, Pod|
|AppCpuUsagePercentage|App CPU Usage Percentage (Percentuale di utilizzo CPU app)|Percentuale|Media|Percentuale di utilizzo CPU JVM app|AppName, Pod|
|AppMemoryCommitted|App Memory Assigned (Memoria app assegnata)|Byte|Media|Memoria assegnata a JVM in byte|AppName, Pod|
|AppMemoryUsed|App Memory Used (Memoria app usata)|Byte|Media|Memoria app utilizzata in byte|AppName, Pod|
|AppMemoryMax|App Memory Max (Memoria app max)|Byte|Massimo|Quantità massima di memoria in byte che può essere utilizzata per la gestione della memoria|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Max Available Old Generation Data Size (Dimensione dati max disponibile di precedente generazione)|Byte|Media|Dimensioni massime del pool di memoria di generazione precedente|AppName, Pod|
|OldGenMemoryPoolBytes|Old Generation Data Size (Dimensione dati di precedente generazione)|Byte|Media|Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo|AppName, Pod|
|OldGenPromotedBytes|Promote to Old Generation Data Size (Alza il livello a dimensione dati di precedente generazione)|Byte|Massimo|Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC|AppName, Pod|
|YoungGenPromotedBytes|Promote to Young Generation Data Size (Alza il livello a dimensione dati di nuova generazione)|Byte|Massimo|Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo|AppName, Pod|
|GCPauseTotalCount|GC Pause Count (Conteggio pausa GC)|Conteggio|Totale|GC Pause Count (Conteggio pausa GC)|AppName, Pod|
|GCPauseTotalTime|GC Pause Total Time (Tempo totale pausa GC)|Millisecondi|Totale|GC Pause Total Time (Tempo totale pausa GC)|AppName, Pod|
|TomcatSentBytes|Tomcat Total Sent Bytes (Byte totali inviati Tomcat)|Byte|Totale|Tomcat Total Sent Bytes (Byte totali inviati Tomcat)|AppName, Pod|
|TomcatReceivedBytes|Tomcat Total Received Bytes (Byte totali ricevuti Tomcat)|Byte|Totale|Tomcat Total Received Bytes (Byte totali ricevuti Tomcat)|AppName, Pod|
|TomcatRequestTotalTime|Tomcat Request Total Times (Tempo totale richiesta Tomcat)|Millisecondi|Totale|Tomcat Request Total Times (Tempo totale richiesta Tomcat)|AppName, Pod|
|TomcatRequestTotalCount|Tomcat Request Total Count (Conteggio totale richieste Tomcat)|Conteggio|Totale|Tomcat Request Total Count (Conteggio totale richieste Tomcat)|AppName, Pod|
|TomcatResponseAvgTime|Tomcat Request Average Time (Tempo medio richiesta Tomcat)|Millisecondi|Media|Tomcat Request Average Time (Tempo medio richiesta Tomcat)|AppName, Pod|
|TomcatRequestMaxTime|Tomcat Request Max Time (Tempo max richiesta Tomcat)|Millisecondi|Massimo|Tomcat Request Max Time (Tempo max richiesta Tomcat)|AppName, Pod|
|TomcatErrorCount|Tomcat Global Error (Errore globale Tomcat)|Conteggio|Totale|Tomcat Global Error (Errore globale Tomcat)|AppName, Pod|
|TomcatSessionActiveMaxCount|Tomcat Session Max Active Count (Conteggio max attivo sessioni Tomcat)|Conteggio|Totale|Tomcat Session Max Active Count (Conteggio max attivo sessioni Tomcat)|AppName, Pod|
|TomcatSessionAliveMaxTime|Tomcat Session Max Alive Time (Tempo max attivo sessioni Tomcat)|Millisecondi|Massimo|Tomcat Session Max Alive Time (Tempo max attivo sessioni Tomcat)|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat Session Alive Count (Conteggio attivo sessioni Tomcat)|Conteggio|Totale|Tomcat Session Alive Count (Conteggio attivo sessioni Tomcat)|AppName, Pod|
|TomcatSessionCreatedCount|Tomcat Session Created Count (Conteggio creazione sessioni Tomcat)|Conteggio|Totale|Tomcat Session Created Count (Conteggio creazione sessioni Tomcat)|AppName, Pod|
|TomcatSessionExpiredCount|Tomcat Session Expired Count (Conteggio scadenza sessioni Tomcat)|Conteggio|Totale|Tomcat Session Expired Count (Conteggio scadenza sessioni Tomcat)|AppName, Pod|
|TomcatSessionRejectedCount|Tomcat Session Rejected Count (Conteggio rifiuto sessioni Tomcat)|Conteggio|Totale|Tomcat Session Rejected Count (Conteggio rifiuto sessioni Tomcat)|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs (Esecuzioni totali della distribuzione di aggiornamenti)|Conteggio|Totale|Totale esecuzioni di distribuzioni di aggiornamenti software|SoftwareUpdateConfigurationName, stato|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs (Esecuzioni totali della distribuzione di aggiornamenti del computer)|Conteggio|Totale|Il computer di distribuzione degli aggiornamenti software totale viene eseguito in un'esecuzione di distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|TaskStartEvent|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|poolId, jobId|
|TaskCompleteEvent|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|poolId, jobId|
|TaskFailEvent|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|poolId, jobId|
|PoolCreateEvent|Eventi pool creati|Conteggio|Totale|Il numero totale di pool che sono stati creati|poolId|
|PoolResizeStartEvent|Eventi ridimensionamento pool avviati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool avviati|poolId|
|PoolResizeCompleteEvent|Eventi ridimensionamento pool completati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool completati|poolId|
|PoolDeleteStartEvent|Eventi eliminazione pool avviati|Conteggio|Totale|Il numero totale di eliminazioni di pool avviate|poolId|
|PoolDeleteCompleteEvent|Eventi eliminazione pool completati|Conteggio|Totale|Il numero totale di eliminazioni di pool completate|poolId|
|JobDeleteCompleteEvent|Eventi eliminazione processi completati|Conteggio|Totale|Numero totale dei processi eliminati.|jobId|
|JobDeleteStartEvent|Eventi eliminazione processi avviati|Conteggio|Totale|Numero totale di processi da eliminare.|jobId|
|JobDisableCompleteEvent|Eventi disabilitazione processi completati|Conteggio|Totale|Numero totale dei processi disabilitati.|jobId|
|JobDisableStartEvent|Eventi disabilitazione processi avviati|Conteggio|Totale|Numero totale di processi da disabilitare.|jobId|
|JobStartEvent|Eventi di avvio processi|Conteggio|Totale|Numero totale di processi avviati.|jobId|
|JobTerminateCompleteEvent|Eventi terminazione processi completati|Conteggio|Totale|Numero totale di processi terminati.|jobId|
|JobTerminateStartEvent|Eventi terminazione processi avviati|Conteggio|Totale|Numero totale di processi da terminare.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Processo inviato|Processo inviato|Conteggio|Totale|Numero di processi inviati|Scenario, clustername|
|Job Completed (Processo completato)|Job Completed (Processo completato)|Conteggio|Totale|Numero di processi completati|Scenario, clustername, ResultType|
|Totale nodi|Totale nodi|Conteggio|Media|Numero di nodi totali|Scenario, clustername|
|Active Nodes (Nodi attivi)|Active Nodes (Nodi attivi)|Conteggio|Media|Il numero di nodi in esecuzione|Scenario, clustername|
|Idle Nodes (Nodi inattivi)|Idle Nodes (Nodi inattivi)|Conteggio|Media|Il numero di nodi inattivi|Scenario, clustername|
|Unusable Nodes (Nodi non utilizzabili)|Unusable Nodes (Nodi non utilizzabili)|Conteggio|Media|Il numero di nodi che non possono essere usati|Scenario, clustername|
|Preempted Nodes (Nodi superati)|Preempted Nodes (Nodi superati)|Conteggio|Media|Numero di nodi annullati|Scenario, clustername|
|Leaving Nodes (Nodi di uscita)|Leaving Nodes (Nodi di uscita)|Conteggio|Media|Numero di nodi in uscita|Scenario, clustername|
|Core totali|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Active Cores (Core attivi)|Active Cores (Core attivi)|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Idle Cores (Core inattivi)|Idle Cores (Core inattivi)|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Unusable Cores (Core non utilizzabili)|Unusable Cores (Core non utilizzabili)|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Preempted Cores (Core superati)|Preempted Cores (Core superati)|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Leaving Cores (Core di uscita)|Leaving Cores (Core di uscita)|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Quota Utilization Percentage (Percentuale di utilizzo quota)|Quota Utilization Percentage (Percentuale di utilizzo quota)|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU Usage Percentage (Percentuale di utilizzo CPU)|Percentuale|Massimo|CPU Usage Percentage (Percentuale di utilizzo CPU)|Nodo|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Utilizzo della memoria|Nodo|
|MemoryLimit|Limite memoria|Byte|Media|Limite memoria|Nodo|
|MemoryUsagePercentageInDouble|Memory Usage Percentage (Percentuale di utilizzo memoria)|Percentuale|Media|Memory Usage Percentage (Percentuale di utilizzo memoria)|Nodo|
|StorageUsage|Utilizzo dello spazio di archiviazione|Byte|Media|Utilizzo dello spazio di archiviazione|Nodo|
|IOReadBytes|IO Read Bytes (Byte operazioni di I/O in lettura)|Byte|Totale|IO Read Bytes (Byte operazioni di I/O in lettura)|Nodo|
|IOWriteBytes|IO Write Bytes (Byte operazioni di I/O in scrittura)|Byte|Totale|IO Write Bytes (Byte operazioni di I/O in scrittura)|Nodo|
|ConnectionAccepted|Accepted Connections (Connessioni accettate)|Conteggio|Totale|Accepted Connections (Connessioni accettate)|Nodo|
|ConnectionHandled|Handled Connections (Connessioni gestite)|Conteggio|Totale|Handled Connections (Connessioni gestite)|Nodo|
|ConnectionActive|Connessioni attive|Conteggio|Media|Connessioni attive|Nodo|
|RequestHandled|Handled Requests (Richieste gestite)|Conteggio|Totale|Handled Requests (Richieste gestite)|Nodo|
|ProcessedBlocks|Processed Blocks (Blocchi elaborati)|Conteggio|Totale|Processed Blocks (Blocchi elaborati)|Nodo|
|ProcessedTransactions|Processed Transactions (Transazioni elaborate)|Conteggio|Totale|Processed Transactions (Transazioni elaborate)|Nodo|
|PendingTransactions|Pending Transactions (Transazioni in sospeso)|Conteggio|Media|Pending Transactions (Transazioni in sospeso)|Nodo|
|QueuedTransactions|Queued Transactions (Transazioni in coda)|Conteggio|Media|Queued Transactions (Transazioni in coda)|Nodo|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, ruleName, azione|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Percentuale CPU|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|Nessuno|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Percentuale CPU|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di spazio di archiviazione utilizzata dal servizio BLOB dell'account di archiviazione in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Il numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Il numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuno|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di spazio di archiviazione utilizzata dal servizio tabelle dell'account di archiviazione in byte.|Nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Il numero di tabelle nel servizio tabelle dell'account di archiviazione.|Nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di spazio di archiviazione usata dal servizio file dell'account di archiviazione in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Il numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Il numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuno|
|FileShareSnapshotCount|Numero di snapshot in condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot in condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareCapacityQuota|Dimensioni quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'Servizio di accodamento dell'account di archiviazione in byte.|Nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Il numero di coda nel Servizio di accodamento dell'account di archiviazione.|Nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo di messaggi della coda nel Servizio di accodamento dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|TotalTokenCalls|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|
|CharactersTranslated|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained (Caratteri di cui è stato eseguito il training)|Conteggio|Totale|Numero totale di caratteri sottoposti a training.|ApiName, OperationName, Region|
|SpeechSessionDuration|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|ApiName, OperationName, Region|
|TotalTransactions|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|Nessuno|
|ProcessedImages|Immagini elaborate|Conteggio|Totale| Numero di transazioni per l'elaborazione di immagini.|ApiName, FeatureName, canale, area|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Percentuale CPU|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuno|
|Rete in uscita|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD [(deprecato)](portal-disk-metrics-deprecation.md)] (Portal-disk-Metrics-Deprecation.MD)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti da disco/sec per un disco dati|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco/sec per un disco dati|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura da disco/sec per un disco dati|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco/sec per un disco dati|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità coda per un disco dati|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti da disco/sec per un disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per un disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Numero massimo richieste di creazione flussi in ingresso|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuno|
|Numero massimo richieste di creazione flussi in uscita|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Riscontri letture cache per un disco dati Premium|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN|
|Mancati riscontri letture cache per un disco dati Premium|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN|
|Riscontri letture cache per un disco del sistema operativo Premium|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|Nessuno|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|Nessuno|
|Rete in ingresso (totale)|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Rete in uscita (totale)|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Percentuale CPU|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Rete in ingresso|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|VMName|
|Rete in uscita|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|VMName|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti da disco/sec per un disco dati|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti su disco/sec per un disco dati|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura da disco/sec per un disco dati|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco/sec per un disco dati|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Profondità coda per un disco dati|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti da disco/sec per un disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti su disco/sec per un disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Profondità coda per un disco del sistema operativo|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|VMName|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in ingresso|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in uscita|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Riscontri letture cache per un disco dati Premium|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN, VMName|
|Mancati riscontri letture cache per un disco dati Premium|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN, VMName|
|Riscontri letture cache per un disco del sistema operativo Premium|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|VMName|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|VMName|
|Rete in ingresso (totale)|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Rete in uscita (totale)|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Percentuale CPU|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuno|
|Rete in ingresso|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuno|
|Rete in uscita|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuno|
|Byte letti da disco/sec per disco|Byte letti dal disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti disco dati/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Profondità coda per disco dati|Disco dati QD [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti dal disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura disco del sistema operativo/sec [(deprecate)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per disco del sistema operativo|Disco del sistema operativo QD [(deprecato)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Byte letti da disco/sec per un disco dati|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco/sec per un disco dati|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura da disco/sec per un disco dati|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco/sec per un disco dati|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Profondità coda per un disco dati|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti da disco/sec per un disco del sistema operativo|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Byte scritti su disco/sec per un disco del sistema operativo|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Numero massimo richieste di creazione flussi in ingresso|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuno|
|Numero massimo richieste di creazione flussi in uscita|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuno|
|Riscontri letture cache per un disco dati Premium|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN|
|Mancati riscontri letture cache per un disco dati Premium|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN|
|Riscontri letture cache per un disco del sistema operativo Premium|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|Nessuno|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|Nessuno|
|Rete in ingresso (totale)|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuno|
|Rete in uscita (totale)|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuno|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo di CPU|Conteggio|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|Nessuno|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|Nessuno|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count (Conteggio pull totale)|Conteggio|Media|Numero totale di pull di immagini|Nessuno|
|SuccessfulPullCount|Successful Pull Count (Conteggio pull riuscito)|Conteggio|Media|Numero di pull di immagini riuscite|Nessuno|
|TotalPushCount|Total Push Count (Conteggio push totale)|Conteggio|Media|Numero totale di push di immagini|Nessuno|
|SuccessfulPushCount|Successful Push Count (Conteggio push riuscito)|Conteggio|Media|Numero di push di immagini riusciti|Nessuno|
|RunDuration|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|Nessuno|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Media|Numero totale di core CPU disponibili in un cluster gestito|Nessuno|
|kube_node_status_allocatable_memory_bytes|Numero totale di memoria disponibile in un cluster gestito|Byte|Media|Numero totale di memoria disponibile in un cluster gestito|Nessuno|
|kube_pod_status_ready|Numero di pod in stato Pronto|Conteggio|Media|Numero di pod in stato Pronto|spazio dei nomi, Pod|
|kube_node_status_condition|Stati per diverse condizioni dei nodi|Conteggio|Media|Stati per diverse condizioni dei nodi|Condition, status, status2, node|
|kube_pod_status_phase|Numero di pod per fase|Conteggio|Media|Numero di pod per fase|fase, spazio dei nomi, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SuccessfullRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite effettuate dal provider personalizzato|HttpMethod, CallPath, StatusCode|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Ottiene i log disponibili per i provider di risorse personalizzati|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva lettura (rete)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettiva download cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|CloudUploadThroughputPerShare|Velocità effettiva caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|BytesUploadedToCloudPerShare|Byte cloud caricati (condivisione)|Byte|Media|Il numero totale di byte caricati in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|TotalCapacity|Capacità totale|Byte|Media|Capacità totale|Nessuno|
|AvailableCapacity|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di Reporting.|Nessuno|
|CloudUploadThroughput|Velocità effettiva caricamento cloud|Byte al secondo|Media|La velocità effettiva di caricamento nel cloud in Azure durante il periodo di Reporting.|Nessuno|
|CloudReadThroughput|Velocità effettiva download cloud|Byte al secondo|Media|La velocità effettiva di download del cloud in Azure durante il periodo di Reporting.|Nessuno|
|BytesUploadedToCloud|Byte cloud caricati (dispositivo)|Byte|Media|Il numero totale di byte caricati in Azure da un dispositivo durante il periodo di Reporting.|Nessuno|
|HyperVVirtualProcessorUtilization|Calcolo Edge - Percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|HyperVMemoryUtilization|Calcolo Edge - Utilizzo memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale||PipelineName, ActivityName|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale||PipelineName, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||FailureType, nome|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||FailureType, nome|
|PipelineCancelledRuns|Cancelled pipeline runs metrics (Metrica esecuzioni pipeline annullate)|Conteggio|Totale||FailureType, nome|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|ActivityCancelledRuns|Cancelled activity runs metrics (Metrica esecuzioni attività annullate)|Conteggio|Totale||ActivityType, PipelineName, FailureType, nome|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Nome, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Nome, FailureType|
|TriggerCancelledRuns|Cancelled trigger runs metrics (Metrica esecuzioni trigger annullate)|Conteggio|Totale||Nome, FailureType|
|IntegrationRuntimeCpuPercentage|Uso della CPU di Integration runtime|Percentuale|Media||IntegrationRuntimeName, nodeName|
|IntegrationRuntimeAvailableMemory|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName, nodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Integration runtime queue duration (Durata coda runtime di integrazione)|Secondi|Media||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Integration runtime queue length (Lunghezza coda runtime di integrazione)|Conteggio|Media||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Numero di nodi disponibili in Integration Runtime|Conteggio|Media||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximum allowed entities count (Conteggio massimo consentito per le entità)|Conteggio|Massimo||Nessuno|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit) (Dimensione massima consentita per la factory (unità GB))|Conteggio|Massimo||Nessuno|
|ResourceCount|Total entities count (Conteggio totale entità)|Conteggio|Massimo||Nessuno|
|FactorySizeInGbUnits|Total factory size (GB unit) (Dimensione totale factory (unità GB))|Conteggio|Massimo||Nessuno|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|Nessuno|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|Nessuno|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati|Nessuno|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|Nessuno|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|Nessuno|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati|Nessuno|
|JobStage|Processi nella fase|Conteggio|Totale|Numero di processi in ogni fase.|Nessuno|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massimo|Quantità totale di dati archiviati nell'account|Nessuno|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuno|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuno|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|Nessuno|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|Nessuno|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ShareCount|Condivisioni inviate|Conteggio|Massimo|Numero di condivisioni inviate nell'account|ShareName|
|ShareSubscriptionCount|Condivisioni ricevute|Conteggio|Massimo|Numero di condivisioni ricevute nell'account|ShareSubscriptionName|
|SucceededShareSynchronizations|Snapshot condivisione inviata riuscita|Conteggio|Conteggio|Numero di snapshot di condivisione inviati riusciti nell'account|Nessuno|
|FailedShareSynchronizations|Snapshot di condivisione inviata non riusciti|Conteggio|Conteggio|Numero di snapshot di condivisione inviati non riusciti nell'account|Nessuno|
|SucceededShareSubscriptionSynchronizations|Snapshot condivisione ricevuta riusciti|Conteggio|Conteggio|Numero di snapshot di condivisione ricevuti riusciti nell'account|Nessuno|
|FailedShareSubscriptionSynchronizations|Snapshot di condivisione ricevuti non riusciti|Conteggio|Conteggio|Numero di snapshot di condivisione ricevuti non riusciti nell'account|Nessuno|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuno|
|backup_storage_used|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuno|
|backup_storage_used|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|backup_storage_used|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massimo|Ritardo replica in secondi|Nessuno|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massimo|Ritardo in byte della replica più in ritardo|Nessuno|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|iops|IOPS|Conteggio|Media|Operazioni di i/o al secondo|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuno|
|iops|IOPS|Conteggio|Media|Operazioni di i/o al secondo|Nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|connections_succeeded|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuno|
|maximum_used_transactionIDs|Numero massimo di ID transazione utilizzati|Conteggio|Media|Numero massimo di ID transazione utilizzati|Nessuno|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuno|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuno|
|c2d.commands.egress.complete.success|C2D message deliveries completed (Recapiti messaggi C2D completati)|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|Nessuno|
|c2d.commands.egress.abandon.success|C2D messages abandoned (Messaggi C2D abbandonati)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|Nessuno|
|c2d.commands.egress.reject.success|C2D messages rejected (Messaggi C2D rifiutati)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|Nessuno|
|C2DMessagesExpired|C2D Messages Expired (preview) (Messaggi C2D scaduti (anteprima))|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|Nessuno|
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
|EventGridDeliveries|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento ( https://aka.ms/ioteventgrid) .|ResourceId, result, EventType|
|EventGridLatency|Event Grid latency (preview) (Latenza Griglia di eventi (anteprima))|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|ResourceId, EventType|
|RoutingDeliveries|Recapito di routing (anteprima)|Millisecondi|Totale|Il numero di tentativi di recapito di messaggi a tutti gli endpoint tramite l'hub. Per visualizzare il numero di tentativi riusciti o non riusciti, utilizzare la dimensione risultato. Per visualizzare il motivo dell'errore, ad esempio non valido, eliminato o orfano, utilizzare la dimensione FailureReasonCategory. È anche possibile usare le dimensioni EndpointName e EndpointType per comprendere il numero di messaggi recapitati ai diversi endpoint. Il valore della metrica aumenta di uno per ogni tentativo di recapito, incluso se il messaggio viene recapitato a più endpoint o se il messaggio viene recapitato più volte allo stesso endpoint.|ResourceId, EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Latenza recapito routing (anteprima)|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e il messaggio di telemetria in ingresso in un endpoint. È possibile usare le dimensioni EndpointName e EndpointType per comprendere la latenza per i diversi endpoint.|ResourceId, EndpointType, EndpointName, RoutingSource|
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
|deviceDataUsage|Total device data usage (Utilizzo totale dati dispositivo)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuno|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuno|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuno|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|Nessuno|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|Nessuno|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, stato|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, stato, protocollo|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AddRegion|Area aggiunta|Conteggio|Conteggio|Area aggiunta|Region|
|AvailableStorage|Spazio di archiviazione disponibile|Byte|Totale|Totale spazio di archiviazione disponibile segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni Cassandra chiuse, segnalate a una granularità di 1 minuto|APIType, Region, ClosureReason|
|CassandraKeyspaceDelete|Spazio di portadi Cassandra eliminato|Conteggio|Conteggio|Spazio di portadi Cassandra eliminato|ResourceName, tipologia API, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Velocità effettiva del tasto di Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva del tasto di Cassandra aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Spazio del tasto Cassandra aggiornato|Conteggio|Conteggio|Spazio del tasto Cassandra aggiornato|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Addebiti richieste Cassandra|Conteggio|Totale|Ur utilizzate per le richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste Cassandra effettuate|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Tabella Cassandra eliminata|Conteggio|Conteggio|Tabella Cassandra eliminata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Velocità effettiva della tabella Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva della tabella Cassandra aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Tabella Cassandra aggiornata|Conteggio|Conteggio|Tabella Cassandra aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Account creato|Conteggio|Conteggio|Account creato|Nessuno|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DeleteAccount|Account eliminato|Conteggio|Conteggio|Account eliminato|Nessuno|
|DocumentCount|Conteggio documenti|Conteggio|Totale|Numero totale di documenti segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DocumentQuota|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalata con granularità di 5 minuti|CollectionName, DatabaseName, Region|
|GremlinDatabaseDelete|Database Gremlin eliminato|Conteggio|Conteggio|Database Gremlin eliminato|ResourceName, tipologia API, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Velocità effettiva del database Gremlin aggiornata|Conteggio|Conteggio|Velocità effettiva del database Gremlin aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Database Gremlin aggiornato|Conteggio|Conteggio|Database Gremlin aggiornato|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Grafo Gremlin eliminato|Conteggio|Conteggio|Grafo Gremlin eliminato|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Velocità effettiva del grafo Gremlin aggiornata|Conteggio|Conteggio|Velocità effettiva del grafo Gremlin aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Grafico Gremlin aggiornato|Conteggio|Conteggio|Grafico Gremlin aggiornato|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Utilizzo indice|Byte|Totale|Totale utilizzo di indici restituiti a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests (Richieste di metadati)|Conteggio|Conteggio|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, consentendo di enumerare le raccolte, i database e così via e le relative configurazioni gratuitamente.|DatabaseName, CollectionName, Region, StatusCode, Role|
|MongoCollectionDelete|Raccolta Mongo eliminata|Conteggio|Conteggio|Raccolta Mongo eliminata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Velocità effettiva raccolta Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva raccolta Mongo aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Raccolta Mongo aggiornata|Conteggio|Conteggio|Raccolta Mongo aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Database Mongo aggiornato|Conteggio|Conteggio|Database Mongo aggiornato|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Database Mongo eliminato|Conteggio|Conteggio|Database Mongo eliminato|ResourceName, tipologia API, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Velocità effettiva del database Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva del database Mongo aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Mongo Request Charge (Addebito richiesta Mongo)|Conteggio|Totale|Unità richiesta Mongo utilizzate|DatabaseName, CollectionName, Region, CommandName, ErrorCode, stato|
|MongoRequests|Richieste Mongo|Conteggio|Conteggio|Numero di richieste Mongo eseguite|DatabaseName, CollectionName, Region, CommandName, ErrorCode, stato|
|MongoRequestsCount|Mongo Request Rate (Frequenza richieste Mongo)|Conteggio al secondo|Media|Numero di richieste Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo)|Conteggio al secondo|Media|Richiesta di eliminazione Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo)|Conteggio al secondo|Media|Numero di inserimento di Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo Query Request Rate (Frequenza richieste di query Mongo)|Conteggio al secondo|Media|Richieste di query Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsUpdate|Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo)|Conteggio al secondo|Media|Richiesta di aggiornamento Mongo al secondo|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|NormalizedRUConsumption|Consumo ur normalizzato|Percentuale|Massimo|Percentuale di utilizzo massimo delle UR al minuto|CollectionName, DatabaseName, Region|
|ProvisionedThroughput|Velocità effettiva sottoposta a provisioning|Conteggio|Massimo|Velocità effettiva sottoposta a provisioning|DatabaseName, CollectionName|
|RegionFailover|Area sottoposta a failover|Conteggio|Conteggio|Area sottoposta a failover|Nessuno|
|RemoveRegion|Area rimossa|Conteggio|Conteggio|Area rimossa|Region|
|ReplicationLatency|P99 Replication Latency (Latenza di replica P99)|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion,TargetRegion|
|ServerSideLatency|Latenza lato server|Millisecondi|Media|Latenza lato server|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Disponibilità del servizio|Percentuale|Media|L'account richiede disponibilità a una granularità di un'ora, giorno o mese|Nessuno|
|SqlContainerDelete|Contenitore SQL eliminato|Conteggio|Conteggio|Contenitore SQL eliminato|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|Velocità effettiva del contenitore SQL aggiornata|Conteggio|Conteggio|Velocità effettiva del contenitore SQL aggiornata|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Contenitore SQL aggiornato|Conteggio|Conteggio|Contenitore SQL aggiornato|ResourceName, ChildResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|Database SQL eliminato|Conteggio|Conteggio|Database SQL eliminato|ResourceName, tipologia API, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|Velocità effettiva del database SQL aggiornata|Conteggio|Conteggio|Velocità effettiva del database SQL aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|Database SQL aggiornato|Conteggio|Conteggio|Database SQL aggiornato|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|Tabella AzureTable eliminata|Conteggio|Conteggio|Tabella AzureTable eliminata|ResourceName, tipologia API, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|Velocità effettiva della tabella AzureTable aggiornata|Conteggio|Conteggio|Velocità effettiva della tabella AzureTable aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Tabella AzureTable aggiornata|Conteggio|Conteggio|Tabella AzureTable aggiornata|ResourceName, tipologia API, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, Region, StatusCode, OperationType, stato|
|TotalRequests|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|DatabaseName, CollectionName, Region, StatusCode, OperationType, stato|
|UpdateAccountKeys|Chiavi dell'account aggiornate|Conteggio|Conteggio|Chiavi dell'account aggiornate|KeyType|
|UpdateAccountNetworkSettings|Impostazioni di rete account aggiornate|Conteggio|Conteggio|Impostazioni di rete account aggiornate|Nessuno|
|UpdateAccountReplicationSettings|Impostazioni di replica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di replica dell'account aggiornate|Nessuno|
|UpdateDiagnosticsSettings|Impostazioni di diagnostica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di diagnostica dell'account aggiornate|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TransactionCount|Numero transazioni|Conteggio|Conteggio|Conteggio totale transazioni|TransactionCount|
|SuccessCount|Conteggio operazioni riuscite|Conteggio|Conteggio|Conteggio transazioni riuscite|SuccessCount|
|FailureCount|Conteggio errori|Conteggio|Conteggio|Conteggio transazioni non riuscite|FailureCount|
|SuccessLatency|Success Latency (Latenza operazioni riuscite)|Millisecondi|Media|Latenza delle transazioni riuscite|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishFailCount|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessLatencyInMs|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuno|
|PublishFailCount|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuno|
|PublishSuccessLatencyInMs|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuno|
|PublishFailCount|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuno|
|PublishSuccessLatencyInMs|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuno|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuno|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuno|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuno|
|PublishFailCount|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuno|
|PublishSuccessLatencyInMs|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuno|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|Dimensione|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (deprecato)|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (deprecato)|Nessuno|
|SUCCREQ|Richieste completate correttamente (deprecato)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|FAILREQ|Richieste non riuscite (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|SVRBSY|Errori server occupato (deprecato)|Conteggio|Totale|Totale errori server occupati per uno spazio dei nomi (deprecato)|Nessuno|
|INTERR|Errori interni del server (deprecato)|Conteggio|Totale|Totale errori interni del server per uno spazio dei nomi (deprecato)|Nessuno|
|MISCERR|Altri errori (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuno|
|INMSGS|Incoming Messages (obsolete) (Deprecated) (Messaggi in ingresso (obsoleto) (deprecato))|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in ingresso (deprecata)|Nessuno|
|EHINMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi (deprecato)|Nessuno|
|OUTMSGS|Outgoing Messages (obsolete) (Deprecated) (Messaggi in uscita (obsoleto) (deprecato))|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in uscita (deprecata)|Nessuno|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecato)|Nessuno|
|EHINMBS|Byte in ingresso (obsoleto) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in ingresso (deprecata)|Nessuno|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva del messaggio in ingresso dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuno|
|EHOUTMBS|Outgoing bytes (obsolete) (Deprecated) (Byte in uscita (obsoleto) (deprecato))|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in uscita (deprecata)|Nessuno|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva del messaggio in uscita dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuno|
|EHABL|Archiviazione backlog messaggi (deprecato)|Conteggio|Totale|Messaggi di archiviazione dell'hub eventi nel backlog per uno spazio dei nomi (deprecato)|Nessuno|
|EHAMSGS|Archiviazione messaggi (deprecato)|Conteggio|Totale|Messaggi archiviati dell'hub eventi in uno spazio dei nomi (deprecato)|Nessuno|
|EHAMBS|Velocità effettiva archiviazione messaggi (deprecato)|Byte|Totale|Velocità effettiva del messaggio archiviato nell'hub eventi in uno spazio dei nomi (deprecato)|Nessuno|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|Dimensione|Dimensioni di un hub eventi in byte.|Byte|Media|Dimensioni di un hub eventi in byte.|Ruolo|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Number of Active Workers (Numero di ruoli di lavoro)|Conteggio|Massimo|Number of Active Workers (Numero di ruoli di lavoro)|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|Nessuno|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult/Name, availabilityResult/location|
|availabilityResults/count|Test della disponibilità|Conteggio|Conteggio|Conteggio dei test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|availabilityResults/duration|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/Name, availabilityResult/location, availabilityResult/Success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|Nessuno|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|Nessuno|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|Nessuno|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|Nessuno|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|Nessuno|
|dependencies/count|Chiamate di dipendenza|Conteggio|Conteggio|Numero delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Errori di chiamate di dipendenza|Conteggio|Conteggio|Numero di chiamate di dipendenza non riuscite eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
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
|requests/rate|Frequenza richieste server|Conteggio al secondo|Media|Frequenza delle richieste server al secondo|Request/performanceBucket, Request/resultCode, Operation/Synthetic, cloud/roleInstance, Request/Success, cloud/roleName|
|exceptions/count|Eccezioni|Conteggio|Conteggio|Numero combinato di tutte le eccezioni non rilevate.|Cloud/roleName, cloud/roleInstance, client/tipo|
|exceptions/browser|Eccezioni del browser|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nel browser.|client/server, cloud/roleName|
|exceptions/server|Eccezioni del server|Conteggio|Conteggio|Numero delle eccezioni non rilevate generate nell'applicazione server.|client/server, cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Conteggio|Conteggio|Numero documenti di analisi|Trace/severityLevel, Operation/Synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|connectedDeviceCount|Totale dispositivi connessi|Conteggio|Media|Numero di dispositivi connessi a IoT Central|Nessuno|
|C2D. Property. Read. Success|Letture della proprietà del dispositivo riuscite da IoT Central|Conteggio|Totale|Numero di tutte le letture di proprietà riuscite avviate da IoT Central|Nessuno|
|C2D. Property. Read. Failure|Letture della proprietà del dispositivo non riuscite da IoT Central|Conteggio|Totale|Numero di tutte le letture di proprietà non riuscite avviate da IoT Central|Nessuno|
|D2C. Property. Read. Success|Letture della proprietà del dispositivo riuscite dai dispositivi|Conteggio|Totale|Numero di tutte le letture di proprietà riuscite avviate dai dispositivi|Nessuno|
|D2C. Property. Read. Failure|Letture della proprietà del dispositivo non riuscite dai dispositivi|Conteggio|Totale|Numero di tutte le letture di proprietà non riuscite avviate dai dispositivi|Nessuno|
|C2D. Property. Update. Success|Aggiornamenti della proprietà del dispositivo riusciti da IoT Central|Conteggio|Totale|Numero di tutti gli aggiornamenti di proprietà riuscite avviati dal IoT Central|Nessuno|
|C2D. Property. Update. Failure|Aggiornamenti della proprietà del dispositivo non riusciti da IoT Central|Conteggio|Totale|Numero di tutti gli aggiornamenti di proprietà non riuscite avviati dalla IoT Central|Nessuno|
|D2C. Property. Update. Success|Aggiornamenti della proprietà del dispositivo riusciti dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti della proprietà riusciti avviati dai dispositivi|Nessuno|
|D2C. Property. Update. Failure|Aggiornamenti della proprietà del dispositivo non riusciti dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti delle proprietà non riuscite avviati dai dispositivi|Nessuno|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Totale dei risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturazione complessiva dell'insieme di credenziali|Percentuale|Media|Capacità dell'insieme di credenziali utilizzata|ActivityType, ActivityName, TransactionType|
|Disponibilità|Disponibilità complessiva dell'insieme di credenziali|Percentuale|Media|Disponibilità richieste di archiviazione|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CacheUtilization|Utilizzo cache|Percentuale|Media|Livello di uso nell'ambito del cluster|Nessuno|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query in secondi|QueryStatus|
|IngestionUtilization|Utilizzo inserimento|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|Nessuno|
|KeepAlive|Keep alive|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|Nessuno|
|IngestionVolumeInMB|Ingestion volume (in MB) (Volume inserimento (in MB))|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Database|
|IngestionLatencyInSeconds|Latenza inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|Nessuno|
|EventsProcessedForEventHubs|Events processed (for Event/IoT Hubs) (Eventi elaborati (per hub eventi/IoT))|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi/tutto|EventStatus|
|IngestionResult|Risultati inserimento|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|CPU|CPU|Percentuale|Media|Livello di uso della CPU|Nessuno|
|ContinuousExportNumOfRecordsExported|Esportazione continua: numero di record esportati|Conteggio|Totale|Numero di record esportati, generati per ogni artefatto di archiviazione scritto durante l'operazione di esportazione|ContinuousExportName, database|
|ExportUtilization|Export Utilization (Utilizzo esportazione)|Percentuale|Massimo|Esporta utilizzo|Nessuno|
|ContinuousExportPendingCount|Continuous Export Pending Count (Esportazione continua - Conteggio in sospeso)|Conteggio|Massimo|Numero di processi di esportazione continua in sospeso pronti per l'esecuzione|Nessuno|
|ContinuousExportMaxLatenessMinutes|Latenza massima di esportazione continua|Conteggio|Massimo|Ritardo (in minuti) segnalato dai processi di esportazione continua nel cluster|Nessuno|
|ContinuousExportResult|Continuous Export Result (Risultato esportazione continua)|Conteggio|Conteggio|Indica se l'esportazione continua ha avuto esito positivo o negativo|ContinuousExportName, result, database|
|StreamingIngestDuration|Streaming Ingest Duration (Durata inserimento streaming)|Millisecondi|Media|Durata di inserimento del flusso in millisecondi|Nessuno|
|StreamingIngestDataRate|Streaming Ingest Data Rate (Velocità dati inserimento streaming)|Conteggio|Media|Velocità dati di inserimento dei flussi (MB al secondo)|Nessuno|
|SteamingIngestRequestRate|Streaming Ingest Request Rate (Frequenza richieste inserimento streaming)|Conteggio|RateRequestsPerSecond|Frequenza delle richieste di inserimento in streaming (richieste al secondo)|Nessuno|
|StreamingIngestResults|Streaming Ingest Result (Risultato inserimento streaming)|Conteggio|Media|Risultato inserimento flusso|Risultato|
|TotalNumberOfConcurrentQueries|Numero totale di query simultanee|Conteggio|Totale|Numero totale di query simultanee|Nessuno|
|TotalNumberOfThrottledQueries|Numero totale di query limitate|Conteggio|Totale|Numero totale di query limitate|Nessuno|
|TotalNumberOfThrottledCommands|Numero totale di comandi limitati|Conteggio|Totale|Numero totale di comandi limitati|CommandType|
|TotalNumberOfExtents|Numero totale di extent|Conteggio|Totale|Numero totale di extent dei dati|Nessuno|
|InstanceCount|Conteggio istanze|Conteggio|Media|Numero totale di istanze|Nessuno|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuno|
|RunStartThrottledEvents|Eventi di avvio esecuzione limitati|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuno|
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

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuno|
|RunStartThrottledEvents|Eventi di avvio esecuzione limitati|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuno|
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
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente di Integration Services.|Nessuno|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente di Integration Services.|Nessuno|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|Nessuno|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|Nessuno|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Esecuzioni annullate|Esecuzioni annullate|Conteggio|Totale|Numero di esecuzioni annullate per questa area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Annulla esecuzioni richieste|Annulla esecuzioni richieste|Conteggio|Totale|Numero di esecuzioni in cui è stato richiesto l'annullamento per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni completate|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni completate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Finalizzazione delle esecuzioni|Finalizzazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni immesse per lo stato di completamento per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non rispondenti|Esecuzioni non rispondenti|Conteggio|Totale|Numero di esecuzioni non rispondenti per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non avviate|Esecuzioni non avviate|Conteggio|Totale|Numero di esecuzioni in stato non avviato per questa area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Preparazione delle esecuzioni|Preparazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni preparate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni del provisioning|Esecuzioni del provisioning|Conteggio|Totale|Numero di esecuzioni di cui viene eseguito il provisioning per questa area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni in coda|Esecuzioni in coda|Conteggio|Totale|Numero di esecuzioni accodate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni avviate|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni avviate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Avvio delle esecuzioni|Avvio delle esecuzioni|Conteggio|Totale|Numero di esecuzioni avviate per l'area di lavoro|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Errori|Errors|Conteggio|Totale|Numero di errori di esecuzione in questa area di lavoro|Scenario|
|Avvisi|Avvisi|Conteggio|Totale|Numero di avvisi di esecuzione in questa area di lavoro|Scenario|
|Model Register Succeeded (Registrazione di modelli riuscita)|Model Register Succeeded (Registrazione di modelli riuscita)|Conteggio|Totale|Numero di registrazioni del modello riuscite in questa area di lavoro|Scenario|
|Model Register Failed (Registrazione di modelli non riuscita)|Model Register Failed (Registrazione di modelli non riuscita)|Conteggio|Totale|Numero di registrazioni del modello non riuscite in questa area di lavoro|Scenario, StatusCode|
|Model Deploy Started (Distribuzione di modelli avviata)|Model Deploy Started (Distribuzione di modelli avviata)|Conteggio|Totale|Numero di distribuzioni di modelli avviate in questa area di lavoro|Scenario|
|Model Deploy Succeeded (Distribuzione di modelli riuscita)|Model Deploy Succeeded (Distribuzione di modelli riuscita)|Conteggio|Totale|Numero di distribuzioni di modelli riuscite in questa area di lavoro|Scenario|
|Model Deploy Failed (Distribuzione di modelli non riuscita)|Model Deploy Failed (Distribuzione di modelli non riuscita)|Conteggio|Totale|Numero di distribuzioni di modelli non riuscite in questa area di lavoro|Scenario, StatusCode|
|Totale nodi|Totale nodi|Conteggio|Media|Numero totale di nodi. Il totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi Premepted, lasciando nodi|Scenario, clustername|
|Active Nodes (Nodi attivi)|Active Nodes (Nodi attivi)|Conteggio|Media|Numero di nodi Active. Questi sono i nodi che eseguono attivamente un processo.|Scenario, clustername|
|Idle Nodes (Nodi inattivi)|Idle Nodes (Nodi inattivi)|Conteggio|Media|Numero di nodi inattivi. I nodi inattivi sono i nodi che non eseguono alcun processo, ma possono accettare un nuovo processo, se disponibile.|Scenario, clustername|
|Unusable Nodes (Nodi non utilizzabili)|Unusable Nodes (Nodi non utilizzabili)|Conteggio|Media|Numero di nodi inutilizzabili. I nodi inutilizzabili non sono funzionali a causa di un problema irrisolvibile. Azure ricicla questi nodi.|Scenario, clustername|
|Preempted Nodes (Nodi superati)|Preempted Nodes (Nodi superati)|Conteggio|Media|Numero di nodi con precedenza. Questi nodi sono i nodi con priorità bassa che vengono sottratti dal pool di nodi disponibile.|Scenario, clustername|
|Leaving Nodes (Nodi di uscita)|Leaving Nodes (Nodi di uscita)|Conteggio|Media|Numero di nodi in uscita. Lasciando i nodi sono i nodi che hanno appena terminato l'elaborazione di un processo e passano allo stato inattivo.|Scenario, clustername|
|Core totali|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Active Cores (Core attivi)|Active Cores (Core attivi)|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Idle Cores (Core inattivi)|Idle Cores (Core inattivi)|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Unusable Cores (Core non utilizzabili)|Unusable Cores (Core non utilizzabili)|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Preempted Cores (Core superati)|Preempted Cores (Core superati)|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Leaving Cores (Core di uscita)|Leaving Cores (Core di uscita)|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Quota Utilization Percentage (Percentuale di utilizzo quota)|Quota Utilization Percentage (Percentuale di utilizzo quota)|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Conteggio|Media|CPU (anteprima)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Conteggio|Media|GPU (anteprima)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Uso|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilità|Disponibilità|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte.|OutputFormat|
|SuccessE2ELatency|Success end to end Latency (Latenza end-to-end riuscita)|Millisecondi|Media|Latenza media per le richieste con esito positivo in millisecondi.|OutputFormat|
|Requests|Requests|Conteggio|Totale|Richieste a un endpoint di streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetQuota|Asset quota (Quota asset)|Conteggio|Media|Numero di asset consentiti per l'account del servizio multimediale corrente|Nessuno|
|AssetCount|Asset count (Conteggio asset)|Conteggio|Media|Numero di asset già creati nell'account del servizio multimediale corrente|Nessuno|
|AssetQuotaUsedPercentage|Asset quota used percentage (Percentuale usata quota asset)|Percentuale|Media|Percentuale di risorse usate nell'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyQuota|Content Key Policy quota (Quota criteri chiave simmetrica)|Conteggio|Media|Numero di criteri di chiave simmetrica consentiti per l'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyCount|Content Key Policy count (Conteggio criteri chiave simmetrica)|Conteggio|Media|Quanti criteri chiave simmetrica sono già stati creati nell'account del servizio multimediale corrente|Nessuno|
|ContentKeyPolicyQuotaUsedPercentage|Content Key Policy quota used percentage (Percentuale usata quota criteri chiave simmetrica)|Percentuale|Media|Percentuale di utilizzo del criterio della chiave simmetrica nell'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyQuota|Streaming Policy quota (Quota criteri di streaming)|Conteggio|Media|Quanti criteri di streaming sono consentiti per l'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyCount|Streaming Policy count (Conteggio criteri di streaming)|Conteggio|Media|Quanti criteri di streaming sono già stati creati nell'account del servizio multimediale corrente|Nessuno|
|StreamingPolicyQuotaUsedPercentage|Streaming Policy quota used percentage (Percentuale usata quota criteri di streaming)|Percentuale|Media|Percentuale di utilizzo dei criteri di flusso nell'account del servizio multimediale corrente|Nessuno|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetsConverted|Asset convertiti|Conteggio|Totale|Numero totale di asset convertiti|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Sessioni di rendering attive|Conteggio|Totale|Numero totale di sessioni di rendering attive|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AverageReadLatency|Latenza media in lettura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di lettura|Nessuno|
|AverageWriteLatency|Latenza media in scrittura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di scrittura|Nessuno|
|VolumeLogicalSize|Dimensioni utilizzate del volume|Byte|Media|Dimensioni logiche del volume (byte usati)|Nessuno|
|VolumeSnapshotSize|Dimensioni dello snapshot del volume|Byte|Media|Dimensioni di tutti gli snapshot nel volume|Nessuno|
|ReadIops|Operazioni di I/O in lettura|Conteggio al secondo|Media|Operazioni di I/O in lettura al secondo|Nessuno|
|WriteIops|Operazioni di I/O in scrittura|Conteggio al secondo|Media|Operazioni di I/O in scrittura al secondo|Nessuno|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool allocato alle dimensioni del volume|Byte|Media|Dimensioni usate del pool|Nessuno|
|VolumePoolTotalLogicalSize|Dimensioni utilizzate pool|Byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|Nessuno|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Byte|Totale|Numero di byte inviati dall'interfaccia di rete|Nessuno|
|BytesReceivedRate|Byte ricevuti|Byte|Totale|Numero di byte ricevuti dall'interfaccia di rete|Nessuno|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|Nessuno|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|Nessuno|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query per zona DNS|Nessuno|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS|Nessuno|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità di set di record usata da una zona DNS|Nessuno|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Round trip time for Pings to a VM (Tempo di round trip per ping a una macchina virtuale)|Millisecondi|Media|Tempo di round trip per i ping inviati a una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Failed Pings to a VM (Ping non riusciti a una macchina virtuale)|Percentuale|Media|Percentuale del numero di ping non riusciti in totale ping inviati di una macchina virtuale di destinazione|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count (Numero di passaggi regole applicazione)|Conteggio|Totale|Numero di volte in cui sono state raggiunte le regole applicazione|Stato, motivo, protocollo|
|NetworkRuleHit|Network rules hit count (Numero di passaggi regole di rete)|Conteggio|Totale|Numero di volte in cui sono state eseguite le regole di rete|Stato, motivo, protocollo|
|FirewallHealth|Firewall health state (Stato di integrità firewall)|Percentuale|Media|Firewall health state (Stato di integrità firewall)|Stato, motivo|
|DataProcessed|Dati elaborati|Byte|Totale|Quantità totale di dati elaborati dal firewall|Nessuno|
|SNATPortUtilization|SNAT port utilization (Utilizzo porte SNAT)|Percentuale|Media|SNAT port utilization (Utilizzo porte SNAT)|Nessuno|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuno|
|UnhealthyHostCount|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Totale richieste|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Requests per minute per Healthy Host (Richieste al minuto per host integro)|Conteggio|Media|Numero medio di richieste al minuto per host back-end integro in un pool|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Current Connections (Connessioni correnti)|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|Nessuno|
|NewConnectionsPerSecond|Nuove connessioni al secondo|Conteggio al secondo|Media|Nuove connessioni al secondo stabilite con il gateway applicazione|Nessuno|
|CpuUtilization|Utilizzo CPU|Percentuale|Media|Utilizzo corrente della CPU del gateway applicazione|Nessuno|
|CapacityUnits|Current Capacity Units (Unità di capacità correnti)|Conteggio|Media|Unità di capacità utilizzate|Nessuno|
|FixedBillableCapacityUnits|Unità di capacità fatturabili fisse|Conteggio|Media|Unità di capacità minima che verranno addebitate|Nessuno|
|EstimatedBilledCapacityUnits|Unità di capacità fatturate stimate|Conteggio|Media|Unità di capacità stimate che verranno addebitate|Nessuno|
|ComputeUnits|Current Compute Units (Unità di calcolo correnti)|Conteggio|Media|Unità di calcolo utilizzate|Nessuno|
|BackendResponseStatus|Backend Response Status (Stato risposta back-end)|Conteggio|Totale|Numero di codici di risposta HTTP generati dai membri back-end. Non sono inclusi i codici di risposta generati dal gateway applicazione.|BackendServer, integri, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Client TLS Protocol (Protocollo TLS client)|Conteggio|Totale|Numero di richieste TLS e non TLS avviate dal client che hanno stabilito la connessione al gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base al protocollo TLS della dimensione.|Listener, TlsProtocol|
|BytesSent|Byte inviati|Byte|Totale|Numero totale di byte inviati dal gateway applicazione ai client|Listener|
|BytesReceived|Byte ricevuti|Byte|Totale|Numero totale di byte ricevuti dal gateway applicazione dai client|Listener|
|ClientRtt|Client RTT (Scrittura in tempo reale client)|Millisecondi|Media|Tempo medio round trip tra i client e il gateway applicazione. Questa metrica indica il tempo necessario per stabilire le connessioni e restituire i riconoscimenti|Listener|
|ApplicationGatewayTotalTime|Application Gateway Total Time (Tempo totale gateway applicazione)|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta e la relativa risposta da inviare. Viene calcolato come media dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al momento in cui termina l'operazione di invio della risposta. È importante tenere presente che questo in genere include il tempo di elaborazione del gateway applicazione, il tempo in cui i pacchetti di richiesta e risposta vengono spostati in rete e il tempo impiegato dal server back-end per rispondere.|Listener|
|BackendConnectTime|Backend Connect Time (Tempo di connessione back-end)|Millisecondi|Media|Tempo impiegato per stabilire una connessione con un server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendFirstByteResponseTime|Backend First Byte Response Time (Tempo di risposta primo byte back-end)|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta, il tempo di elaborazione approssimativo del server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendLastByteResponseTime|Backend Last Byte Response Time (Tempo di risposta ultimo byte back-end)|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta|Listener, BackendServer, integri, BackendHttpSetting|
|MatchedCount|Distribuzione della regola totale del firewall applicazione Web V1|Conteggio|Totale|Distribuzione della regola totale del firewall applicazione Web V1 per il traffico in ingresso|RuleGroup, RuleId|
|BlockedCount|Distribuzione della regola richieste bloccate del firewall applicazione Web V1|Conteggio|Totale|Distribuzione della regola richieste bloccate del firewall applicazione Web V1|RuleGroup, RuleId|
|BlockedReqCount|Numero di richieste bloccate V1 del firewall applicazione Web|Conteggio|Totale|Numero di richieste bloccate V1 del firewall applicazione Web|Nessuno|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Conteggio|Media|Livello di luce RX in dBm|Collegamento, corsia|
|TxLightLevel|TxLightLevel|Conteggio|Media|Livello di luce TX in dBm|Collegamento, corsia|
|AdminState|AdminState|Conteggio|Media|Stato amministratore della porta|Collegamento|
|LineProtocol|LineProtocol|Conteggio|Media|Stato del protocollo di riga della porta|Collegamento|
|PortBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Collegamento|
|PortBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Collegamento|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Peering|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Peering|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeredCircuitSKey|
|BgpAvailability|Bgp Availability (Disponibilità BGP)|Percentuale|Media|Disponibilità BGP da MSEE verso tutti i peer.|Peering, peer|
|ArpAvailability|Arp Availability (Disponibilità ARP)|Percentuale|Media|Disponibilità ARP da MSEE verso tutti i peer.|Peering, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|Conteggio al secondo|Media|Bit in ingresso dei dati eliminati al secondo|Nessuno|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|Conteggio al secondo|Media|Bit di dati in uscita eliminati al secondo|Nessuno|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuno|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuno|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QpsByEndpoint|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stato endpoint per endpoint|Conteggio|Massimo|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ProbesFailedPercent|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|Nessuno|
|AverageRoundtripMs|Avg. Tempo di round trip (ms)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|Nessuno|
|ChecksFailedPercent|Checks Failed Percent (Preview) (Percentuale controlli non superati (anteprima))|Percentuale|Media|% dei controlli di monitoraggio della connettività non riusciti|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Round-Trip Time (ms) (Preview) (Tempo di round trip (ms) (anteprima))|Millisecondi|Media|Tempo di round trip in millisecondi per i controlli di monitoraggio della connettività|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RequestCount|Conteggio richieste|Conteggio|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Billable Response Size (Dimensione risposta fatturabile)|Byte|Totale|Numero di byte fatturabili (2 KB minimo per richiesta) inviati come risposte dal proxy HTTP/S ai client.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Conteggio delle richieste del back-end|Conteggio|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latenza della richiesta del back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|TotalLatency|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentuale di integrità del back-end|Percentuale|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Back-end, integri|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, ruleName, azione|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query gestite per una zona DNS privato|Nessuno|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS privato|Nessuno|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità del set di record utilizzata da una zona DNS privato|Nessuno|
|VirtualNetworkLinkCount|Conteggio collegamenti rete virtuale|Conteggio|Massimo|Numero di reti virtuali collegate a una zona di DNS privato|Nessuno|
|VirtualNetworkLinkCapacityUtilization|Utilizzo capacità collegamento rete virtuale|Percentuale|Massimo|Percentuale della capacità del collegamento di rete virtuale utilizzata da una zona DNS privato|Nessuno|
|VirtualNetworkWithRegistrationLinkCount|Conteggio collegamenti registrazione rete virtuale|Conteggio|Massimo|Numero di reti virtuali collegate a una zona di DNS privato con registrazione automatica abilitata|Nessuno|
|VirtualNetworkWithRegistrationCapacityUtilization|Utilizzo della capacità del collegamento di registrazione della rete virtuale|Percentuale|Massimo|Percentuale di collegamento di rete virtuale con capacità di registrazione automatica utilizzata da una zona DNS privato|Nessuno|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Conteggio|Media|Average_Avg. Disk sec/Transfer|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
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
|Average_Processes|Processi|Conteggio|Media|Average_Processes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Conteggio|Media|Average_Uptime|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Users|Utenti|Conteggio|Media|Average_Users|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Lunghezza corrente coda del disco|Conteggio|Media|Average_Current Disk Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Available MBytes|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Average_Processor Queue Length|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer, ObjectName, NomeIstanza, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Conteggio|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aggiorna|Aggiornamento|Conteggio|Media|Aggiorna|Computer, prodotto, classificazione, UpdateState, facoltativo, approvato|
|Evento|Event|Conteggio|Media|Evento|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, EventId|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PrefixLatency|Latenza prefisso|Millisecondi|Media|Latenza mediana del prefisso|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilità sessione V4|Percentuale|Media|Disponibilità della sessione V4|ConnectionId|
|SessionAvailabilityV6|Disponibilità della sessione V6|Percentuale|Media|Disponibilità della sessione V6|ConnectionId|
|IngressTrafficRate|Velocità di traffico in ingresso|BitsPerSecond|Media|Velocità di traffico in ingresso in bit al secondo|ConnectionId|
|EgressTrafficRate|Frequenza traffico in uscita|BitsPerSecond|Media|Velocità di traffico in uscita in bit al secondo|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuzione di asset per classificazione|Conteggio|Totale|Indica il numero di asset a cui è assegnata una determinata classificazione, ovvero sono classificati con tale etichetta.|Classificazione, origine, ResourceId|
|AssetDistributionByStorageType|Distribuzione di asset per tipo di archiviazione|Conteggio|Totale|Indica il numero di asset con un determinato tipo di archiviazione.|StorageType, ResourceId|
|CatalogActiveUsers|Utenti attivi giornalieri|Conteggio|Totale|Numero di utenti attivi giornaliero|ResourceId|
|CatalogUsage|Distribuzione utilizzo per operazione|Conteggio|Totale|Indica il numero di operazioni apportate dall'utente al catalogo, ad esempio l'accesso, la ricerca e il glossario.|Operazione, ResourceId|
|NumberOfAssetsWithClassifications|Numero di asset con almeno una classificazione|Conteggio|Media|Indica il numero di asset con almeno una classificazione dei tag.|ResourceId|
|ScanCancelled|Analisi annullata|Conteggio|Totale|Indica il numero di analisi annullate.|ResourceId|
|ScanCompleted|Analisi completata|Conteggio|Totale|Indica il numero di analisi completate correttamente.|ResourceId|
|ScanFailed|Analisi non riuscita|Conteggio|Totale|Indica il numero di analisi non riuscite.|ResourceId|
|ScanTimeTaken|Tempo di analisi impiegato|Secondi|Totale|Indica il tempo di analisi totale in secondi.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuno|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuno|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuno|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|Dimensione|Dimensione|Byte|Media|Dimensioni di una coda o di un argomento in byte.|EntityName|
|Messaggi|Numero di messaggi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi contenuti in una coda o in un argomento.|EntityName|
|ActiveMessages|Numero di messaggi attivi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi attivi contenuti in una coda o in un argomento.|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda o in un argomento.|Conteggio|Media|Numero di messaggi non recapitabili in una coda o in un argomento.|EntityName|
|ScheduledMessages|Numero di messaggi pianificati in una coda o in un argomento.|Conteggio|Media|Numero di messaggi pianificati in una coda o in un argomento.|EntityName|
|NamespaceCpuUsage|CPU|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio.|Replica|
|NamespaceMemoryUsage|Utilizzo della memoria|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio.|Replica|
|CPUXNS|CPU (deprecato)|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio. Questa metrica è depricated. Usare invece la metrica CPU (NamespaceCpuUsage).|Replica|
|WSXNS|Utilizzo memoria (deprecato)|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio. Questa metrica è deprecata. Usare invece la metrica utilizzo memoria (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Conteggio|Massimo|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|Nessuno|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|Nessuno|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|Nessuno|
|UserErrors|Errori utente|Percentuale|Massimo|Percentuale di errori utente|Nessuno|
|SystemErrors|Errori di sistema|Percentuale|Massimo|Percentuale di errori di sistema|Nessuno|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuno|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale di i/o del log. Non applicabile ai data warehouse.|Nessuno|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai database basati su DTU.|Nessuno|
|storage|Spazio dati usato|Byte|Massimo|Spazio dati utilizzato. Non applicabile ai data warehouse.|Nessuno|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuno|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuno|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|Nessuno|
|deadlock|Deadlock|Conteggio|Totale|Deadlock. Non applicabile ai data warehouse.|Nessuno|
|storage_percent|Data space used percent (Percentuale usata spazio dati)|Percentuale|Massimo|Percentuale di utilizzo dello spazio dei dati. Non applicabile a data warehouse o database con iperscalabilità.|Nessuno|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria. Non applicabile ai data warehouse.|Nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro. Non applicabile ai data warehouse.|Nessuno|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni. Non applicabile ai data warehouse.|Nessuno|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU. Si applica ai database basati su DTU.|Nessuno|
|dtu_used|Uso DTU|Conteggio|Media|DTU usato. Si applica ai database basati su DTU.|Nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai database basati su vCore.|Nessuno|
|cpu_used|CPU used (CPU usata)|Conteggio|Media|CPU utilizzata. Si applica ai database basati su vCore.|Nessuno|
|dwu_limit|Limite DWU|Conteggio|Massimo|Limite DWU. Si applica solo ai data warehouse.|Nessuno|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massimo|Percentuale di DWU. Si applica solo ai data warehouse.|Nessuno|
|dwu_used|Uso DWU|Conteggio|Massimo|DWU usato. Si applica solo ai data warehouse.|Nessuno|
|cache_hit_percent|Percentuale dei riscontri nella cache|Percentuale|Massimo|Percentuale riscontri cache. Si applica solo ai data warehouse.|Nessuno|
|cache_used_percent|Percentuale della cache utilizzata|Percentuale|Massimo|Percentuale di utilizzo della cache. Si applica solo ai data warehouse.|Nessuno|
|sqlserver_process_core_percent<sup>1</sup> |Percentuale Core processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della CPU per il processo di SQL Server, misurata dal sistema operativo.|Nessuno|
|sqlserver_process_memory_percent<sup>1</sup> |Percentuale memoria processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della memoria per il processo di SQL Server, misurato dal sistema operativo.|Nessuno|
|tempdb_data_size<sup>1</sup> |Tempdb Data File Size Kilobytes (Dimensioni file dati tempdb in kilobyte)|Conteggio|Massimo|Dimensioni del file di dati tempdb.|Nessuno|
|tempdb_log_size<sup>1</sup> |Tempdb Log File Size Kilobytes (Dimensioni file di log tempdb in kilobyte)|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb.|Nessuno|
|tempdb_log_used_percent<sup>1</sup> |Tempdb Percent Log Used (Percentuale log utilizzata tempdb)|Percentuale|Massimo|Log percentuale tempdb utilizzato.|Nessuno|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|Nessuno|
|app_cpu_billed|CPU dell'app fatturata|Conteggio|Totale|CPU app fatturata. Si applica ai database senza server.|Nessuno|
|app_cpu_percent|App CPU percentage (Percentuale CPU app)|Percentuale|Media|Percentuale CPU app. Si applica ai database senza server.|Nessuno|
|app_memory_percent|App memory percentage (Percentuale memoria app)|Percentuale|Media|Percentuale di memoria dell'app. Si applica ai database senza server.|Nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Archiviazione dati allocata. Non applicabile ai data warehouse.|Nessuno|
|memory_usage_percent|Percentuale di memoria|Percentuale|Massimo|Percentuale di memoria. Si applica solo ai data warehouse.|Nessuno|
|dw_backup_size_gb|Dimensioni di archiviazione dei dati|Conteggio|Totale|Le dimensioni di archiviazione dei dati sono costituite dalle dimensioni dei dati e dal log delle transazioni. La metrica viene conteggiata in base alla parte "archiviazione" della fattura. Si applica solo ai data warehouse.|Nessuno|
|dw_snapshot_size_gb|Dimensioni di archiviazione snapshot|Conteggio|Totale|Dimensioni di archiviazione snapshot è la dimensione delle modifiche incrementali acquisite dagli snapshot per creare punti di ripristino automatici e definiti dall'utente. La metrica viene conteggiata in base alla parte "archiviazione" della fattura. Si applica solo ai data warehouse.|Nessuno|
|dw_geosnapshot_size_gb|Dimensioni di archiviazione per il ripristino di emergenza|Conteggio|Totale|Le dimensioni di archiviazione per il ripristino di emergenza vengono riflesse in "archiviazione ripristino di emergenza" nella fattura. Si applica solo ai data warehouse.|Nessuno|
|wlg_allocation_relative_to_system_percent|Allocazione del gruppo di carico di lavoro per percentuale di sistema|Percentuale|Massimo|Percentuale allocata di risorse rispetto all'intero sistema per gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Allocazione gruppo del carico di lavoro per percentuale risorse Cap|Percentuale|Massimo|Percentuale allocata di risorse rispetto alle risorse CAP specificate per gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries|Query attive del gruppo di carico di lavoro|Conteggio|Totale|Query attive all'interno del gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Query accodate del gruppo di carico di lavoro|Conteggio|Totale|Query in coda all'interno del gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|active_queries|Query attive|Conteggio|Totale|Query attive in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|Nessuno|
|queued_queries|Query in coda|Conteggio|Totale|Query in coda in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|Nessuno|
|wlg_active_queries_timeouts|Timeout query del gruppo di carico di lavoro|Conteggio|Totale|Query che hanno raggiunto il timeout per il gruppo del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Percentuale risorse minima effettiva|Percentuale|Massimo|Percentuale minima di risorse riservate e isolate per il gruppo del carico di lavoro, prendendo in considerazione il valore minimo del livello di servizio. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Percentuale limite di risorse effettiva|Percentuale|Massimo|Limite rigido della percentuale di risorse consentite per il gruppo del carico di lavoro, prendendo in considerazione la percentuale di risorse minime effettiva allocata per altri gruppi del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|full_backup_size_bytes|Dimensioni di archiviazione del backup completo|Byte|Massimo|Dimensioni di archiviazione dei backup completi cumulativi. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuno|
|diff_backup_size_bytes|Dimensioni di archiviazione del backup differenziale|Byte|Massimo|Dimensioni di archiviazione del backup differenziale cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuno|
|log_backup_size_bytes|Dimensioni di archiviazione dei backup del log|Byte|Massimo|Dimensioni di archiviazione dei backup del log cumulative. Si applica ai database basati su vCore e con iperscalabilità.|Nessuno|
|snapshot_backup_size_bytes|Dimensioni di archiviazione del backup di snapshot|Byte|Massimo|Dimensioni di archiviazione dei backup di snapshot cumulativi. Si applica ai database con iperscalabilità.|Nessuno|
|base_blob_size_bytes|Dimensioni di archiviazione BLOB di base|Byte|Massimo|Dimensioni di archiviazione BLOB di base. Si applica ai database con iperscalabilità.|Nessuno|

<sup>1</sup> questa metrica è disponibile per i database che usano il modello di acquisto vCore con 2 Vcore e versioni successive oppure 200 DTU e versioni successive per il modello di acquisto basato su DTU. 

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuno|
|database_cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuno|
|database_physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuno|
|database_log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai pool elastici basati su DTU.|Nessuno|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|storage_percent|Data space used percent (Percentuale usata spazio dati)|Percentuale|Media|Data space used percent (Percentuale usata spazio dati)|Nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuno|
|database_workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuno|
|database_sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Conteggio|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|Nessuno|
|storage_limit|Dimensioni massime dati|Byte|Media|Dimensioni massime dati|Nessuno|
|eDTU_used|Uso eDTU|Conteggio|Media|eDTU usato. Si applica ai pool elastici basati su DTU.|Nessuno|
|database_eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|DatabaseResourceId|
|storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|Nessuno|
|database_storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|DatabaseResourceId|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai pool elastici basati su vCore.|Nessuno|
|database_cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|DatabaseResourceId|
|cpu_used|CPU used (CPU usata)|Conteggio|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|Nessuno|
|database_cpu_used|CPU used (CPU usata)|Conteggio|Media|CPU used (CPU usata)|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Percentuale Core processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della CPU per il processo di SQL Server, misurata dal sistema operativo. Si applica ai pool elastici. |Nessuno|
|sqlserver_process_memory_percent<sup>1</sup>|Percentuale memoria processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della memoria per il processo di SQL Server, misurato dal sistema operativo. Si applica ai pool elastici. |Nessuno|
|tempdb_data_size<sup>1</sup>|Tempdb Data File Size Kilobytes (Dimensioni file dati tempdb in kilobyte)|Conteggio|Massimo|Dimensioni del file di dati tempdb.|Nessuno|
|tempdb_log_size<sup>1</sup>|Tempdb Log File Size Kilobytes (Dimensioni file di log tempdb in kilobyte)|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb. |Nessuno|
|tempdb_log_used_percent<sup>1</sup>|Tempdb Percent Log Used (Percentuale log utilizzata tempdb)|Percentuale|Massimo|Log percentuale tempdb utilizzato.|Nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuno|
|database_allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|DatabaseResourceId|
|allocated_data_storage_percent|Data space allocated percent (Percentuale spazio dati allocato)|Percentuale|Massimo|Data space allocated percent (Percentuale spazio dati allocato)|Nessuno|

<sup>1</sup> questa metrica è disponibile per i database che usano il modello di acquisto vCore con 2 Vcore e versioni successive oppure 200 DTU e versioni successive per il modello di acquisto basato su DTU. 

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|virtual_core_count|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|Nessuno|
|avg_cpu_percent|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|Nessuno|
|reserved_storage_mb|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|Nessuno|
|storage_space_used_mb|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|Nessuno|
|io_requests|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|Nessuno|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|Nessuno|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|Nessuno|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di spazio di archiviazione utilizzata dal servizio BLOB dell'account di archiviazione in byte.|BlobType, livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Il numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Il numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuno|
|IndexCapacity|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di spazio di archiviazione utilizzata dal servizio tabelle dell'account di archiviazione in byte.|Nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Il numero di tabelle nel servizio tabelle dell'account di archiviazione.|Nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di spazio di archiviazione usata dal servizio file dell'account di archiviazione in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Il numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Il numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuno|
|FileShareSnapshotCount|Numero di snapshot in condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot in condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareCapacityQuota|Dimensioni quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'Servizio di accodamento dell'account di archiviazione in byte.|Nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Il numero di coda nel Servizio di accodamento dell'account di archiviazione.|Nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo di messaggi della coda nel Servizio di accodamento dell'account di archiviazione.|Nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|Geotype, ApiName, autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ClientIOPS|Totale operazioni di I/O al secondo client|Conteggio|Media|Frequenza delle operazioni dei file client elaborate dalla cache.|Nessuno|
|ClientLatency|Latenza client media|Millisecondi|Media|Latenza media delle operazioni dei file client nella cache di archiviazione.|Nessuno|
|ClientReadIOPS|Operazioni di I/O al secondo lette dal client|Conteggio al secondo|Media|Operazioni di lettura client al secondo.|Nessuno|
|ClientReadThroughput|Velocità effettiva media di lettura dalla cache|Byte al secondo|Media|Velocità di trasferimento dati in lettura client.|Nessuno|
|ClientWriteIOPS|Operazioni di I/O al secondo scritte dal client|Conteggio al secondo|Media|Operazioni di scrittura client al secondo.|Nessuno|
|ClientWriteThroughput|Velocità effettiva media di scrittura nella cache|Byte al secondo|Media|Velocità di trasferimento dati in scrittura client.|Nessuno|
|ClientMetadataReadIOPS|Operazioni di I/O al secondo di lettura dei metadati del client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le letture dei dati, che non modificano lo stato persistente.|Nessuno|
|ClientMetadataWriteIOPS|Operazioni di I/O al secondo di scrittura dei metadati del client|Conteggio al secondo|Media|Frequenza delle operazioni dei file client inviate alla cache, escluse le scritture dei dati, che modificano lo stato persistente.|Nessuno|
|ClientLockIOPS|Operazioni di I/O al secondo di blocco client|Conteggio al secondo|Media|Operazioni di blocco del file client al secondo.|Nessuno|
|StorageTargetHealth|Integrità destinazione di archiviazione|Conteggio|Media|Risultati booleani del test di connettività tra la cache e le destinazioni di archiviazione.|Nessuno|
|Uptime|Uptime|Conteggio|Media|Risultati booleani del test di connettività tra la cache e il sistema di monitoraggio.|Nessuno|
|StorageTargetIOPS|Totale operazioni di I/O al secondo destinazione di archiviazione|Conteggio|Media|Frequenza di tutte le operazioni sui file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Operazioni di I/O al secondo scritte nella destinazione di archiviazione|Conteggio|Media|Frequenza delle operazioni di scrittura di file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Velocità di scrittura asincrona nella destinazione di archiviazione|Byte al secondo|Media|Frequenza con cui la cache scrive in modo asincrono i dati in un particolare StorageTarget. Si tratta di scritture opportunistiche che non provocano il blocco dei client.|StorageTarget|
|StorageTargetSyncWriteThroughput|Velocità di scrittura sincrona nella destinazione di archiviazione|Byte al secondo|Media|Frequenza con cui la cache scrive in modo sincrono i dati in un particolare StorageTarget. Si tratta di scritture che fanno sì che i client si blocchino.|StorageTarget|
|StorageTargetTotalWriteThroughput|Velocità effettiva scritture totali dalla destinazione di archiviazione|Byte al secondo|Media|Velocità totale di scrittura dei dati nella cache in un particolare StorageTarget.|StorageTarget|
|StorageTargetLatency|Latenza destinazione di archiviazione|Millisecondi|Media|Latenza media round trip di tutte le operazioni sui file inviate dalla cache a una StorageTarget partricular.|StorageTarget|
|StorageTargetMetadataReadIOPS|Operazioni di I/O al secondo di lettura dei metadati della destinazione di archiviazione|Conteggio al secondo|Media|Frequenza delle operazioni sui file che non modificano lo stato persistente ed escludendo l'operazione di lettura, che la cache Invia a un determinato StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Operazioni di I/O al secondo di scrittura dei metadati della destinazione di archiviazione|Conteggio al secondo|Media|Frequenza delle operazioni sui file che modificano lo stato persistente ed escludono l'operazione di scrittura, che la cache Invia a un determinato StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Operazioni di I/O al secondo lette dalla destinazione di archiviazione|Conteggio al secondo|Media|Frequenza delle operazioni di lettura del file inviate dalla cache a un determinato StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Velocità effettiva di lettura in avanti della destinazione di archiviazione|Byte al secondo|Media|Frequenza con cui la cache opportunisticly legge i dati da StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Velocità effettiva di riempimento della destinazione di archiviazione|Byte al secondo|Media|Frequenza con cui la cache legge i dati da StorageTarget per gestire un mancato riscontro nella cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Velocità effettiva letture totali dalla destinazione di archiviazione|Byte al secondo|Media|Velocità totale di lettura dei dati da un particolare StorageTarget da parte della cache.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result (Risultato sessione di sincronizzazione)|Conteggio|Media|Metrica che registra il valore 1 ogni volta che l'endpoint del server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Cloud tiering recall size (Dimensioni richiamo cloud a livelli)|Byte|Totale|Dimensione dei dati richiamati|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Cloud tiering recall throughput (Velocità effettiva richiamo cloud a livelli)|Byte al secondo|Media|Dimensioni della velocità effettiva di richiamo dei dati|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Cloud tiering recall size by application (Dimensioni richiamo cloud a livelli per applicazione)|Byte|Totale|Dimensioni dei dati richiamati dall'applicazione|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|ServerEndpointName, SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerHeartbeat|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Esecuzioni di pipeline terminate|Conteggio|Totale|Numero di esecuzioni di pipeline di orchestrazione con esito positivo, negativo o annullato|Risultato, FailureType, pipeline|
|OrchestrationActivityRunsEnded|Esecuzioni attività terminate|Conteggio|Totale|Numero di attività di orchestrazione con esito positivo, negativo o annullato|Risultato, FailureType, attività, ActivityType, pipeline|
|OrchestrationTriggersEnded|Trigger terminati|Conteggio|Totale|Numero di trigger di orchestrazione riusciti, non riusciti o annullati|Risultato, FailureType, trigger|
|SQLOnDemandLoginAttempts|Tentativi di accesso|Conteggio|Totale|Numero di tentativi di accesso riuscito o non riusciti|Risultato|
|SQLOnDemandQueriesEnded|Query terminate|Conteggio|Totale|Numero di query che hanno avuto esito positivo, negativo o sono state annullate|Risultato|
|SQLOnDemandQueryProcessedBytes|Dati elaborati|Byte|Totale|Quantità di dati elaborati dalle query|Nessuno|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. sinapsi/Workspaces/bigDataPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SparkJobsEnded|Applicazioni terminate|Conteggio|Totale|Numero di applicazioni terminate|Tipoprocesso, JobResult|
|CoresCapacity|Capacità core|Conteggio|Massimo|Capacità core|Nessuno|
|MemoryCapacityGB|Capacità memoria (GB)|Conteggio|Massimo|Capacità memoria (GB)|Nessuno|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. sinapsi/Workspaces/sqlpools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|DWULimit|Limite DWU|Conteggio|Massimo|Obiettivo del livello di servizio del pool SQL|Nessuno|
|DWUUsed|Uso DWU|Conteggio|Massimo|Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato per limite DWU * DWU%|Nessuno|
|DWUUsedPercent|Percentuale di utilizzo di DWU|Percentuale|Massimo|Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato prendendo il massimo tra la percentuale di CPU e la percentuale di i/o dei dati|Nessuno|
|ConnectionsBlockedByFirewall|Connessioni bloccate dal firewall|Conteggio|Totale|Numero di connessioni bloccate dalle regole del firewall. Rivedere i criteri di controllo di accesso per il pool SQL e monitorare queste connessioni se il conteggio è elevato|Nessuno|
|AdaptiveCacheHitPercent|Percentuale riscontri cache adattiva|Percentuale|Massimo|Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale di riscontri nella cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache.|Nessuno|
|AdaptiveCacheUsedPercent|Percentuale utilizzata della cache adattiva|Percentuale|Massimo|Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale utilizzata della cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache.|Nessuno|
|LocalTempDBUsedPercent|Percentuale di utilizzo del tempdb locale|Percentuale|Massimo|Utilizzo tempdb locale in tutti i nodi di calcolo: i valori vengono emessi ogni cinque minuti|Nessuno|
|MemoryUsedPercent|Percentuale di memoria utilizzata|Percentuale|Massimo|Utilizzo della memoria in tutti i nodi del pool SQL|Nessuno|
|Connessioni|Connessioni|Conteggio|Totale|Numero totale di accessi al pool SQL|Risultato|
|WLGActiveQueries|Query attive del gruppo di carico di lavoro|Conteggio|Totale|Query attive all'interno del gruppo di carico di lavoro. Utilizzando questa metrica non filtrata e unsplit, vengono visualizzate tutte le query attive in esecuzione nel sistema|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Timeout query del gruppo di carico di lavoro|Conteggio|Totale|Query per il gruppo del carico di lavoro di cui si è verificato il timeout. I timeout delle query segnalati da questa metrica sono solo dopo l'avvio dell'esecuzione della query (non è incluso il tempo di attesa a causa del blocco o delle attese delle risorse)|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Allocazione del gruppo di carico di lavoro per percentuale di sistema|Percentuale|Massimo|Percentuale di allocazione delle risorse rispetto all'intero sistema|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Allocazione del gruppo di carico di lavoro per percentuale risorse massima|Percentuale|Massimo|Visualizza la percentuale di allocazione delle risorse rispetto alla percentuale effettiva delle risorse CAP per gruppo di carico di lavoro. Questa metrica fornisce l'utilizzo effettivo del gruppo di carico di lavoro|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Percentuale limite di risorse effettiva|Percentuale|Massimo|Percentuale effettiva della risorsa Cap per il gruppo del carico di lavoro. Se sono presenti altri gruppi di carico di lavoro con min_percentage_resource > 0, il effective_cap_percentage_resource viene ridotto proporzionalmente|IsUserDefined, WorkloadGroup|
|wlg_effective_min_resource_percent|Percentuale risorse minima effettiva|Percentuale|Minima|L'impostazione percentuale effettiva minima delle risorse può prendere in considerazione il livello di servizio e le impostazioni del gruppo di carico di lavoro. Il min_percentage_resource effettivo può essere modificato in un livello superiore rispetto ai livelli di servizio più bassi|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Query accodate del gruppo di carico di lavoro|Conteggio|Totale|Conteggio cumulativo delle richieste accodate dopo il raggiungimento del limite massimo di concorrenza|IsUserDefined, WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuno|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di scrittura nel periodo di campionamento.|Nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|Nessuno|
|DiskReadOperations|Disk Read Operations (Operazioni di lettura da disco)|Conteggio|Totale|Numero di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|DiskWriteOperations|Disk Write Operations (Operazioni di scrittura su disco)|Conteggio|Totale|Numero di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuno|
|DiskReadLatency|Latenza di lettura da disco|Millisecondi|Media|Latenza di lettura totale. Somma delle latenze di lettura del dispositivo e del kernel.|Nessuno|
|DiskWriteLatency|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. Somma delle latenze di scrittura del dispositivo e del kernel.|Nessuno|
|NetworkInBytesPerSecond|Network In Bytes/Sec (Rete in ingresso in byte/sec)|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|Nessuno|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec (Rete in uscita in byte/sec)|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|Nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|Nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|Nessuno|
|MemoryUsed|Memory Used (Memoria usata)|Byte|Media|Quantità di memoria del computer utilizzata dalla VM.|Nessuno|
|MemoryGranted|Memory Granted (Memoria concessa)|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host fino a quando non viene toccata una sola volta e la memoria concessa potrebbe essere invertita o rimossa se il VMkernel richiede la memoria.|Nessuno|
|MemoryActive|Memory Active (Memoria attiva)|Byte|Media|Quantità di memoria usata dalla macchina virtuale in un intervallo di tempo ridotto. Questo è il numero "vero" della quantità di memoria attualmente necessaria per la macchina virtuale. È possibile che venga scambiata una memoria aggiuntiva inutilizzata o che non abbia alcun effetto sulle prestazioni del Guest.|Nessuno|
|Percentuale CPU|CPU percentuale|Percentuale|Media|Utilizzo della CPU. Questo valore viene segnalato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale a 2 vie che usa il 50% di un sistema a quattro core USA completamente due core.|Nessuno|
|PercentageCpuReady|Percentage CPU Ready (Percentuale di disponibilità CPU)|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento precedente.|Nessuno|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|TcpSynSent|TCP Syn Sent|Conteggio|Media|TCP Syn Sent|Istanza|
|TcpSynReceived|TCP Syn Received|Conteggio|Media|TCP Syn Received|Istanza|
|TcpEstablished|TCP Established|Conteggio|Media|TCP Established|Istanza|
|TcpFinWait1|TCP Fin Wait 1|Conteggio|Media|TCP Fin Wait 1|Istanza|
|TcpFinWait2|TCP Fin Wait 2|Conteggio|Media|TCP Fin Wait 2|Istanza|
|TcpClosing|TCP Closing|Conteggio|Media|TCP Closing|Istanza|
|TcpCloseWait|TCP Close Wait|Conteggio|Media|TCP Close Wait|Istanza|
|TcpLastAck|TCP Last Ack|Conteggio|Media|TCP Last Ack|Istanza|
|TcpTimeWait|TCP Time Wait|Conteggio|Media|TCP Time Wait|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni) 

> [!NOTE]
> L' **utilizzo del file System** è una nuova metrica distribuita a livello globale, non è previsto alcun dato, a meno che non sia stata inserita nell'elenco elementi consentiti per l'anteprima privata.

> [!IMPORTANT]
> Il **tempo di risposta medio** sarà deprecato per evitare confusione con le aggregazioni di metriche. Usare il **tempo di risposta** come sostituzione.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|HttpResponseTime|Tempo di risposta|Secondi|Totale|Tempo di risposta|Istanza|
|AverageResponseTime|Tempo medio di risposta (obsoleto)|Secondi|Media|Tempo medio di risposta|Istanza|
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
|HealthCheckStatus|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

> [!NOTE]
> L' **utilizzo del file System** è una nuova metrica distribuita a livello globale, non è previsto alcun dato, a meno che non sia stata inserita nell'elenco elementi consentiti per l'anteprima privata.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|MB/millisecondi|Totale|[Unità di esecuzione della funzione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
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
|HealthCheckStatus|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|HealthCheckStatus|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|FileSystemUsage|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuno|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
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
|TotalFrontEnds|Front end totali|Conteggio|Media|Front end totali|Nessuno|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|Nessuno|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|Nessuno|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|Nessuno|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|Nessuno|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|Nessuno|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|Nessuno|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Creare avvisi relativi alle metriche](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](platform-logs-overview.md)
