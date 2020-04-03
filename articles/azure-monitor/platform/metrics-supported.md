---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 79bae9712cea04425cc36414ec56fdddd4345eab
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586033"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

> [!NOTE]
> Questo elenco viene generato automaticamente dall'API REST delle metriche di Azure Monitor.This list is largely auto-generated from the Azure Monitor Metrics REST API. Qualsiasi modifica apportata a questo elenco tramite Github può essere sovrascritta senza preavviso. Contattare l'autore di questo articolo per informazioni dettagliate su come effettuare aggiornamenti permanenti.

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Di seguito è riportato un elenco completo di tutte le metriche attualmente disponibili con la pipeline delle metriche di monitoraggio di Azure. Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy. L'elenco riportato di seguito include solo le metriche disponibili tramite la pipeline delle metriche di Monitoraggio di Azure consolidata. Le metriche sono organizzate per spazio dei nomi. Per un elenco dei servizi e degli spazi dei nomi che appartengono a essi, vedere Provider di risorse per i servizi di [Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md) Per eseguire una query e accedere a queste metriche a livello di codice, utilizzare la versione API 2018-01-01To query for and access these metrics programmatically, please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
>
> *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
>
> Per un elenco delle metriche della piattaforma esportabili tramite le impostazioni di diagnostica, vedere [questo articolo](metrics-supported-export-diagnostic-settings.md).


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
|mashup_engine_private_bytes_metric|Byte privati del motore MM Engine Private Bytes|Byte|Media|Utilizzo di byte privati da parte dei processi del motore mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Byte virtuali del motore MM Engine Virtual Bytes|Byte|Media|Utilizzo di byte virtuali da parte dei processi del motore di mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalRequests|Totale richieste gateway (obsoleto)|Conteggio|Totale|Numero di richieste gateway: usare la metrica delle richieste multidimensionali con la dimensione GatewayResponseCodeCategory|Percorso,Nome host|
|SuccessfulRequests|Richieste gateway riuscite (obsoleto)|Conteggio|Totale|Numero di richieste gateway riuscite - Utilizzare la metrica delle richieste multidimensionali con la dimensione GatewayResponseCodeCategory|Percorso,Nome host|
|UnauthorizedRequests|Richieste gateway non autorizzate (obsoleto)|Conteggio|Totale|Numero di richieste di gateway non autorizzate - Usare la metrica delle richieste multi-dimensione con la dimensione GatewayResponseCodeCategory|Percorso,Nome host|
|FailedRequests|Richieste gateway non riuscite (obsoleto)|Conteggio|Totale|Numero di errori nelle richieste gateway - Usare la metrica delle richieste multidimensionali con la dimensione GatewayResponseCodeCategory|Percorso,Nome host|
|OtherRequests|Altre richieste gateway (obsoleto)|Conteggio|Totale|Numero di altre richieste gateway: usare la metrica delle richieste multidimensionali con la dimensione GatewayResponseCodeCategoryNumberNumber of other gateway requests - Use multi-dimension request metric with GatewayResponseCodeCategory dimension instead|Percorso,Nome host|
|Duration|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Percorso,Nome host|
|BackendDuration|Durata delle richieste di back-end|Millisecondi|Media|Durata delle richieste di back-end in millisecondi|Percorso,Nome host|
|Capacity|Capacity|Percentuale|Media|Metrica di utilizzo per il servizio ApiManagement|Location|
|EventHubTotalEvents|Totale eventi EventHub|Conteggio|Totale|Numero di eventi inviati a EventHub|Location|
|EventHubSuccessfulEvents|Eventi EventHub riusciti|Conteggio|Totale|Numero di eventi EventHub riusciti|Location|
|EventHubTotalFailedEvents|Eventi EventHub non riusciti|Conteggio|Totale|Numero di eventi EventHub non riusciti|Location|
|EventHubRejectedEvents|Eventi EventHub rifiutati|Conteggio|Totale|Numero di eventi EventHub rifiutati (configurazione errata o non autorizzata)|Location|
|EventHubThrottledEvents|Throttled EventHub Eventi|Conteggio|Totale|Numero di eventi EventHub limitati|Location|
|EventHubTimedoutEvents|Eventi EventHub timeout|Conteggio|Totale|Numero di eventi EventHub verificatisi|Location|
|EventHubDroppedEvents|Eventi EventHub eliminati|Conteggio|Totale|Numero di eventi ignorati a causa del raggiungimento del limite di dimensione della coda|Location|
|EventHubTotalBytesSent|Dimensione degli eventi EventHub|Byte|Totale|Dimensione totale degli eventi EventHub in byte|Location|
|Requests|Requests|Conteggio|Totale|Metriche delle richieste di gateway con più dimensioniGateway request metrics with multiple dimensions|Percorso,Nome host,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Connettività di rete|Stato della connettività di rete delle risorse (anteprima)Network Connectivity Status of Resources (Preview)|Conteggio|Totale|Stato della connettività di rete dei tipi di risorse dipendenti dal servizio Gestione API|Posizione,TipoRisorsa|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Conteggio|Conteggio|Numero totale di richieste http in arrivo.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Conteggio|Media|Latenza su una richiesta http.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Primavera

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SystemCpuUsagePercentuale|Percentuale di utilizzo della CPU di sistema|Percentuale|Media|Il recente utilizzo di cpu per l'intero sistema|NomeApp,Pod|
|Percentuale di AppCpuUsagePercentage|Percentuale di utilizzo della CPU dell'app|Percentuale|Media|Percentuale di utilizzo della CPU JVM dell'app|NomeApp,Pod|
|AppMemoryCommitted|Memoria app assegnata|Byte|Media|Memoria assegnata a JVM in byte|NomeApp,Pod|
|AppMemoryUsed|Memoria dell'app utilizzata|Byte|Media|Memoria app utilizzata in byte|NomeApp,Pod|
|AppMemoryMax|Memoria app max|Byte|Massimo|Quantità massima di memoria in byte che può essere utilizzata per la gestione della memoria|NomeApp,Pod|
|MaxOldGenMemoryPoolByte|Dimensioni massime dei dati di vecchia generazione disponibiliMax Available Old Generation Data Size|Byte|Media|Dimensioni massime del pool di memoria di vecchia generazione|NomeApp,Pod|
|Byte OldGenMemoryPoolBytes|Dimensioni dei dati di generazione precedente|Byte|Media|Dimensioni del pool di memoria di vecchia generazione dopo un GC completo|NomeApp,Pod|
|OldGenPromotedBytes|Promuovi a dimensioni dei dati di vecchia generazione|Byte|Massimo|Conteggio degli aumenti positivi delle dimensioni del pool di memoria di vecchia generazione prima di GC a dopo GC|NomeApp,Pod|
|YoungGenPromotedBytes|Promuovere a giovani dimensioni dei dati|Byte|Massimo|Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovane dopo un GC a prima del successivo|NomeApp,Pod|
|Metodo GCPauseTotalCount|Conteggio pause GC|Conteggio|Totale|Conteggio pause GC|NomeApp,Pod|
|Metodo GCPauseTotalTime|Tempo totale pausa GC|Millisecondi|Totale|Tempo totale pausa GC|NomeApp,Pod|
|Byte TomcatSentBytes|Totale byte inviati Tomcat|Byte|Totale|Totale byte inviati Tomcat|NomeApp,Pod|
|TomcatReceivedBytes|Byte totali ricevuti Tomcat|Byte|Totale|Byte totali ricevuti Tomcat|NomeApp,Pod|
|TomcatRequestTotalTime (Informazioni in base al tempo di esecuzione)|Tempi totali richieste Tomcat|Millisecondi|Totale|Tempi totali richieste Tomcat|NomeApp,Pod|
|TomcatRequestTotalCount|Conteggio totale richieste Tomcat|Conteggio|Totale|Conteggio totale richieste Tomcat|NomeApp,Pod|
|TomcatResponseAvgTime (Ora di TomcatResponseAvgTime)|Tomcat Richiesta Tempo medio|Millisecondi|Media|Tomcat Richiesta Tempo medio|NomeApp,Pod|
|TomcatRequestMaxTime|Ora massima richiesta Tomcat|Millisecondi|Massimo|Ora massima richiesta Tomcat|NomeApp,Pod|
|TomcatErrorCount (Conteggio errori di calcolo)|Errore globale Tomcat|Conteggio|Totale|Errore globale Tomcat|NomeApp,Pod|
|TomcatSessionActiveMaxCount|Conteggio attivo massimo sessione Tomcat|Conteggio|Totale|Conteggio attivo massimo sessione Tomcat|NomeApp,Pod|
|TomcatSessionAliveMaxTime|Tempo di vita massimo sessione Tomcat|Millisecondi|Massimo|Tempo di vita massimo sessione Tomcat|NomeApp,Pod|
|TomcatSessionActiveCurrentCount|Numero di sessioni Tomcat Alive|Conteggio|Totale|Numero di sessioni Tomcat Alive|NomeApp,Pod|
|TomcatSessionCreatedCount|Conteggio sessioni Tomcat create|Conteggio|Totale|Conteggio sessioni Tomcat create|NomeApp,Pod|
|TomcatSessionExpiredCount (Conteggio sessioni risposte)|Conteggio sessione tomcat scaduto|Conteggio|Totale|Conteggio sessione tomcat scaduto|NomeApp,Pod|
|TomcatSessionRejectedCount|Conteggio sessioni Tomcat rifiutate|Conteggio|Totale|Conteggio sessioni Tomcat rifiutate|NomeApp,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalJob|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook,Stato|
|TotalUpdateDeploymentRuns|Esecuzioni totali della distribuzione di aggiornamenti|Conteggio|Totale|Totale esecuzioni della distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName,Stato|
|TotalUpdateDeploymentMachineRuns|Esecuzioni totali della distribuzione di aggiornamenti del computer|Conteggio|Totale|Totale esecuzioni del computer di distribuzione degli aggiornamenti software in un'esecuzione della distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName,Stato,ComputerDestinazione,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CoreCount|Numero di core dedicati|Conteggio|Totale|Numero totale di core dedicati nell'account Batch|nessuno|
|TotalNodeCount|Numero di nodi dedicati|Conteggio|Totale|Numero totale di nodi dedicati nell'account Batch|nessuno|
|LowPriorityCoreCount|Numero di core a bassa priorità|Conteggio|Totale|Numero totale di core a bassa priorità nell'account Batch|nessuno|
|TotalLowPriorityNodeCount|Numero di nodi a bassa priorità|Conteggio|Totale|Numero totale di nodi a bassa priorità nell'account Batch|nessuno|
|CreatingNodeCount|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|nessuno|
|StartingNodeCount|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|nessuno|
|WaitingForStartTaskNodeCount|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|nessuno|
|StartTaskFailedNodeCount|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|nessuno|
|IdleNodeCount|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|nessuno|
|OfflineNodeCount|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|nessuno|
|RebootingNodeCount|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|nessuno|
|ReimagingNodeCount|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|nessuno|
|RunningNodeCount|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|nessuno|
|LeavingPoolNodeCount|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|nessuno|
|UnusableNodeCount|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|nessuno|
|PreemptedNodeCount|Numero di nodi annullati|Conteggio|Totale|Numero di nodi annullati|nessuno|
|TaskStartEvent|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|poolId,idlavoro|
|TaskCompleteEvent|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|poolId,idlavoro|
|TaskFailEvent|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|poolId,idlavoro|
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


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/aree di lavoro

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Lavoro inviato|Lavoro inviato|Conteggio|Totale|Numero di lavori inviati|Scenario,NomeCluster|
|Processo completato|Processo completato|Conteggio|Totale|Numero di lavori completati|Scenario,NomeCluster,TipoRisulta|
|Nodi totali|Nodi totali|Conteggio|Media|Numero di nodi totali|Scenario,NomeCluster|
|Nodi attivi|Nodi attivi|Conteggio|Media|Il numero di nodi in esecuzione|Scenario,NomeCluster|
|Nodi inattivi|Nodi inattivi|Conteggio|Media|Il numero di nodi inattivi|Scenario,NomeCluster|
|Nodi inutilizzabili|Nodi inutilizzabili|Conteggio|Media|Il numero di nodi che non possono essere usati|Scenario,NomeCluster|
|Nodi anticipati|Nodi anticipati|Conteggio|Media|Numero di nodi annullati|Scenario,NomeCluster|
|Dati in uscita|Dati in uscita|Conteggio|Media|Numero di nodi in uscita|Scenario,NomeCluster|
|Nuclei totali|Nuclei totali|Conteggio|Media|Numero di core totali|Scenario,NomeCluster|
|Nuclei attivi|Nuclei attivi|Conteggio|Media|Numero di core attivi|Scenario,NomeCluster|
|Core inattivi|Core inattivi|Conteggio|Media|Numero di core inattivi|Scenario,NomeCluster|
|Core inutilizzabili|Core inutilizzabili|Conteggio|Media|Numero di core inutilizzabili|Scenario,NomeCluster|
|Nuclei preempted|Nuclei preempted|Conteggio|Media|Numero di core anticipati|Scenario,NomeCluster|
|Lasciare i nuclei|Lasciare i nuclei|Conteggio|Media|Numero di nuclei di uscita|Scenario,NomeCluster|
|Percentuale di utilizzo della quota|Percentuale di utilizzo della quota|Conteggio|Media|Percentuale di quota utilizzata|Scenario,NomeCluster,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentuale di utilizzo CPU|Percentuale|Massimo|Percentuale di utilizzo CPU|Nodo|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Utilizzo della memoria|Nodo|
|MemoryLimit|Limite di memoria|Byte|Media|Limite di memoria|Nodo|
|MemoryUsagePercentageInDouble|Percentuale utilizzo memoria|Percentuale|Media|Percentuale utilizzo memoria|Nodo|
|StorageUsage|Utilizzo dello storage|Byte|Media|Utilizzo dello storage|Nodo|
|IOReadBytes|Byte di lettura IO|Byte|Totale|Byte di lettura IO|Nodo|
|IOWriteBytesIWriteBytes|Byte di scrittura IO|Byte|Totale|Byte di scrittura IO|Nodo|
|ConnectionAccepted|Connessioni accettate|Conteggio|Totale|Connessioni accettate|Nodo|
|ConnectionHandled|Connessioni gestite|Conteggio|Totale|Connessioni gestite|Nodo|
|ConnectionActive (Attivo)|Connessioni attive|Conteggio|Media|Connessioni attive|Nodo|
|RequestHandled|Richieste gestite|Conteggio|Totale|Richieste gestite|Nodo|
|ProcessedBlocks (Blocchi elaborati)|Blocchi elaborati|Conteggio|Totale|Blocchi elaborati|Nodo|
|ProcessedTransactionsProcessedTransactions|Transazioni elaborate|Conteggio|Totale|Transazioni elaborate|Nodo|
|PendingTransactions|Transazioni in sospeso|Conteggio|Media|Transazioni in sospeso|Nodo|
|Transazioni in coda|Transazioni in coda|Conteggio|Media|Transazioni in coda|Nodo|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|connectedclients|Client connessi|Conteggio|Massimo||ShardId|
|totalcommandsprocessed|Totale operazioni|Conteggio|Totale||ShardId|
|cachehits|Riscontri cache|Conteggio|Totale||ShardId|
|cachemisses|Mancati riscontri nella cache|Conteggio|Totale||ShardId|
|cachemissrate|Frequenza di mancati riscontri nella cache|Percentuale|cachemissrate||ShardId|
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
|errori|Errors|Conteggio|Massimo||ShardId,ErrorType|
|connectedclients0|Client connessi (partizione 0)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed0|Totale operazioni (partizione 0)|Conteggio|Totale||nessuno|
|cachehits0|Riscontri cache (partizione 0)|Conteggio|Totale||nessuno|
|cachemisses0|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||nessuno|
|getcommands0|Operazioni Get (partizione 0)|Conteggio|Totale||nessuno|
|setcommands0|Operazioni Set (partizione 0)|Conteggio|Totale||nessuno|
|operationsPerSecond0|Operazioni al secondo (partizione 0)|Conteggio|Massimo||nessuno|
|evictedkeys0|Chiavi rimosse (partizione 0)|Conteggio|Totale||nessuno|
|totalkeys0|Totale chiavi (partizione 0)|Conteggio|Massimo||nessuno|
|expiredkeys0|Chiavi scadute (partizione 0)|Conteggio|Totale||nessuno|
|usedmemory0|Memoria usata (partizione 0)|Byte|Massimo||nessuno|
|usedmemoryRss0|Memoria usata RSS (partizione 0)|Byte|Massimo||nessuno|
|serverLoad0|Carico server (partizione 0)|Percentuale|Massimo||nessuno|
|cacheWrite0|Scrittura nella cache (partizione 0)|Byte al secondo|Massimo||nessuno|
|cacheRead0|Lettura da cache (partizione 0)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime0|CPU (partizione 0)|Percentuale|Massimo||nessuno|
|connectedclients1|Client connessi (partizione 1)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed1|Totale operazioni (partizione 1)|Conteggio|Totale||nessuno|
|cachehits1|Riscontri cache (partizione 1)|Conteggio|Totale||nessuno|
|cachemisses1|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||nessuno|
|getcommands1|Operazioni Get (partizione 1)|Conteggio|Totale||nessuno|
|setcommands1|Operazioni Set (partizione 1)|Conteggio|Totale||nessuno|
|operationsPerSecond1|Operazioni al secondo (partizione 1)|Conteggio|Massimo||nessuno|
|evictedkeys1|Chiavi rimosse (partizione 1)|Conteggio|Totale||nessuno|
|totalkeys1|Totale chiavi (partizione 1)|Conteggio|Massimo||nessuno|
|expiredkeys1|Chiavi scadute (partizione 1)|Conteggio|Totale||nessuno|
|usedmemory1|Memoria usata (partizione 1)|Byte|Massimo||nessuno|
|usedmemoryRss1|Memoria usata RSS (partizione 1)|Byte|Massimo||nessuno|
|serverLoad1|Carico server (partizione 1)|Percentuale|Massimo||nessuno|
|cacheWrite1|Scrittura nella cache (partizione 1)|Byte al secondo|Massimo||nessuno|
|cacheRead1|Lettura da cache (partizione 1)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime1|CPU (partizione 1)|Percentuale|Massimo||nessuno|
|connectedclients2|Client connessi (partizione 2)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed2|Totale operazioni (partizione 2)|Conteggio|Totale||nessuno|
|cachehits2|Riscontri cache (partizione 2)|Conteggio|Totale||nessuno|
|cachemisses2|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||nessuno|
|getcommands2|Operazioni Get (partizione 2)|Conteggio|Totale||nessuno|
|setcommands2|Operazioni Set (partizione 2)|Conteggio|Totale||nessuno|
|operationsPerSecond2|Operazioni al secondo (partizione 2)|Conteggio|Massimo||nessuno|
|evictedkeys2|Chiavi rimosse (partizione 2)|Conteggio|Totale||nessuno|
|totalkeys2|Totale chiavi (partizione 2)|Conteggio|Massimo||nessuno|
|expiredkeys2|Chiavi scadute (partizione 2)|Conteggio|Totale||nessuno|
|usedmemory2|Memoria usata (partizione 2)|Byte|Massimo||nessuno|
|usedmemoryRss2|Memoria usata RSS (partizione 2)|Byte|Massimo||nessuno|
|serverLoad2|Carico server (partizione 2)|Percentuale|Massimo||nessuno|
|cacheWrite2|Scrittura nella cache (partizione 2)|Byte al secondo|Massimo||nessuno|
|cacheRead2|Lettura da cache (partizione 2)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime2|CPU (partizione 2)|Percentuale|Massimo||nessuno|
|connectedclients3|Client connessi (partizione 3)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed3|Totale operazioni (partizione 3)|Conteggio|Totale||nessuno|
|cachehits3|Riscontri cache (partizione 3)|Conteggio|Totale||nessuno|
|cachemisses3|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||nessuno|
|getcommands3|Operazioni Get (partizione 3)|Conteggio|Totale||nessuno|
|setcommands3|Operazioni Set (partizione 3)|Conteggio|Totale||nessuno|
|operationsPerSecond3|Operazioni al secondo (partizione 3)|Conteggio|Massimo||nessuno|
|evictedkeys3|Chiavi rimosse (partizione 3)|Conteggio|Totale||nessuno|
|totalkeys3|Totale chiavi (partizione 3)|Conteggio|Massimo||nessuno|
|expiredkeys3|Chiavi scadute (partizione 3)|Conteggio|Totale||nessuno|
|usedmemory3|Memoria usata (partizione 3)|Byte|Massimo||nessuno|
|usedmemoryRss3|Memoria usata RSS (partizione 3)|Byte|Massimo||nessuno|
|serverLoad3|Carico server (partizione 3)|Percentuale|Massimo||nessuno|
|cacheWrite3|Scrittura nella cache (partizione 3)|Byte al secondo|Massimo||nessuno|
|cacheRead3|Lettura da cache (partizione 3)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime3|CPU (partizione 3)|Percentuale|Massimo||nessuno|
|connectedclients4|Client connessi (partizione 4)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed4|Totale operazioni (partizione 4)|Conteggio|Totale||nessuno|
|cachehits4|Riscontri cache (partizione 4)|Conteggio|Totale||nessuno|
|cachemisses4|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||nessuno|
|getcommands4|Operazioni Get (partizione 4)|Conteggio|Totale||nessuno|
|setcommands4|Operazioni Set (partizione 4)|Conteggio|Totale||nessuno|
|operationsPerSecond4|Operazioni al secondo (partizione 4)|Conteggio|Massimo||nessuno|
|evictedkeys4|Chiavi rimosse (partizione 4)|Conteggio|Totale||nessuno|
|totalkeys4|Totale chiavi (partizione 4)|Conteggio|Massimo||nessuno|
|expiredkeys4|Chiavi scadute (partizione 4)|Conteggio|Totale||nessuno|
|usedmemory4|Memoria usata (partizione 4)|Byte|Massimo||nessuno|
|usedmemoryRss4|Memoria usata RSS (partizione 4)|Byte|Massimo||nessuno|
|serverLoad4|Carico server (partizione 4)|Percentuale|Massimo||nessuno|
|cacheWrite4|Scrittura nella cache (partizione 4)|Byte al secondo|Massimo||nessuno|
|cacheRead4|Lettura da cache (partizione 4)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime4|CPU (partizione 4)|Percentuale|Massimo||nessuno|
|connectedclients5|Client connessi (partizione 5)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed5|Totale operazioni (partizione 5)|Conteggio|Totale||nessuno|
|cachehits5|Riscontri cache (partizione 5)|Conteggio|Totale||nessuno|
|cachemisses5|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||nessuno|
|getcommands5|Operazioni Get (partizione 5)|Conteggio|Totale||nessuno|
|setcommands5|Operazioni Set (partizione 5)|Conteggio|Totale||nessuno|
|operationsPerSecond5|Operazioni al secondo (partizione 5)|Conteggio|Massimo||nessuno|
|evictedkeys5|Chiavi rimosse (partizione 5)|Conteggio|Totale||nessuno|
|totalkeys5|Totale chiavi (partizione 5)|Conteggio|Massimo||nessuno|
|expiredkeys5|Chiavi scadute (partizione 5)|Conteggio|Totale||nessuno|
|usedmemory5|Memoria usata (partizione 5)|Byte|Massimo||nessuno|
|usedmemoryRss5|Memoria usata RSS (partizione 5)|Byte|Massimo||nessuno|
|serverLoad5|Carico server (partizione 5)|Percentuale|Massimo||nessuno|
|cacheWrite5|Scrittura nella cache (partizione 5)|Byte al secondo|Massimo||nessuno|
|cacheRead5|Lettura da cache (partizione 5)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime5|CPU (partizione 5)|Percentuale|Massimo||nessuno|
|connectedclients6|Client connessi (partizione 6)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed6|Totale operazioni (partizione 6)|Conteggio|Totale||nessuno|
|cachehits6|Riscontri cache (partizione 6)|Conteggio|Totale||nessuno|
|cachemisses6|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||nessuno|
|getcommands6|Operazioni Get (partizione 6)|Conteggio|Totale||nessuno|
|setcommands6|Operazioni Set (partizione 6)|Conteggio|Totale||nessuno|
|operationsPerSecond6|Operazioni al secondo (partizione 6)|Conteggio|Massimo||nessuno|
|evictedkeys6|Chiavi rimosse (partizione 6)|Conteggio|Totale||nessuno|
|totalkeys6|Totale chiavi (partizione 6)|Conteggio|Massimo||nessuno|
|expiredkeys6|Chiavi scadute (partizione 6)|Conteggio|Totale||nessuno|
|usedmemory6|Memoria usata (partizione 6)|Byte|Massimo||nessuno|
|usedmemoryRss6|Memoria usata RSS (partizione 6)|Byte|Massimo||nessuno|
|serverLoad6|Carico server (partizione 6)|Percentuale|Massimo||nessuno|
|cacheWrite6|Scrittura nella cache (partizione 6)|Byte al secondo|Massimo||nessuno|
|cacheRead6|Lettura da cache (partizione 6)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime6|CPU (partizione 6)|Percentuale|Massimo||nessuno|
|connectedclients7|Client connessi (partizione 7)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed7|Totale operazioni (partizione 7)|Conteggio|Totale||nessuno|
|cachehits7|Riscontri cache (partizione 7)|Conteggio|Totale||nessuno|
|cachemisses7|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||nessuno|
|getcommands7|Operazioni Get (partizione 7)|Conteggio|Totale||nessuno|
|setcommands7|Operazioni Set (partizione 7)|Conteggio|Totale||nessuno|
|operationsPerSecond7|Operazioni al secondo (partizione 7)|Conteggio|Massimo||nessuno|
|evictedkeys7|Chiavi rimosse (partizione 7)|Conteggio|Totale||nessuno|
|totalkeys7|Totale chiavi (partizione 7)|Conteggio|Massimo||nessuno|
|expiredkeys7|Chiavi scadute (partizione 7)|Conteggio|Totale||nessuno|
|usedmemory7|Memoria usata (partizione 7)|Byte|Massimo||nessuno|
|usedmemoryRss7|Memoria usata RSS (partizione 7)|Byte|Massimo||nessuno|
|serverLoad7|Carico server (partizione 7)|Percentuale|Massimo||nessuno|
|cacheWrite7|Scrittura nella cache (partizione 7)|Byte al secondo|Massimo||nessuno|
|cacheRead7|Lettura da cache (partizione 7)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime7|CPU (partizione 7)|Percentuale|Massimo||nessuno|
|connectedclients8|Client connessi (partizione 8)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed8|Totale operazioni (partizione 8)|Conteggio|Totale||nessuno|
|cachehits8|Riscontri cache (partizione 8)|Conteggio|Totale||nessuno|
|cachemisses8|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||nessuno|
|getcommands8|Operazioni Get (partizione 8)|Conteggio|Totale||nessuno|
|setcommands8|Operazioni Set (partizione 8)|Conteggio|Totale||nessuno|
|operationsPerSecond8|Operazioni al secondo (partizione 8)|Conteggio|Massimo||nessuno|
|evictedkeys8|Chiavi rimosse (partizione 8)|Conteggio|Totale||nessuno|
|totalkeys8|Totale chiavi (partizione 8)|Conteggio|Massimo||nessuno|
|expiredkeys8|Chiavi scadute (partizione 8)|Conteggio|Totale||nessuno|
|usedmemory8|Memoria usata (partizione 8)|Byte|Massimo||nessuno|
|usedmemoryRss8|Memoria usata RSS (partizione 8)|Byte|Massimo||nessuno|
|serverLoad8|Carico server (partizione 8)|Percentuale|Massimo||nessuno|
|cacheWrite8|Scrittura nella cache (partizione 8)|Byte al secondo|Massimo||nessuno|
|cacheRead8|Lettura da cache (partizione 8)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime8|CPU (partizione 8)|Percentuale|Massimo||nessuno|
|connectedclients9|Client connessi (partizione 9)|Conteggio|Massimo||nessuno|
|totalcommandsprocessed9|Totale operazioni (partizione 9)|Conteggio|Totale||nessuno|
|cachehits9|Riscontri cache (partizione 9)|Conteggio|Totale||nessuno|
|cachemisses9|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||nessuno|
|getcommands9|Operazioni Get (partizione 9)|Conteggio|Totale||nessuno|
|setcommands9|Operazioni Set (partizione 9)|Conteggio|Totale||nessuno|
|operationsPerSecond9|Operazioni al secondo (partizione 9)|Conteggio|Massimo||nessuno|
|evictedkeys9|Chiavi rimosse (partizione 9)|Conteggio|Totale||nessuno|
|totalkeys9|Totale chiavi (partizione 9)|Conteggio|Massimo||nessuno|
|expiredkeys9|Chiavi scadute (partizione 9)|Conteggio|Totale||nessuno|
|usedmemory9|Memoria usata (partizione 9)|Byte|Massimo||nessuno|
|usedmemoryRss9|Memoria usata RSS (partizione 9)|Byte|Massimo||nessuno|
|serverLoad9|Carico server (partizione 9)|Percentuale|Massimo||nessuno|
|cacheWrite9|Scrittura nella cache (partizione 9)|Byte al secondo|Massimo||nessuno|
|cacheRead9|Lettura da cache (partizione 9)|Byte al secondo|Massimo||nessuno|
|percentProcessorTime9|CPU (partizione 9)|Percentuale|Massimo||nessuno|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|NomeCriterio,NomeRegola,Azione|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|nessuno|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|nessuno|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|nessuno|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Rete in uscita|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Disk Read Bytes/Sec|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Disk Write Bytes/Sec|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata da Archiviazione di Azure per elaborare una richiesta riuscita, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio BLOB dell'account di archiviazione in byte.|BlobType,Livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType,Livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|nessuno|
|IndexCapacity|Capacità dell'indice|Byte|Media|Quantità di spazio di archiviazione utilizzato dall'indice ADLS Gen2 (gerarchico) in byte.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata da Archiviazione di Azure per elaborare una richiesta riuscita, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di spazio di archiviazione usata dal servizio tabelle dell'account di archiviazione in byte.|nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabelle nel servizio tabelle dell'account di archiviazione.|nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata da Archiviazione di Azure per elaborare una richiesta riuscita, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio file dell'account di archiviazione in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|nessuno|
|FileShareSnapshotCount|Conteggio snapshot condivisione file|Conteggio|Media|Numero di snapshot presenti nella condivisione nel servizio File dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareQuota|Dimensione quota condivisione file|Byte|Media|Limite superiore alla quantità di spazio di archiviazione che può essere usata dal servizio file di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione,CondivisioneFile|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata da Archiviazione di Azure per elaborare una richiesta riuscita, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio di coda dell'account di archiviazione in byte.|nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Numero di code nel servizio di coda dell'account di archiviazione.|nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo di messaggi della coda nel servizio di coda dell'account di archiviazione.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata da Archiviazione di Azure per elaborare una richiesta riuscita, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalCalls|Totale chiamate|Conteggio|Totale|Numero totale di chiamate.|NomeApi,NomeOperazione,Regione|
|SuccessfulCalls|Chiamate riuscite|Conteggio|Totale|Numero di chiamate riuscite.|NomeApi,NomeOperazione,Regione|
|TotalErrors|Totale errori|Conteggio|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|NomeApi,NomeOperazione,Regione|
|BlockedCalls|Chiamate bloccate|Conteggio|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|NomeApi,NomeOperazione,Regione|
|ServerErrors|Errori server|Conteggio|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|NomeApi,NomeOperazione,Regione|
|ClientErrors|Errori client|Conteggio|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|NomeApi,NomeOperazione,Regione|
|DataIn|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|NomeApi,NomeOperazione,Regione|
|DataOut|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|NomeApi,NomeOperazione,Regione|
|Latenza|Latenza|Millisecondi|Media|Latenza in millisecondi.|NomeApi,NomeOperazione,Regione|
|TotalTokenCalls|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|NomeApi,NomeOperazione,Regione|
|CharactersTranslated|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|NomeApi,NomeOperazione,Regione|
|PersonaggiAddestrati|Personaggi addestrati|Conteggio|Totale|Numero totale di caratteri sottoposti a training.|NomeApi,NomeOperazione,Regione|
|SpeechSessionDuration|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|NomeApi,NomeOperazione,Regione|
|TotalTransactions|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|nessuno|
|ProcessedImages|Immagini elaborate|Conteggio|Totale| Numero di transazioni per l'elaborazione delle immagini.|NomeApi,NomeCaratteristica,Canale,Regione|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|nessuno|
|Rete in ingresso|Rete in fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (obsoleto)|nessuno|
|Rete in uscita|Uscita dalla rete fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili in uscita da tutte le interfacce di rete da parte delle macchine virtuali (traffico in uscita) (obsoleto)|nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|nessuno|
|Byte letti da disco/sec per disco|Byte letti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|SlotId|
|Profondità coda per disco dati|QD del disco dati [(obsoleto)](portal-disk-metrics-deprecation.md)] (portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|nessuno|
|Profondità coda per disco del sistema operativo|QD del disco del sistema operativo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|nessuno|
|Byte letti su disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura su disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|LUN|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)Data Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti su disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Byte scritti su disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Operazioni di lettura su disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|nessuno|
|Operazioni di scrittura su disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda disco sistema operativo (anteprima)OS Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono numero di flussi correnti nella direzione in ingresso (traffico in ingresso nella macchina virtuale)|nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono numero di flussi correnti nella direzione in uscita (il traffico che esce dalla macchina virtuale)|nessuno|
|Flussi in ingresso Velocità massima di creazione|Flussi in ingresso Velocità massima di creazione|Conteggio al secondo|Media|La velocità massima di creazione dei flussi in ingresso (traffico nella macchina virtuale)|nessuno|
|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|nessuno|
|Hit lettura cache su disco premium|Hit lettura cache disco dati Premium (anteprima)Premium Data Disk Cache Read Hit (Preview)|Percentuale|Media|Hit lettura cache su disco premium|LUN|
|Premium Data Disk Cache Lettura Miss|Premium Data Disk Cache Lettura Miss (anteprima)|Percentuale|Media|Premium Data Disk Cache Lettura Miss|LUN|
|Hit lettura della cache del disco del sistema operativo Premium|Hit lettura cache disco Premium OS (anteprima)|Percentuale|Media|Hit lettura della cache del disco del sistema operativo Premium|nessuno|
|Lettura lettura della cache del disco del sistema operativo Premium|Lettura lettura lettura cache su disco Premium OS (anteprima)|Percentuale|Media|Lettura lettura della cache del disco del sistema operativo Premium|nessuno|
|Rete in totale|Rete in totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|nessuno|
|Totale uscita rete|Totale uscita rete|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|nessuno|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Rete in ingresso|Rete in fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (obsoleto)|VMName|
|Rete in uscita|Uscita dalla rete fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili in uscita da tutte le interfacce di rete da parte delle macchine virtuali (traffico in uscita) (obsoleto)|VMName|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|nessuno|
|Byte letti da disco/sec per disco|Byte letti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|SlotId|
|Profondità coda per disco dati|QD del disco dati [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|nessuno|
|Profondità coda per disco del sistema operativo|QD del disco del sistema operativo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|nessuno|
|Byte letti su disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN,VMName|
|Byte scritti su disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN,VMName|
|Operazioni di lettura su disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|LUN,VMName|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|LUN,VMName|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)Data Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN,VMName|
|Byte letti su disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti su disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura su disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|VMName|
|Operazioni di scrittura su disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|VMName|
|Profondità coda per un disco del sistema operativo|Profondità coda disco sistema operativo (anteprima)OS Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono numero di flussi correnti nella direzione in ingresso (traffico in ingresso nella macchina virtuale)|VMName|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono numero di flussi correnti nella direzione in uscita (il traffico che esce dalla macchina virtuale)|VMName|
|Flussi in ingresso Velocità massima di creazione|Flussi in ingresso Velocità massima di creazione|Conteggio al secondo|Media|La velocità massima di creazione dei flussi in ingresso (traffico nella macchina virtuale)|VMName|
|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Hit lettura cache su disco premium|Hit lettura cache disco dati Premium (anteprima)Premium Data Disk Cache Read Hit (Preview)|Percentuale|Media|Hit lettura cache su disco premium|LUN,VMName|
|Premium Data Disk Cache Lettura Miss|Premium Data Disk Cache Lettura Miss (anteprima)|Percentuale|Media|Premium Data Disk Cache Lettura Miss|LUN,VMName|
|Hit lettura della cache del disco del sistema operativo Premium|Hit lettura cache disco Premium OS (anteprima)|Percentuale|Media|Hit lettura della cache del disco del sistema operativo Premium|VMName|
|Lettura lettura della cache del disco del sistema operativo Premium|Lettura lettura lettura cache su disco Premium OS (anteprima)|Percentuale|Media|Lettura lettura della cache del disco del sistema operativo Premium|VMName|
|Rete in totale|Rete in totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Totale uscita rete|Totale uscita rete|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CPU percentuale|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|nessuno|
|Rete in ingresso|Rete in fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (obsoleto)|nessuno|
|Rete in uscita|Uscita dalla rete fatturabile (obsoleto)|Byte|Totale|Numero di byte fatturabili in uscita da tutte le interfacce di rete da parte delle macchine virtuali (traffico in uscita) (obsoleto)|nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|nessuno|
|Crediti CPU rimanenti|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|nessuno|
|Crediti CPU usati|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|nessuno|
|Byte letti da disco/sec per disco|Byte letti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Byte scritti su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Operazioni di lettura su disco dati/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|SlotId|
|Operazioni di scrittura su disco/sec per disco|Operazioni di scrittura su disco dati al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|SlotId|
|Profondità coda per disco dati|QD del disco dati [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco del sistema operativo|Byte letti disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Byte scritti su disco/sec per disco del sistema operativo|Byte scritti su disco del sistema operativo/sec [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Operazioni di lettura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|nessuno|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Operazioni di scrittura su disco del sistema operativo al secondo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|nessuno|
|Profondità coda per disco del sistema operativo|QD del disco del sistema operativo [(obsoleto)](portal-disk-metrics-deprecation.md)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|nessuno|
|Byte letti su disco dati/sec|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco dati/sec|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura su disco dati/sec|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioRead IOPS from a single disk during monitoring period|LUN|
|Operazioni di scrittura su disco dati/sec|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggioWrite IOPS from a single disk during monitoring period|LUN|
|Profondità coda per un disco dati|Profondità coda del disco dati (anteprima)Data Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti su disco del sistema operativo/sec|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Byte scritti su disco del sistema operativo/sec|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|nessuno|
|Operazioni di lettura su disco del sistema operativo/sec|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere le operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoRead IOPS from a single disk during monitoring period for OS disk|nessuno|
|Operazioni di scrittura su disco del sistema operativo/sec|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrittura di operazioni di I/O al secondo da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativoWrite IOPS from a single disk during monitoring period for OS disk|nessuno|
|Profondità coda per un disco del sistema operativo|Profondità coda disco sistema operativo (anteprima)OS Disk Queue Depth (Preview)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|nessuno|
|Flussi in ingresso|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono numero di flussi correnti nella direzione in ingresso (traffico in ingresso nella macchina virtuale)|nessuno|
|Flussi in uscita|Flussi in uscita|Conteggio|Media|I flussi in uscita sono numero di flussi correnti nella direzione in uscita (il traffico che esce dalla macchina virtuale)|nessuno|
|Flussi in ingresso Velocità massima di creazione|Flussi in ingresso Velocità massima di creazione|Conteggio al secondo|Media|La velocità massima di creazione dei flussi in ingresso (traffico nella macchina virtuale)|nessuno|
|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Velocità di creazione massima dei flussi in uscitaOutbound Flows Maximum Creation Rate|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|nessuno|
|Hit lettura cache su disco premium|Hit lettura cache disco dati Premium (anteprima)Premium Data Disk Cache Read Hit (Preview)|Percentuale|Media|Hit lettura cache su disco premium|LUN|
|Premium Data Disk Cache Lettura Miss|Premium Data Disk Cache Lettura Miss (anteprima)|Percentuale|Media|Premium Data Disk Cache Lettura Miss|LUN|
|Hit lettura della cache del disco del sistema operativo Premium|Hit lettura cache disco Premium OS (anteprima)|Percentuale|Media|Hit lettura della cache del disco del sistema operativo Premium|nessuno|
|Lettura lettura della cache del disco del sistema operativo Premium|Lettura lettura lettura cache su disco Premium OS (anteprima)|Percentuale|Media|Lettura lettura della cache del disco del sistema operativo Premium|nessuno|
|Rete in totale|Rete in totale|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|nessuno|
|Totale uscita rete|Totale uscita rete|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|nessuno|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuUsage|Utilizzo di CPU|Conteggio|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Utilizzo della memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|nessuno|
|NetworkBytesTransmittedPerSecond|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|nessuno|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Conteggio TotalePullCount|Conteggio pull totale|Conteggio|Media|Numero totale di pull di immagini|nessuno|
|Conteggio Pull riuscito|Conteggio pull riuscito|Conteggio|Media|Numero di pull di immagini riuscite|nessuno|
|TotalPushCount|Conteggio push totale|Conteggio|Media|Numero totale di push di immagini|nessuno|
|SuccessfulPushCount|Conteggio push riuscito|Conteggio|Media|Numero di push di immagini riuscite|nessuno|
|Durata esecuzione|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|nessuno|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Media|Numero totale di core CPU disponibili in un cluster gestito|nessuno|
|kube_node_status_allocatable_memory_bytes|Numero totale di memoria disponibile in un cluster gestito|Byte|Media|Numero totale di memoria disponibile in un cluster gestito|nessuno|
|kube_pod_status_ready|Numero di pod in stato Pronto|Conteggio|Media|Numero di pod in stato Pronto|spazio dei nomi,pod|
|kube_node_status_condition|Stati per diverse condizioni dei nodi|Conteggio|Media|Stati per diverse condizioni dei nodi|condizione,stato,stato2,nodo|
|kube_pod_status_phase|Numero di pod per fase|Conteggio|Media|Numero di pod per fase|fase,spaziodeinomi,podphase,namespace,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SuccessfullRequests (Richieste riuscite)|Richieste riuscite|Conteggio|Totale|Richieste riuscite effettuate dal provider personalizzato|HttpMethod,CallPath,StatusCode|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Ottiene i log disponibili per i provider di risorse personalizzati|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|NICReadThroughput|Velocità effettiva di lettura (rete)Read Throughput (Network)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di report per tutti i volumi nel gateway.|InstanceName|
|NICWriteThroughput|Velocità effettiva di scrittura (rete)Write Throughput (Network)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di report per tutti i volumi nel gateway.|InstanceName|
|CloudReadThroughputPerShare|Velocità effettiva di download cloud (condivisione)Cloud Download Throughput (Share)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di creazione di report.|Condividi|
|CloudUploadThroughputPerShare|Velocità effettiva di caricamento cloud (condivisione)Cloud Upload Throughput (Share)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di creazione di report.|Condividi|
|ByteUploadedToCloudPerShare|Byte cloud caricati (condivisione)|Byte|Media|Numero totale di byte caricati in Azure da una condivisione durante il periodo di creazione di report.|Condividi|
|TotaleCapacità|Capacità totale|Byte|Media|Capacità totale|nessuno|
|AvailableCapacità|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di reporting.|nessuno|
|CloudUploadThroughput|Velocità effettiva di caricamento cloud|Byte al secondo|Media|Velocità effettiva di caricamento del cloud in Azure durante il periodo di creazione di report.|nessuno|
|CloudReadThroughput|Velocità effettiva di download cloud|Byte al secondo|Media|Velocità effettiva di download del cloud in Azure durante il periodo di creazione dei report.|nessuno|
|ByteUploadedToCloud|Byte cloud caricati (dispositivo)|Byte|Media|Numero totale di byte caricati in Azure da un dispositivo durante il periodo di creazione di report.|nessuno|
|HyperVVirtualProcessorUtilization (Utilizzo di HyperVVirtualProcessor)|Calcolo Edge - Percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|Utilizzo di HyperVMemory|Calcolo Edge - Utilizzo della memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuzione delle risorse per classificazione|Conteggio|Totale|Indica il numero di attività a cui è assegnata una determinata classificazione, ovvero sono classificati con tale etichetta.|Classificazione,Fonte|
|AssetDistributionByStorageType|Distribuzione delle risorse per tipo di risorsa di archiviazione|Conteggio|Totale|Indica il numero di risorse con un determinato tipo di archiviazione.|StorageType|
|NumberOfAssetsWithClassifications|Numero di attività con almeno una classificazione|Conteggio|Media|Indica il numero di risorse con almeno una classificazione tag.|nessuno|
|ScanCancelled|Scansione annullata|Conteggio|Totale|Indica il numero di scansioni annullate.|nessuno|
|ScanCompleted (Completato)|Scansione completata|Conteggio|Totale|Indica il numero di analisi completate.|nessuno|
|ScanFailed (ScanFailed)|Scansione non riuscita|Conteggio|Totale|Indica che il numero di scansioni non è riuscita.|nessuno|
|ScansioneTimeTaken|Tempo di scansione impiegato|Secondi|Totale|Indica il tempo totale di scansione in secondi.|nessuno|
|CatalogActiveUsers|Utenti attivi giornalieri|Conteggio|Totale|Numero di utenti attivi al giorno|nessuno|
|CatalogUsage|Distribuzione dell'utilizzo per operazione|Conteggio|Totale|Indicare il numero di operazioni che l'utente effettua al catalogo, ad esempio Accesso, Ricerca, Glossario.|Operazione|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FailedRuns|Esecuzioni non riuscite|Conteggio|Totale||pipelineName,nomeattività|
|SuccessfulRuns|Esecuzioni riuscite|Conteggio|Totale||pipelineName,nomeattività|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||FailureType,Nome|
|PipelineSucceededRuns|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||FailureType,Nome|
|PipelineCancelledRuns|Metriche di esecuzione della pipeline annullata|Conteggio|Totale||FailureType,Nome|
|ActivityFailedRuns|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||ActivityType,NomePipeline,TipoErrore,Nome|
|ActivitySucceededRuns|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||ActivityType,NomePipeline,TipoErrore,Nome|
|ActivityCancelledRuns|L'attività annullata esegue le metriche|Conteggio|Totale||ActivityType,NomePipeline,TipoErrore,Nome|
|TriggerFailedRuns|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Nome,TipoErrore|
|TriggerSucceededRuns|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Nome,TipoErrore|
|TriggerCancelledRuns (TriggerCancelledRuns)|Trigger annullato esegue le metriche|Conteggio|Totale||Nome,TipoErrore|
|IntegrationRuntimeCpuPercentage|Uso della CPU di Integration runtime|Percentuale|Media||IntegrationRuntimeName,NomeNodo|
|IntegrationRuntimeAvailableMemory|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName,NomeNodo|
|IntegrationRuntimeAverageTaskPickupDelayDelayDelay|Durata coda runtime di integrazione|Secondi|Media||IntegrationRuntimeName (NomeRuntime)|
|IntegrationRuntimeQueueLength|Lunghezza coda runtime di integrazione|Conteggio|Media||IntegrationRuntimeName (NomeRuntime)|
|NumerodinoDiIntegrationRuntimeAvailableNodeNumber|Conteggio dei nodi disponibili del runtime di integrazione|Conteggio|Media||IntegrationRuntimeName (NomeRuntime)|
|MaxAllowedResourceCount|Numero massimo di entità consentite|Conteggio|Massimo||nessuno|
|MaxAllowedFactorySizeInGbUnits|Dimensioni massime di fabbrica consentite (unità GB)|Conteggio|Massimo||nessuno|
|Conteggio risorse|Conteggio entità totali|Conteggio|Massimo||nessuno|
|FactorySizeInGbUnits|Dimensioni totali dello stabilimento (unità GB)|Conteggio|Massimo||nessuno|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|JobEndedSuccess|Processi completati|Conteggio|Totale|Numero di processi completati|nessuno|
|JobEndedFailure|Processi non riusciti|Conteggio|Totale|Numero di processi non riusciti|nessuno|
|JobEndedCancelled|Processi annullati|Conteggio|Totale|Numero di processi annullati|nessuno|
|JobAUEndedSuccess|Tempo di aggiornamenti automatici con esito positivo|Secondi|Totale|Tempo totale di aggiornamenti automatici per i processi completati|nessuno|
|JobAUEndedFailure|Tempo di aggiornamenti automatici non riusciti|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi non riusciti|nessuno|
|JobAUEndedCancelled|Tempo di aggiornamenti automatici annullati|Secondi|Totale|Tempo totale di aggiornamenti automatici per processi annullati|nessuno|
|JobStage (Scena lavoro)|Lavori in fase|Conteggio|Totale|Numero di lavori in ogni fase.|nessuno|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TotalStorage|Spazio di archiviazione totale|Byte|Massimo|Quantità totale di dati archiviati nell'account|nessuno|
|DataWritten|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|nessuno|
|DataRead|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|nessuno|
|WriteRequests|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|nessuno|
|ReadRequests|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|nessuno|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Conteggio condivisione|Condivisioni inviate|Conteggio|Massimo|Numero di azioni inviate nel conto|ShareName|
|ShareSubscriptionCountShareSubscriptionCount|Azioni ricevute|Conteggio|Massimo|Numero di azioni ricevute nel conto|ShareSubscriptionName|
|SucceededShareSynchronizations|Snapshot della condivisione inviata completati|Conteggio|Conteggio|Numero di snapshot della condivisione inviata riusciti nell'account|nessuno|
|FailedShareSynchronizations|Snapshot di snapshot non riusciti della condivisione inviata|Conteggio|Conteggio|Numero di snapshot di condivisione inviata non riuscita nell'account|nessuno|
|SucceededShareSubscriptionSynchronizations|Snapshot di condivisione ricevuti riusciti|Conteggio|Conteggio|Numero di snapshot della condivisione ricevuta riusciti nell'account|nessuno|
|FailedShareSubscriptionSynchronizationsFailedShareSubscriptionSynchronizations|Snapshot di condivisione ricevuti non riusciti|Conteggio|Conteggio|Numero di snapshot di condivisione ricevuti non riusciti nell'account|nessuno|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|nessuno|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|nessuno|
|seconds_behind_master|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|nessuno|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|nessuno|
|io_consumption_percent|Percentuale IO|Percentuale|Media|Percentuale IO|nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|nessuno|
|storage_limit|Limite archiviazione|Byte|Massimo|Limite archiviazione|nessuno|
|serverlog_storage_percent|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|nessuno|
|serverlog_storage_usage|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|nessuno|
|serverlog_storage_limit|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|nessuno|
|backup_storage_used|Archiviazione di backup utilizzata|Byte|Media|Archiviazione di backup utilizzata|nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|nessuno|
|pg_replica_log_delay_in_seconds|Replica Lag (Ritardo replica)|Secondi|Massimo|Ritardo della replica in pochi secondi|nessuno|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massimo|Ritardo in byte della replica più in ritardo|nessuno|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|nessuno|
|Iops|IOPS|Conteggio|Media|Operazioni I/O al secondo|nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|nessuno|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singoliserver

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|memory_percent|Percentuale memoria|Percentuale|Media|Percentuale memoria|nessuno|
|Iops|IOPS|Conteggio|Media|Operazioni I/O al secondo|nessuno|
|storage_percent|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|nessuno|
|storage_used|Uso archiviazione|Byte|Media|Uso archiviazione|nessuno|
|active_connections|Connessioni attive|Conteggio|Media|Connessioni attive|nessuno|
|network_bytes_egress|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|nessuno|
|network_bytes_ingress|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|nessuno|
|connections_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|nessuno|
|connections_succeeded|Connessioni completate|Conteggio|Totale|Connessioni completate|nessuno|
|maximum_used_transactionIDs|Numero massimo di ID transazione utilizzati|Conteggio|Media|Numero massimo di ID transazione utilizzati|nessuno|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|nessuno|
|d2c.telemetry.ingress.success|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|nessuno|
|c2d.commands.egress.complete.success|Recapiti di messaggi C2D completati|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati correttamente dal dispositivo|nessuno|
|c2d.commands.egress.abandon.success|Messaggi C2D abbandonati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|nessuno|
|c2d.commands.egress.reject.success|Messaggi C2D rifiutati|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|nessuno|
|C2DMessagesExpired|Messaggi C2D scaduti (anteprima)C2D Messages Expired (preview)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|nessuno|
|devices.totalDevices|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|devices.connectedDevices.allProtocol|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|nessuno|
|d2c.telemetry.egress.success|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|nessuno|
|d2c.telemetry.egress.dropped|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|nessuno|
|d2c.telemetry.egress.orphaned|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |nessuno|
|d2c.telemetry.egress.invalid|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|nessuno|
|d2c.telemetry.egress.fallback|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|nessuno|
|d2c.endpoints.egress.eventHubs|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|nessuno|
|d2c.endpoints.latency.eventHubs|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|nessuno|
|d2c.endpoints.egress.serviceBusQueues|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|nessuno|
|d2c.endpoints.latency.serviceBusQueues|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|nessuno|
|d2c.endpoints.egress.serviceBusTopics|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|nessuno|
|d2c.endpoints.latency.serviceBusTopics|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|nessuno|
|d2c.endpoints.egress.builtIn.events|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|nessuno|
|d2c.endpoints.latency.builtIn.events|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|nessuno|
|d2c.endpoints.egress.storage|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|nessuno|
|d2c.endpoints.latency.storage|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|nessuno|
|d2c.endpoints.egress.storage.bytes|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|nessuno|
|d2c.endpoints.egress.storage.blobs|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|nessuno|
|EventGridDeliveries|Consegne griglia di eventi (anteprima)Event Grid deliveries (preview)|Conteggio|Totale|Numero di eventi dell'hub IoT pubblicati in Griglia di eventi. Utilizzare la dimensione Risultato per il numero di richieste riuscite e non riuscite. La dimensione EventType indicahttps://aka.ms/ioteventgrid)il tipo di evento ( .|ResourceId,Risultato,TipoEvento|
|EventGridLatency (EventGridLatency)|Latenza griglia eventi (anteprima)Event Grid latency (preview)|Millisecondi|Media|Latenza media (millisecondi) da quando è stato generato l'evento Dell'hub Iot al momento in cui l'evento è stato pubblicato nella griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|ResourceId,TipoEvento|
|RoutingConsegne|Consegne di instradamento (anteprima)Routing Deliveries (preview)|Millisecondi|Totale|Numero di tentativi di recapito dei messaggi a tutti gli endpoint da parte dell'hub IoT tramite routing. Per visualizzare il numero di tentativi riusciti o non riusciti, utilizzare la dimensione Risultato. Per visualizzare il motivo dell'errore, ad esempio non valido, eliminato o orfano, utilizzare la dimensione FailureReasonCategory. È anche possibile usare le dimensioni EndpointName ed EndpointType per comprendere quanti messaggi sono stati recapitati ai diversi endpoint. Il valore della metrica aumenta di uno per ogni tentativo di recapito, anche se il messaggio viene recapitato a più endpoint o se il messaggio viene recapitato allo stesso endpoint più volte.|ResourceId,EndpointType,EndpointName,FailureReasonCategory,Result,RoutingSource|
|RoutingDeliveryLatency (Latenza recapito)|Latenza recapito routing (anteprima)Routing Delivery Latency (preview)|Millisecondi|Media|Latenza media (millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio di telemetria in un endpoint. È possibile usare le dimensioni EndpointName ed EndpointType per comprendere la latenza verso i diversi endpoint.|ResourceId,EndpointType,EndpointName,RoutingSource|
|d2c.twin.read.success|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|d2c.twin.read.failure|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|nessuno|
|d2c.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|d2c.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|nessuno|
|d2c.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|nessuno|
|d2c.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|nessuno|
|c2d.methods.success|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|nessuno|
|c2d.methods.failure|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|nessuno|
|c2d.methods.requestSize|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|nessuno|
|c2d.methods.responseSize|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|nessuno|
|c2d.twin.read.success|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|c2d.twin.read.failure|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|nessuno|
|c2d.twin.read.size|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|c2d.twin.update.success|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|nessuno|
|c2d.twin.update.failure|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|nessuno|
|c2d.twin.update.size|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|nessuno|
|twinQueries.success|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|nessuno|
|twinQueries.failure|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|nessuno|
|twinQueries.resultSize|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|nessuno|
|jobs.createTwinUpdateJob.success|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|nessuno|
|jobs.createTwinUpdateJob.failure|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|nessuno|
|jobs.createDirectMethodJob.success|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|nessuno|
|jobs.createDirectMethodJob.failure|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|nessuno|
|jobs.listJobs.success|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|nessuno|
|jobs.listJobs.failure|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|nessuno|
|jobs.cancelJob.success|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|nessuno|
|jobs.cancelJob.failure|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|nessuno|
|jobs.queryJobs.success|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|nessuno|
|jobs.queryJobs.failure|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|nessuno|
|jobs.completed|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|nessuno|
|jobs.failed|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|nessuno|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|nessuno|
|dailyMessageQuotaUsed|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Media|Numero totale di messaggi usati nella data odierna|nessuno|
|deviceDataUsage|Utilizzo totale dei dati del dispositivo|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|deviceDataUsageV2|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|totalDeviceCount|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|connectedDeviceCount|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|nessuno|
|configurazioni|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|nessuno|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName,IotHubName,Stato|
|DeviceAssignments|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName,IotHubName (Nome ServizioDiale)<a1>IotHubName</a1|
|AttestationAttempts|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName,Stato,Protocollo|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AddRegion|Regione aggiunta|Conteggio|Conteggio|Regione aggiunta|Region|
|AvailableStorage|Spazio di archiviazione disponibile|Byte|Totale|Spazio di archiviazione disponibile totale riportato con una granularità di 5 minuti|NomeRaccolta,NomeDatabase,Regione|
|CassandraConnectionClosures|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni Cassandra che sono state chiuse, segnalate con una granularità di 1 minuto|TIPOAPI,Regione,ClosureReason|
|CassandraKeyspaceCan|Cassandra Keyspace Eliminato|Conteggio|Conteggio|Cassandra Keyspace Eliminato|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Velocità effettiva dello spazio chiave Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva dello spazio chiave Cassandra aggiornata|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Keyspace Aggiornato|Conteggio|Conteggio|Cassandra Keyspace Aggiornato|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Addebiti richieste Cassandra|Conteggio|Totale|RU consumate per le richieste Cassandra effettuate|Tipo API,NomeDatabase,NomeRaccolta,Regione,TipoOperazione,TipoRisorsa|
|CassandraRequests|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste Cassandra effettuate|Tipo API,NomeDatabase,NomeRaccolta,Regione,TipoOperazione,TipoRisorsa,CodiceErrore|
|CassandraTableDelete|Cassandra Tabella Eliminato|Conteggio|Conteggio|Cassandra Tabella Eliminato|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,TipoOperazione|
|CassandraTableThroughputUpdate|Velocità effettiva tabella Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva tabella Cassandra aggiornata|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate (aggiornamento tabella di Cassandra)|Cassandra Tabella aggiornata|Conteggio|Conteggio|Cassandra Tabella aggiornata|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CreateAccount|Account creato|Conteggio|Conteggio|Account creato|nessuno|
|DataUsage|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati riportato con una granularità di 5 minuti|NomeRaccolta,NomeDatabase,Regione|
|DeleteAccount (AccountEliminazione)|Account eliminato|Conteggio|Conteggio|Account eliminato|nessuno|
|DocumentCount|Conteggio documenti|Conteggio|Totale|Conteggio totale dei documenti riportato con una granularità di 5 minuti|NomeRaccolta,NomeDatabase,Regione|
|DocumentQuota|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalata con una granularità di 5 minuti|NomeRaccolta,NomeDatabase,Regione|
|GremlinDatabaseDelete|Gremlin Database Eliminato|Conteggio|Conteggio|Gremlin Database Eliminato|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughputUpdate|Velocità effettiva database Gremlin aggiornata|Conteggio|Conteggio|Velocità effettiva database Gremlin aggiornata|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate (aggiornamento)|Database Gremlin aggiornato|Conteggio|Conteggio|Database Gremlin aggiornato|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphElimina|Gremlin Grafico Eliminato|Conteggio|Conteggio|Gremlin Grafico Eliminato|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,TipoOperazione|
|GremlinGraphThroughputUpdate|Gremlin Graph Throughput Updated|Conteggio|Conteggio|Gremlin Graph Throughput Updated|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Gremlin Grafico Aggiornato|Conteggio|Conteggio|Gremlin Grafico Aggiornato|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndexUsage|Utilizzo indice|Byte|Totale|Utilizzo totale dell'indice riportato con granularità di 5 minuti|NomeRaccolta,NomeDatabase,Regione|
|MetadataRequests|Metadata Requests (Richieste di metadati)|Conteggio|Conteggio|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, consentendo di enumerare le raccolte, i database e così via e le relative configurazioni gratuitamente.|DatabaseName,CollectionName,Region,StatusCode,Role|
|MongoCollectionDelete|Raccolta Mongo eliminata|Conteggio|Conteggio|Raccolta Mongo eliminata|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,TipoOperazione|
|MongoCollectionThroughputUpdate|Velocità effettiva raccolta Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva raccolta Mongo aggiornata|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Collezione Mongo aggiornata|Conteggio|Conteggio|Collezione Mongo aggiornata|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseUpdate (aggiornamento del database di MongoDB)|Database Mongo aggiornato|Conteggio|Conteggio|Database Mongo aggiornato|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Database Mongo eliminato|Conteggio|Conteggio|Database Mongo eliminato|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Velocità effettiva del database Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva del database Mongo aggiornata|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|Mongo Request Charge (Addebito richiesta Mongo)|Conteggio|Totale|Unità richiesta Mongo utilizzate|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore,Stato|
|MongoRequests|Richieste Mongo|Conteggio|Conteggio|Numero di richieste Mongo eseguite|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore,Stato|
|MongoRequestsCount|Frequenza richieste Mongo|Conteggio al secondo|Media|Conteggio richieste Mongo al secondo|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore|
|MongoRequestsDelete|Frequenza richieste eliminazione mongo|Conteggio al secondo|Media|Richiesta di eliminazione Mongo al secondo|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore|
|MongoRequestsInsert|Frequenza richieste inserimento Mongo|Conteggio al secondo|Media|Conteggio inserimenti Mongo al secondo|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore|
|MongoRequestsQuery|Frequenza richieste query Mongo|Conteggio al secondo|Media|Richiesta di query Mongo al secondo|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore|
|MongoRequestsUpdate|Frequenza richieste aggiornamento Mongo|Conteggio al secondo|Media|Richiesta di aggiornamento Mongo al secondo|NomeDatabase,NomeRaccolta,Regione,NomeComando,CodiceErrore|
|NormalizzatoRUConsumption|Consumo RU normalizzato|Percentuale|Massimo|Percentuale di consumo massimo RU al minuto|NomeRaccolta,NomeDatabase,Regione|
|ProvisionedThroughput|Velocità effettiva sottoposta a provisioning|Conteggio|Massimo|Velocità effettiva sottoposta a provisioning|NomeDatabase,NomeRaccolta|
|RegionFailover (Failover di regione)|Area su cui è stato eseguito il failover|Conteggio|Conteggio|Area su cui è stato eseguito il failover|nessuno|
|RemoveRegion|Regione rimossa|Conteggio|Conteggio|Regione rimossa|Region|
|ReplicationLatency|Latenza di replica P99|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion,TargetRegion|
|ServerSideLatency (Latenza server)ServerSideLatency|Latenza lato server|Millisecondi|Media|Latenza lato server|NomeDatabase,NomeRaccolta,Regione,ModalitàConnessione,TipoOperazione,PublicAPIType|
|ServiceAvailability|Disponibilità del servizio|Percentuale|Media|Disponibilità delle richieste dell'account a una granularità di un'ora, giorno o mese|nessuno|
|SqlContainerDelete (Eliminazionecontenitore)|Contenitore Sql eliminato|Conteggio|Conteggio|Contenitore Sql eliminato|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,TipoOperazione|
|SqlContainerThroughputUpdate|Sql Container Throughput Updated|Conteggio|Conteggio|Sql Container Throughput Updated|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Contenitore Sql aggiornato|Conteggio|Conteggio|Contenitore Sql aggiornato|NomeRisorsa,NomeRisorsaFiglio,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete (Istruzione in lingua inglese)|Database SQL eliminato|Conteggio|Conteggio|Database SQL eliminato|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdateSqlDatabaseThroughputUpdate|Sql Database Throughput Updated|Conteggio|Conteggio|Sql Database Throughput Updated|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate (aggiornamento sql database)|Database SQL aggiornato|Conteggio|Conteggio|Database SQL aggiornato|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete (Eliminazione tabella)|Tabella AzureTable eliminata|Conteggio|Conteggio|Tabella AzureTable eliminata|ResourceName,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdateTableTableThroughputUpdate|AzureTable Table Throughput Updated|Conteggio|Conteggio|AzureTable Table Throughput Updated|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableUpdate (aggiornamento di Tabella)|Tabella AzureTable aggiornata|Conteggio|Conteggio|Tabella AzureTable aggiornata|ResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotalRequestUnits|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|NomeDatabase,NomeRaccolta,Regione,CodiceStato,TipoOperazione,Stato|
|TotalRequests|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|NomeDatabase,NomeRaccolta,Regione,CodiceStato,TipoOperazione,Stato|
|UpdateAccountKeys (Chiave di aggiornamento)|Chiavi account aggiornate|Conteggio|Conteggio|Chiavi account aggiornate|KeyType|
|UpdateAccountNetworkSettings|Impostazioni di rete dell'account aggiornate|Conteggio|Conteggio|Impostazioni di rete dell'account aggiornate|nessuno|
|UpdateAccountReplicationSettings|Impostazioni di replica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di replica dell'account aggiornate|nessuno|
|UpdateDiagnosticsSettings|Impostazioni di diagnostica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di diagnostica dell'account aggiornate|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/servizi

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Conteggio transazioni|Conteggio transazioni|Conteggio|Conteggio|Conteggio totale transazioni|Conteggio transazioni|
|SuccessCount|Success Count|Conteggio|Conteggio|Conteggio transazioni riuscite|SuccessCount|
|Conteggio errori|Failure Count|Conteggio|Conteggio|Conteggio transazioni non riuscite|Conteggio errori|
|Operazione riuscitaLatenzaSuccessLatency|Latenza di successo|Millisecondi|Media|Latenza delle transazioni riuscite|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domini

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishFailCount|Pubblicazione degli eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento,TipoErrore,Errore|
|PubblicarESuccessoLatencyInMs|Pubblicazione della latenza di successo|Millisecondi|Totale|Pubblicazione della latenza di successo in millisecondi|nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento,NomeSottoscrizioneEvento,NomeEventoDominio|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento,NomeSottoscrizioneEvento,NomeEventoSottoscrizioneDominio,Errore,TipoErrore|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento,NomeSottoscrizioneEvento,NomeEventoDominio|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento,NomeSottoscrizioneEvento,NomeEventoDominio|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|nessuno|
|PublishFailCount|Pubblicazione degli eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType,Errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|nessuno|
|PubblicarESuccessoLatencyInMs|Pubblicazione della latenza di successo|Millisecondi|Totale|Pubblicazione della latenza di successo in millisecondi|nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|NomeSottoscrizioneEvento|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore,TipoErrore,NomeSottoscrizioneEvento|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|NomeSottoscrizioneEvento|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|NomeSottoscrizioneEvento|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,NomeSottoscrizioneEvento|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName (Nome SottoscrizioneEvento)|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|nessuno|
|PublishFailCount|Pubblicazione degli eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType,Errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|nessuno|
|PubblicarESuccessoLatencyInMs|Pubblicazione della latenza di successo|Millisecondi|Totale|Pubblicazione della latenza di successo in millisecondi|nessuno|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|NomeSottoscrizioneEvento|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore,TipoErrore,NomeSottoscrizioneEvento|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|NomeSottoscrizioneEvento|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|NomeSottoscrizioneEvento|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason,NomeSottoscrizioneEvento|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason,EventSubscriptionName (Nome SottoscrizioneEvento)|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|MatchedEventCount|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|nessuno|
|DeliveryAttemptFailCount|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore,TipoErrore|
|DeliverySuccessCount|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|nessuno|
|DestinationProcessingDurationInMs|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|nessuno|
|DroppedEventCount|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Controllo DropReason|
|DeadLetteredCount|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|nessuno|
|PublishFailCount|Pubblicazione degli eventi non riusciti|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType,Errore|
|UnmatchedEventCount|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|nessuno|
|PubblicarESuccessoLatencyInMs|Pubblicazione della latenza di successo|Millisecondi|Totale|Pubblicazione della latenza di successo in millisecondi|nessuno|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|NomeEntità,RisultatoOperazione|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|NomeEntità,RisultatoOperazione|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|NomeEntità,RisultatoOperazione|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|NomeEntità,RisultatoOperazione|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|NomeEntità,RisultatoOperazione|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|INREQS|Richieste in ingresso (obsoleto)|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (obsoleto)Total incoming send requests for a namespace (Deprecated)|nessuno|
|SUCCREQ|Richieste riuscite (obsoleto)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (obsoleto)Total successful requests for a namespace (Deprecated)|nessuno|
|FAILREQ|Richieste non riuscite (obsoleto)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (obsoleto)Total failed requests for a namespace (Deprecated)|nessuno|
|SVRBSY|Errori di occupato del server (obsoleto)|Conteggio|Totale|Totale errori occupati al server per uno spazio dei nomi (obsoleto)|nessuno|
|INTERR|Errori interni del server (obsoleto)|Conteggio|Totale|Totale errori del server interno per uno spazio dei nomi (obsoleto)Total internal server errors for a namespace (Deprecated)|nessuno|
|MISCERR|Altri errori (obsoleto)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (obsoleto)Total failed requests for a namespace (Deprecated)|nessuno|
|INMSGS|Messaggi in arrivo (obsoleti) (obsoleto)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Utilizzare invece la metrica Messaggi in arrivo (obsoleto)|nessuno|
|EHINMSGS|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale dei messaggi in arrivo per uno spazio dei nomi (obsoleto)Total incoming messages for a namespace (Deprecated)|nessuno|
|OUTMSGS|Messaggi in uscita (obsoleto) (obsoleto)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Utilizzare invece la metrica Messaggi in uscita (obsoleto)|nessuno|
|EHOUTMSGS|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (obsoleto)|nessuno|
|EHINMBS|Byte in ingresso (obsoleto) (obsoleto)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Utilizzare invece la metrica Byte in ingresso (obsoleto)|nessuno|
|EHINBYTES|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'Hub eventi per uno spazio dei nomi (obsoleto)Event Hub incoming message throughput for a namespace (Deprecated)|nessuno|
|EHOUTMBS|Byte in uscita (obsoleto) (obsoleto)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Utilizzare invece la metrica Byte in uscita (obsoleto)|nessuno|
|EHOUTBYTES|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'Hub eventi per uno spazio dei nomi (obsoleto)Event Hub outgoing message throughput for a namespace (Deprecated)|nessuno|
|EHABL|Archiviare i messaggi di backlog (obsoleto)|Conteggio|Totale|Messaggi di archivio dell'Hub eventi nel backlog per uno spazio dei nomi (obsoleto)Event Hub archive messages in backlog for a namespace (Deprecated)|nessuno|
|EHAMSGS|Messaggi di archivio (obsoleto)|Conteggio|Totale|Messaggi archiviati nell'Hub eventi in uno spazio dei nomi (obsoleto)Event Hub archived messages in a namespace (Deprecated)|nessuno|
|EHAMBS|Velocità effettiva dei messaggi di archiviazione (obsoleto)Archive message throughput (Deprecated)|Byte|Totale|Velocità effettiva dei messaggi archiviati nell'Hub eventi in uno spazio dei nomi (obsoleto)Event Hub archived message throughput in a namespace (Deprecated)|nessuno|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|OperationResult|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|OperationResult|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|OperationResult|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|nessuno|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|nessuno|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|nessuno|
|IncomingBytes|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|nessuno|
|OutgoingBytes|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|nessuno|
|ActiveConnections|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|nessuno|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|nessuno|
|CaptureBacklog|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|nessuno|
|CapturedMessages|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|nessuno|
|CapturedBytes|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|nessuno|
|CPU|CPU|Percentuale|Massimo|Uso CPU per il cluster dell'hub eventi in percentuale|Ruolo|
|AvailableMemory|Memoria disponibile|Percentuale|Massimo|Memoria disponibile per il cluster dell'hub eventi come percentuale della memoria totale.|Ruolo|
|Dimensione|Dimensioni di un hub eventi in byte.|Byte|Media|Dimensioni di un hub eventi in byte.|Ruolo|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|GatewayRequests|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|HttpStatus|
|CategorizedGatewayRequests|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Numero di lavoratori attivi|Conteggio|Massimo|Numero di lavoratori attivi|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ObservedMetricValue|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|MetricThreshold|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|nessuno|
|ScaleActionsInitiated|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|availabilityRisultati/availabilityPercentuale|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityRisulta/nome,availabilityRisulta/posizione|
|availabilityRisultati/conteggio|Test della disponibilità|Conteggio|Conteggio|Numero di test di disponibilità|availabilityRisulta/nome,availabilityRisulta/posizione,availabilityResult/success|
|availabilityResults/duration|Durata del test di disponibilitàAvailability test duration|Millisecondi|Media|Durata del test di disponibilitàAvailability test duration|availabilityRisulta/nome,availabilityRisulta/posizione,availabilityResult/success|
|browserTimings/networkDuration|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|nessuno|
|browserTimings/processingDuration|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|nessuno|
|browserTimings/receiveDuration|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|nessuno|
|browserTimings/sendDuration|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|nessuno|
|browserTimings/totalDuration|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|nessuno|
|dependencies/count|Chiamate di dipendenza|Conteggio|Conteggio|Numero delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo,dipendenza/prestazioniBucket,dipendenza/successo,dipendenza/destinazione,dipendenza/codicerisultato,operazione/sintetico,cloud/roleInstance,cloud/nomeruolo|
|dependencies/duration|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo,dipendenza/prestazioniBucket,dipendenza/successo,dipendenza/destinazione,dipendenza/codicerisultato,operazione/sintetico,cloud/roleInstance,cloud/nomeruolo|
|dependencies/failed|Errori delle chiamate di dipendenzaDependency call failures|Conteggio|Conteggio|Numero di chiamate di dipendenza non riuscite eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo,dipendenza/prestazioniBucket,dipendenza/successo,dipendenza/destinazione,dipendenza/codicerisultato,operazione/sintetico,cloud/roleInstance,cloud/nomeruolo|
|pageViews/count|Visualizzazioni pagina|Conteggio|Conteggio|Numero delle visualizzazioni di pagina.|operazione/sintetico,cloud/nomeruolo|
|pageViews/duration|Tempo di caricamento della visualizzazione pagina|Millisecondi|Media|Tempo di caricamento della visualizzazione pagina|operazione/sintetico,cloud/nomeruolo|
|performanceCounters/requestExecutionTime|Tempo di esecuzione della richiesta HTTP|Millisecondi|Media|Tempo di esecuzione della richiesta più recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Richieste HTTP nella coda dell'applicazione|Conteggio|Media|Lunghezza della coda di richieste dell'applicazione.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Frequenza delle richieste HTTP|Conteggio al secondo|Media|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Frequenza di eccezioni|Conteggio al secondo|Media|Conteggio delle eccezioni gestite e non gestite restituite in Windows, incluse le eccezioni .NET e non gestite che vengono convertite in eccezioni .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Velocità di I/O di elaborazione|Byte al secondo|Media|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU processo|Percentuale|Media|Percentuale di tempo trascorso per l'uso del processore da parte di tutti i thread di processo per eseguire le istruzioni. Il valore può variare tra 0 e 100. Questa metrica indica le prestazioni del solo processo w3wp.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo processore|Percentuale|Media|Percentuale di tempo che il processore dedica a thread non inattivi.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memoria disponibile|Byte|Media|Memoria fisica immediatamente disponibile per l'allocazione a un processo o usabile dal sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Byte privati processo|Byte|Media|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.|cloud/roleInstance|
|requests/duration|Tempo di risposta del server|Millisecondi|Media|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/count|Richieste server|Conteggio|Conteggio|Numero delle richieste HTTP completate.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/failed|Richieste non riuscite|Conteggio|Conteggio|Numero delle richieste HTTP contrassegnate come non riuscite. Nella maggior parte dei casi si tratta di richieste con un codice di risposta maggiore o uguale a 400 e diverso da 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|richieste/tasso|Frequenza richieste server|Conteggio al secondo|Media|Frequenza delle richieste server al secondo|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|exceptions/count|Eccezioni|Conteggio|Conteggio|Numero combinato di tutte le eccezioni non rilevate.|cloud/nomeruolo,cloud/istanza-ruolo,client/tipo|
|exceptions/browser|Eccezioni del browser|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nel browser.|client/isServer,cloud/nomeruolo|
|exceptions/server|Eccezioni del server|Conteggio|Conteggio|Numero delle eccezioni non rilevate generate nell'applicazione server.|client/isServer,cloud/nomeruolo,cloud/istanza|
|traces/count|Traces|Conteggio|Conteggio|Numero documenti di analisi|trace/severityLevel,operazione/sintetico,cloud/nomeruolo,cloud/istanza|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|connectedDeviceCount|Totale dispositivi connessi|Conteggio|Media|Numero di dispositivi collegati a IoT Central|nessuno|
|c2d.property.read.success (informazioni in base al successo)|Letture delle proprietà dei dispositivi riuscite da IoT Central|Conteggio|Totale|Conteggio di tutte le letture di proprietà riuscite avviate da IoT Central|nessuno|
|c2d.property.read.failure|Letture delle proprietà del dispositivo non riuscite dal centro IoTFailed Device Property Reads from IoT Central|Conteggio|Totale|Conteggio di tutte le letture di proprietà non riuscite avviate da IoT Central|nessuno|
|file d2c.property.read.success|Letture delle proprietà dei dispositivi riuscite dai dispositivi|Conteggio|Totale|Numero di tutte le letture di proprietà riuscite avviate dai dispositivi|nessuno|
|d2c.property.read.failure|Letture delle proprietà del dispositivo non riuscite dai dispositiviFailed Device Property Reads from Devices|Conteggio|Totale|Numero di letture di proprietà non riuscite avviate dai dispositivi|nessuno|
|c2d.property.update.success|Aggiornamenti delle proprietà del dispositivo riusciti da IoT Central|Conteggio|Totale|Conteggio di tutti gli aggiornamenti delle proprietà riusciti avviati da IoT Central|nessuno|
|c2d.property.update.failure|Aggiornamenti delle proprietà del dispositivo non riusciti da IoT Central|Conteggio|Totale|Conteggio di tutti gli aggiornamenti delle proprietà non riusciti avviati da IoT Central|nessuno|
|d2c.property.update.success|Aggiornamenti delle proprietà dei dispositivi riusciti dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti delle proprietà riusciti avviati dai dispositivi|nessuno|
|d2c.property.update.failure|Aggiornamenti delle proprietà del dispositivo non riusciti dai dispositiviFailed Device Property Updates from Devices|Conteggio|Totale|Numero di tutti gli aggiornamenti delle proprietà non riusciti avviati dai dispositivi|nessuno|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServiceApiHit|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType,NomeAttività|
|ServiceApiLatency|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType,NomeAttività,CodiceStato,CodiceStato|
|ServiceApiResult|Totale dei risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType,NomeAttività,CodiceStato,CodiceStato|
|SaturationShoebox|Saturazione complessiva del Vault|Percentuale|Media|Capacità Vault utilizzata|ActivityType,NomeAttività,TransactionType|
|Disponibilità|Disponibilità complessiva del Vault|Percentuale|Media|Disponibilità delle richieste del Vault|ActivityType,NomeAttività,CodiceStato,CodiceStato|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CacheUtilization|Utilizzo della cache|Percentuale|Media|Livello di uso nell'ambito del cluster|nessuno|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata in secondi delle query|QueryStatus|
|IngestionUtilization|Utilizzo dell'ingestione|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|nessuno|
|KeepAlive|Mantieni vivo|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|nessuno|
|IngestionVolumeInMB|Volume di ingestione (in MB)|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Database|
|IngestionLatencyInSeconds (IngestionLatencyInSeconds)|Latenza di ingestione (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|nessuno|
|EventiProcessedForEventHubsEventsProcessedForEventHubs|Eventi elaborati (per hub evento/IoT)|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub evento/IoT|EventStatus|
|IngestionResult|Risultato dell'ingestione|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|CPU|CPU|Percentuale|Media|Livello di uso della CPU|nessuno|
|ContinuousExportNumOfRecordsEsportaed|Esportazione continua: numero di record esportati|Conteggio|Totale|Numero di record esportati, generati per ogni elemento di archiviazione scritto durante l'operazione di esportazione|ContinuousExportName,Database|
|EsportazioneUtilizzo|Utilizzo esportazione|Percentuale|Massimo|Utilizzo delle esportazioni|nessuno|
|ContinuousExportPendingCount|Conteggio in sospeso esportazione continua|Conteggio|Massimo|Numero di processi di esportazione continua in sospeso pronti per l'esecuzione|nessuno|
|ContinuousExportMaxLatenessMinutes|Esportazione continua massima ritardo|Conteggio|Massimo|Il ritardo (in minuti) riportato dai processi di esportazione continua nel cluster|nessuno|
|ContinuousExportResult|Risultato esportazione continua|Conteggio|Conteggio|Indica se l'esportazione continua ha avuto esito positivo o negativo|ContinuousExportName,Risultato,Database|
|StreamingIngestDuration|Durata dell'inserimento in streaming|Millisecondi|Media|Durata dell'inserimento in streaming in millisecondi|nessuno|
|StreamingIngestDataRate (StreamingIngestDataRate)|Velocità dati di inserimento in streamingStreaming Ingest Data Rate|Conteggio|Media|Velocità dati di inserimento in streaming (MB al secondo)|nessuno|
|SteamingIngestRequestRate|Frequenza richieste inserimento in streaming|Conteggio|RateRequestsPerSecond|Frequenza richieste inserimento in streaming (richieste al secondo)Streaming ingest request rate (requests per second)|nessuno|
|StreamingIngestResults (StreamingIngestResults)|Risultato dell'inserimento in streaming|Conteggio|Media|Risultato dell'inserimento in streaming|Risultato|
|TotalNumberOfConcurrentQueries|Numero totale di query simultanee|Conteggio|Totale|Numero totale di query simultanee|nessuno|
|TotalNumberOfThrottledQueries|Numero totale di query limitate|Conteggio|Totale|Numero totale di query limitate|nessuno|
|TotalNumberOfThrottledCommands|Numero totale di comandi limitati|Conteggio|Totale|Numero totale di comandi limitati|CommandType|
|TotalNumberOfExtents|Numero totale di estensioni|Conteggio|Totale|Numero totale di estensioni di dati|nessuno|
|InstanceCount|Conteggio istanze|Conteggio|Media|Numero totale di istanze|nessuno|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|nessuno|
|RunStartThrottledEvents|Eventi gestiti dall'avvio dell'esecuzioneRun Start Throttled Events|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|nessuno|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|nessuno|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|nessuno|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|nessuno|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|nessuno|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|nessuno|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|nessuno|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|nessuno|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|nessuno|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|nessuno|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|nessuno|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|nessuno|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|nessuno|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|nessuno|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|nessuno|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|nessuno|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|nessuno|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|nessuno|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|nessuno|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|nessuno|
|BillableActionExecutions|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|nessuno|
|BillableTriggerExecutions|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|nessuno|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|nessuno|
|BillingUsageNativeOperation|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|nessuno|
|BillingUsageStandardConnector|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|nessuno|
|BillingUsageStorageConsumption|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|nessuno|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RunsStarted|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|nessuno|
|RunsCompleted|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|nessuno|
|RunsSucceeded|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|nessuno|
|RunsFailed|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|nessuno|
|RunsCancelled|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|nessuno|
|RunLatency|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|nessuno|
|RunSuccessLatency|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|nessuno|
|RunThrottledEvents|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|nessuno|
|RunStartThrottledEvents|Eventi gestiti dall'avvio dell'esecuzioneRun Start Throttled Events|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|nessuno|
|RunFailurePercentage|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|nessuno|
|ActionsStarted|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|nessuno|
|ActionsCompleted|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|nessuno|
|ActionsSucceeded|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|nessuno|
|ActionsFailed|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|nessuno|
|ActionsSkipped|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|nessuno|
|ActionLatency|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|nessuno|
|ActionSuccessLatency|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|nessuno|
|ActionThrottledEvents|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|nessuno|
|TriggersStarted|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|nessuno|
|TriggersCompleted|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|nessuno|
|TriggersSucceeded|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|nessuno|
|TriggersFailed|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|nessuno|
|TriggersSkipped|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|nessuno|
|TriggersFired|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|nessuno|
|TriggerLatency|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|nessuno|
|TriggerFireLatency|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|nessuno|
|TriggerSuccessLatency|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|nessuno|
|TriggerThrottledEvents|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|nessuno|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazioneWorkflow Processor Usage for Integration Service Environment|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazione.|nessuno|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazioneWorkflow Memory Usage for Integration Service Environment|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazione.|nessuno|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|nessuno|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|nessuno|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Esecuzioni annullate|Esecuzioni annullate|Conteggio|Totale|Numero di esecuzioni annullate per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Annulla esecuzioni richieste|Annulla esecuzioni richieste|Conteggio|Totale|Numero di esecuzioni in cui è stata richiesta l'annullamento per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Esecuzioni completate|Esecuzioni completate|Conteggio|Totale|Numero di esecuzioni completate correttamente per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Esecuzioni non riuscite|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Finalizzazione delle esecuzioni|Finalizzazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni immesse stato di finalizzazione per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Non risponde alle esecuzioni|Non risponde alle esecuzioni|Conteggio|Totale|Numero di esecuzioni non risponde per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Esecuzioni non avviate|Esecuzioni non avviate|Conteggio|Totale|Numero di esecuzioni nello stato non avviato per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Preparazione delle esecuzioni|Preparazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni che vengono preparate per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Esecuzioni di provisioning|Esecuzioni di provisioning|Conteggio|Totale|Numero di esecuzioni di cui viene eseguito il provisioning per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Esecuzioni in coda|Esecuzioni in coda|Conteggio|Totale|Numero di esecuzioni accodate per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Corse avviate|Corse avviate|Conteggio|Totale|Numero di esecuzioni avviate per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Avvio delle esecuzioni|Avvio delle esecuzioni|Conteggio|Totale|Numero di esecuzioni avviate per questa area di lavoro|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Errors|Errors|Conteggio|Totale|Numero di errori di esecuzione in questa area di lavoro|Scenario|
|Avvisi|Avvisi|Conteggio|Totale|Numero di avvisi di esecuzione in questa area di lavoro|Scenario|
|Registro modello riuscito|Registro modello riuscito|Conteggio|Totale|Numero di registrazioni di modelli riuscite in questa area di lavoro|Scenario|
|Registro modello non riuscito|Registro modello non riuscito|Conteggio|Totale|Numero di registrazioni di modelli non riuscite in questa area di lavoro|Scenario,StatusCode|
|Distribuzione del modello avviata|Distribuzione del modello avviata|Conteggio|Totale|Numero di distribuzioni di modelli avviate in questa area di lavoro|Scenario|
|Distribuzione del modello completata|Distribuzione del modello completata|Conteggio|Totale|Numero di distribuzioni di modelli riuscite in questa area di lavoro|Scenario|
|Distribuzione del modello non riuscita|Distribuzione del modello non riuscita|Conteggio|Totale|Numero di distribuzioni di modelli non riuscite in questa area di lavoro|Scenario,StatusCode|
|Nodi totali|Nodi totali|Conteggio|Media|Numero di nodi totali. Questo totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi precedente, lasciando i nodi|Scenario,NomeCluster|
|Nodi attivi|Nodi attivi|Conteggio|Media|Numero di nodi Acitve. Questi sono i nodi che eseguono attivamente un processo.|Scenario,NomeCluster|
|Nodi inattivi|Nodi inattivi|Conteggio|Media|Numero di nodi inattivi. I nodi inattivi sono i nodi che non eseguono alcun processo, ma possono accettare nuovi processi, se disponibili.|Scenario,NomeCluster|
|Nodi inutilizzabili|Nodi inutilizzabili|Conteggio|Media|Numero di nodi inutilizzabili. I nodi inutilizzabili non sono funzionali a causa di un problema irrisolvibile. Azure riciclerà questi nodi.|Scenario,NomeCluster|
|Nodi anticipati|Nodi anticipati|Conteggio|Media|Numero di nodi con preempted. Questi nodi sono i nodi con priorità bassa che vengono rimossi dal pool di nodi disponibili.|Scenario,NomeCluster|
|Dati in uscita|Dati in uscita|Conteggio|Media|Numero di nodi in uscita. I nodi in uscita sono i nodi che hanno appena terminato l'elaborazione di un processo e passeranno allo stato Inattivo.|Scenario,NomeCluster|
|Nuclei totali|Nuclei totali|Conteggio|Media|Numero di core totali|Scenario,NomeCluster|
|Nuclei attivi|Nuclei attivi|Conteggio|Media|Numero di core attivi|Scenario,NomeCluster|
|Core inattivi|Core inattivi|Conteggio|Media|Numero di core inattivi|Scenario,NomeCluster|
|Core inutilizzabili|Core inutilizzabili|Conteggio|Media|Numero di core inutilizzabili|Scenario,NomeCluster|
|Nuclei preempted|Nuclei preempted|Conteggio|Media|Numero di core anticipati|Scenario,NomeCluster|
|Lasciare i nuclei|Lasciare i nuclei|Conteggio|Media|Numero di nuclei di uscita|Scenario,NomeCluster|
|Percentuale di utilizzo della quota|Percentuale di utilizzo della quota|Conteggio|Media|Percentuale di quota utilizzata|Scenario,NomeCluster,VmFamilyName,VmPriority|
|Utilizzo Cpu|Utilizzo Cpu|Conteggio|Media|CPU (anteprima)|Scenario,runId,NodeId,CreatedTime|
|GpuUtilizzo|GpuUtilizzo|Conteggio|Media|GPU (anteprima)|Scenario,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Uso|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory,ApiName,ResultType,ResponseCode|
|Disponibilità|Disponibilità|Percentuale|Media|Disponibilità delle API|CategoriaApi,NomeApi|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/Mediaservices/streamingEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Egress|Egress|Byte|Totale|Quantità di dati Egress, in byte.|OutputFormat|
|SuccessE2ELatency|Latenza end-to-end riuscita|Millisecondi|Media|Latenza media per le richieste riuscite in millisecondi.|OutputFormat|
|Requests|Requests|Conteggio|Totale|Richieste a un endpoint di streaming.|FormatoOutput,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/Mediaservices (Servizi multimediali)

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetQuota|Quota di attività|Conteggio|Media|Numero di risorse consentite per l'account del servizio multimediale corrente|nessuno|
|AssetCount (conteggio degli elementi)|Conteggio risorse|Conteggio|Media|Numero di risorse già create nell'account del servizio multimediale corrente|nessuno|
|AssetQuotaUsedPercentual|Quota di attività utilizzata percentuale|Percentuale|Media|Percentuale utilizzata nell'asset nell'account del servizio multimediale corrente|nessuno|
|ContentKeyPolicyQuota|Quota dei criteri della chiave simmetrica|Conteggio|Media|Il numero di criteri chiave del contenuto consentiti per l'account del servizio multimediale corrente|nessuno|
|ContentKeyPolicyCount|Conteggio criteri chiave simmetrica|Conteggio|Media|Numero di criteri chiave simmetrica già creati nell'account del servizio multimediale corrente|nessuno|
|ContentKeyPolicyQuotaUsedPercentage|Percentuale di quota utilizzata per la chiave di contenuto|Percentuale|Media|Criteri chiave simmetrica utilizzati percentuale nell'account del servizio multimediale corrente|nessuno|
|StreamingPolicyQuota|Quota di politica di streaming|Conteggio|Media|Numero di criteri di streaming consentiti per l'account del servizio multimediale corrente|nessuno|
|StreamingPolicyCount|Conteggio criteri di streaming|Conteggio|Media|Numero di criteri di streaming già creati nell'account del servizio multimediale corrente|nessuno|
|StreamingPolicyQuotaUsedPercentage|Percentuale di quota utilizzata per i criteri di streaming|Percentuale|Media|Percentuale utilizzata dai criteri di streaming nell'account del servizio multimediale corrente|nessuno|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetsConverted|Attività convertite|Conteggio|Totale|Numero totale di attività convertite|AppId,ResourceId,SDKVersion|
|ActiveRenderingSessions|Sessioni di rendering attive|Conteggio|Totale|Numero totale di sessioni di rendering attive|AppId,ResourceId,SessionType,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AverageReadLatency|Latenza media in lettura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di lettura|nessuno|
|AverageWriteLatency|Latenza media in scrittura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di scrittura|nessuno|
|VolumeLogicalSize|Dimensioni consumate volume|Byte|Media|Dimensioni logiche del volume (byte usati)|nessuno|
|VolumeSnapshotSize|Dimensioni dello snapshot del volume|Byte|Media|Dimensioni di tutti gli snapshot nel volume|nessuno|
|ReadIops|Operazioni di I/O in lettura|Conteggio al secondo|Media|Operazioni di I/O in lettura al secondo|nessuno|
|WriteIops|Operazioni di I/O in scrittura|Conteggio al secondo|Media|Operazioni di I/O in scrittura al secondo|nessuno|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Dimensioni pool allocati al volume|Byte|Media|Dimensioni usate del pool|nessuno|
|VolumePoolTotalLogicalSize|Dimensioni del pool utilizzate|Byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|nessuno|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BytesSentRate|Byte inviati|Byte|Totale|Numero di byte inviati dall'interfaccia di rete|nessuno|
|BytesReceivedRate|Byte ricevuti|Byte|Totale|Numero di byte ricevuti dall'interfaccia di rete|nessuno|
|PacketsSentRate|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|nessuno|
|PacketsReceivedRate|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|nessuno|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media del percorso dati di Load Balancer per periodo di tempo|FrontendIPAddress,FrontendPort|
|DipAvailability|Stato probe di integrità|Conteggio|Media|Stato probe di integrità media di Load Balancer per periodo di tempo|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|FrontendIPIndirizzo,Porta frontend,Direzione|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|FrontendIPIndirizzo,Porta frontend,Direzione|
|SYNCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|FrontendIPIndirizzo,Porta frontend,Direzione|
|SnatConnectionCount|Numero di connessioni SNAT|Conteggio|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Porte SNAT allocate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT allocate nel periodo di tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval (IndirizzoIP frontend (Indirizzo IP frontend),|
|UsedSnatPorts|Porte SNAT usate (anteprima)|Conteggio|Totale|Numero totale di porte SNAT usate nel periodo di tempo|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval (IndirizzoIP frontend (Indirizzo IP frontend),|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query per zona DNS|nessuno|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS|nessuno|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità di set di record usata da una zona DNS|nessuno|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS pacchetti in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso|nessuno|
|PacketsDroppedDDoS|DDoS pacchetti in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso eliminati|nessuno|
|PacketsForwardedDDoS|DDoS pacchetti in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso inoltrati|nessuno|
|TCPPacketsInDDoS|DDoS pacchetti TCP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso|nessuno|
|TCPPacketsDroppedDDoS|DDoS pacchetti TCP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso eliminati|nessuno|
|TCPPacketsForwardedDDoS|DDoS pacchetti TCP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso inoltrati|nessuno|
|UDPPacketsInDDoS|DDoS pacchetti UDP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso|nessuno|
|UDPPacketsDroppedDDoS|DDoS pacchetti UDP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso eliminati|nessuno|
|UDPPacketsForwardedDDoS|DDoS pacchetti UDP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso inoltrati|nessuno|
|BytesInDDoS|DDoS byte in ingresso|Byte al secondo|Massimo|DDoS byte in ingresso|nessuno|
|BytesDroppedDDoS|DDoS byte in ingresso eliminati|Byte al secondo|Massimo|DDoS byte in ingresso eliminati|nessuno|
|BytesForwardedDDoS|DDoS byte in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte in ingresso inoltrati|nessuno|
|TCPBytesInDDoS|DDoS byte TCP in ingresso|Byte al secondo|Massimo|DDoS byte TCP in ingresso|nessuno|
|TCPBytesDroppedDDoS|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte TCP in ingresso eliminati|nessuno|
|TCPBytesForwardedDDoS|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte TCP in ingresso inoltrati|nessuno|
|UDPBytesInDDoS|DDoS byte UDP in ingresso|Byte al secondo|Massimo|DDoS byte UDP in ingresso|nessuno|
|UDPBytesDroppedDDoS|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte UDP in ingresso eliminati|nessuno|
|UDPBytesForwardedDDoS|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte UDP in ingresso inoltrati|nessuno|
|IfUnderDDoSAttack|Sotto attacco DDoS o no|Conteggio|Massimo|Sotto attacco DDoS o no|nessuno|
|DDoSTriggerTCPPackets|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|nessuno|
|DDoSTriggerUDPPackets|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|nessuno|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Conteggio al secondo|Massimo|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|nessuno|
|VipAvailability|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Porta|
|ByteCount|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Porta,Direzione|
|PacketCount|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Porta,Direzione|
|SynCount|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Porta,Direzione|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo di andata e ritorno per i ping in una macchina virtuale|Millisecondi|Media|Tempo di andata e ritorno per i ping inviati a una macchina virtuale di destinazione|IndirizzoClienteOrigine,IndirizzoClienteDestinatario|
|PingMeshProbesFailedPercent|Ping non riusciti in una macchina virtualeFailed Pings to a VM|Percentuale|Media|Percentuale del numero di Ping non riusciti per il totale dei ping inviati di una macchina virtuale di destinazione|IndirizzoClienteOrigine,IndirizzoClienteDestinatario|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ApplicationRuleHit|Conteggio passaggi delle regole dell'applicazione|Conteggio|Totale|Numero di volte in cui le regole dell'applicazione sono state raggiunte|Stato,Motivo,Protocollo|
|NetworkRuleHit (Elenco di rete)|Conteggio passaggi regole di rete|Conteggio|Totale|Numero di volte in cui le regole di rete sono state colpite|Stato,Motivo,Protocollo|
|FirewallSalute|Stato di integrità del firewall|Percentuale|Media|Stato di integrità del firewall|Stato,Motivo|
|DataProcessed (DataProcessed)|Dati elaborati|Byte|Totale|Quantità totale di dati elaborati da Firewall|nessuno|
|SNATPortUtilizzo|Utilizzo delle porte SNAT|Percentuale|Media|Utilizzo delle porte SNAT|nessuno|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Velocità effettiva|Velocità effettiva|Byte al secondo|Media|Numero di byte al secondo distribuiti dal gateway applicazione|nessuno|
|UnhealthyHostCount|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|TotalRequests|Totale richieste|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|AvgRequestCountPerHealthyHost (file AvgRequestCountPerHealthyHost)|Richieste al minuto per host integroRequests per Minute Per Healthy Host|Conteggio|Media|Numero medio di richieste al minuto per host back-end integro in un pool|BackendSettingsPool|
|FailedRequests|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|ResponseStatus|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|CurrentConnections|Current Connections (Connessioni correnti)|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|nessuno|
|NewConnectionsPerSecond|Nuove connessioni al secondo|Conteggio al secondo|Media|Nuove connessioni al secondo stabilite con il gateway applicazione|nessuno|
|Utilizzo Cpu|Utilizzo CPU|Percentuale|Media|Utilizzo corrente della CPU del gateway applicazione|nessuno|
|CapacityUnits|Unità di capacità correnti|Conteggio|Media|Capacità Unità consumate|nessuno|
|FixedBillableCapacityUnits|Unità di capacità fatturabili fisse|Conteggio|Media|Unità di capacità minima che verranno addebitate|nessuno|
|EstimatedBilledCapacityUnits|Unità di capacità fatturate stimate|Conteggio|Media|Unità di capacità stimate che verranno addebitate|nessuno|
|ComputeUnits (Unità Calcolo)|Unità di calcolo correnti|Conteggio|Media|Unità di calcolo consumate|nessuno|
|BackendResponseStatus|Stato risposta back-end|Conteggio|Totale|Numero di codici di risposta HTTP generati dai membri back-end. Non sono inclusi i codici di risposta generati dal gateway applicazione.|Server backend,BackendPool,BackendHttpSetting,HttpStatusGroup|
|Protocollo Tls|Protocollo TLS client|Conteggio|Totale|Numero di richieste TLS e non TLS avviate dal client che ha stabilito la connessione con il gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base alla dimensione Protocollo TLS.|Listener,TlsProtocol|
|BytesSent|Byte inviati|Byte|Totale|Numero totale di byte inviati dal gateway applicazione ai client|Listener|
|BytesReceived|Byte ricevuti|Byte|Totale|Numero totale di byte ricevuti dal gateway applicazione dai client|Listener|
|ClientRtt (t)|Client RTT|Millisecondi|Media|Tempo di andata e ritorno medio tra i client e il gateway applicazione. Questa metrica indica il tempo necessario per stabilire le connessioni e restituire le conferme|Listener|
|ApplicationGatewayTotalTime|Tempo totale gateway applicazione|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta e l'invio della relativa risposta. Viene calcolata come media dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al momento in cui termina l'operazione di invio della risposta. È importante notare che questo include in genere il tempo di elaborazione del gateway applicazione, il tempo in cui i pacchetti di richiesta e risposta viaggiano sulla rete e il tempo impiegato dal server back-end per rispondere.|Listener|
|BackendConnectTime|Tempo di connessione back-end|Millisecondi|Media|Tempo impiegato per stabilire una connessione con un server back-end|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo di risposta del primo byte back-end|Millisecondi|Media|Intervallo di tempo tra l'inizio della connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta, approssimando il tempo di elaborazione del server back-end|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Tempo di risposta dell'ultimo byte back-end|Millisecondi|Media|Intervallo di tempo tra l'inizio della connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta|Listener,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Distribuzione totale delle regole del firewall applicazione Web v1|Conteggio|Totale|Distribuzione totale delle regole di Web Application Firewall v1 per il traffico in ingresso|Gruppo regole,IDRegola|
|Conteggio bloccati|Distribuzione delle regole delle richieste bloccate del Firewall applicazione Web v1|Conteggio|Totale|Web Application Firewall v1 bloccato richieste distribuzione delle regole|Gruppo regole,IDRegola|
|BlockedReqCount (conteggio bloccato)|Conteggio richieste bloccate di Web Application Firewall v1|Conteggio|Totale|Conteggio richieste bloccate di Web Application Firewall v1|nessuno|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AverageBandwidth|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|nessuno|
|P2SBandwidth|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|nessuno|
|P2SConnectionCount|Numero di connessioni da punto a sito|Conteggio|Massimo|Numero di connessioni da punto a sito di un gateway|Protocollo|
|TunnelAverageBandwidth|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName,IPRemoto|
|TunnelEgressBytes|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName,IPRemoto|
|TunnelIngressBytes|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName,IPRemoto|
|TunnelEgressPackets|Pacchetti in uscita tunnel|Conteggio|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName,IPRemoto|
|TunnelIngressPackets|Pacchetti in ingresso tunnel|Conteggio|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName,IPRemoto|
|TunnelEgressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName,IPRemoto|
|TunnelIngressPacketDropTSMismatch|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName,IPRemoto|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Conteggio|Media|Rx Livello di luce in dBm|Collegamento,Corsia|
|TxLightLevel (livello di illuminazione)|TxLightLevel (livello di illuminazione)|Conteggio|Media|Livello di luce tx in dBm|Collegamento,Corsia|
|Stato Amministratore|Stato Amministratore|Conteggio|Media|Stato amministratore della porta|Collegamento|
|LineProtocol (Protocollo Line)|LineProtocol (Protocollo Line)|Conteggio|Media|Stato del protocollo di linea della porta|Collegamento|
|PortaBitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Collegamento|
|PortaBitsOutPerSecondo|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Collegamento|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeredCircuitSKey|
|BgpAvailability|Disponibilità Bgp|Percentuale|Media|Disponibilità BGP da MSEE verso tutti i peer.|PeeringType,Peer|
|ArpAvailability|Disponibilità Arp|Percentuale|Media|Disponibilità ARP da MSEE verso tutti i peer.|PeeringType,Peer|
|QosDropBitsInPerSecond|Oggetto DroppedInBitsPerSecond|Conteggio al secondo|Media|Bit in ingresso di dati eliminati al secondo|nessuno|
|QosDropBitsOutPerSecondo|DroppedOutBitsPerSecond|Conteggio al secondo|Media|Bit in uscita di dati eliminati al secondo|nessuno|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|nessuno|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|nessuno|
|BitsOutPerSecond|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|nessuno|

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
|ProbesFailedPercent|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|nessuno|
|AverageRoundtripMs|Tempo di andata e ritorno (tempo di andata e ritorno) (tempo di andata e ritorno) (ms)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|nessuno|
|ChecksFailedPercent|Percentuale di controlli non riusciti (anteprima)Checks Failed Percent (Preview)|Percentuale|Media|% dei controlli di controllo della connettività non riusciti|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Tempo di andata e ritorno (ms) (anteprima)Round-Trip Time (ms) (Preview)|Millisecondi|Media|Tempo di andata e ritorno in millisecondi per i controlli di monitoraggio della connettività|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|RequestCount|Conteggio richieste|Conteggio|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ResponseSize|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|FatturabileResponseSize|Dimensioni risposta fatturabile|Byte|Totale|Numero di byte fatturabili (minimo 2 KB per richiesta) inviati come risposte dal proxy HTTP/S ai client.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Conteggio delle richieste del back-end|Conteggio|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|Latenza della richiesta del back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|TotalLatency|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Percentuale di integrità del back-end|Percentuale|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|NomeCriterio,NomeRegola,Azione|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Rete/privateDns-ones

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryVolume|Volume della query|Conteggio|Totale|Numero di query servite per una zona DNS privata|nessuno|
|RecordSetCount|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS privata|nessuno|
|RecordSetCapacityUtilization|Uso della capacità di set di record|Percentuale|Massimo|Percentuale di capacità del set di record utilizzata da una zona DNS privata|nessuno|
|VirtualNetworkLinkCount|Conteggio collegamenti di rete virtualeVirtual Network Link Count|Conteggio|Massimo|Numero di reti virtuali collegate a una zona DNS privata|nessuno|
|VirtualNetworkLinkCapacityUtilization|Utilizzo della capacità del collegamento di rete virtualeVirtual Network Link Capacity Utilization|Percentuale|Massimo|Percentuale di capacità di Virtual Network Link utilizzata da una zona DNS privata|nessuno|
|VirtualNetworkWithRegistrationLinkCount|Conteggio collegamenti registrazione rete virtuale|Conteggio|Massimo|Numero di reti virtuali collegate a una zona DNS privata con la registrazione automatica abilitata|nessuno|
|VirtualNetworkWithRegistrationCapacityUtilization|Utilizzo della capacità del collegamento alla registrazione della rete virtualeVirtual Network Registration Link Capacity Utilization|Percentuale|Massimo|Percentuale di collegamento di rete virtuale con capacità di registrazione automatica utilizzata da una zona DNS privata|nessuno|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|registration.all|Operazioni di registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |nessuno|
|registration.create|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|nessuno|
|registration.update|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|nessuno|
|registration.get|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|nessuno|
|registration.delete|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|nessuno|
|incoming|Messaggi in ingresso|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |nessuno|
|incoming.scheduled|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche push pianificate annullate|nessuno|
|incoming.scheduled.cancel|Notifiche push pianificate annullate|Conteggio|Totale|Notifiche push pianificate annullate|nessuno|
|scheduled.pending|Notifiche pianificate in sospeso|Conteggio|Totale|Notifiche pianificate in sospeso|nessuno|
|installation.all|Operazioni di gestione installazione|Conteggio|Totale|Operazioni di gestione installazione|nessuno|
|installation.get|Ottieni operazioni di installazione|Conteggio|Totale|Ottieni operazioni di installazione|nessuno|
|installation.upsert|Crea o aggiorna operazioni di installazione|Conteggio|Totale|Crea o aggiorna operazioni di installazione|nessuno|
|installation.patch|Operazioni di installazione patch|Conteggio|Totale|Operazioni di installazione patch|nessuno|
|installation.delete|Elimina operazioni di installazione|Conteggio|Totale|Elimina operazioni di installazione|nessuno|
|outgoing.allpns.success|Notifiche riuscite|Conteggio|Totale|Numero di tutte le notifiche riuscite.|nessuno|
|outgoing.allpns.invalidpayload|Errori payload|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|nessuno|
|outgoing.allpns.pnserror|Errori sistema di notifica esterno|Conteggio|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|nessuno|
|outgoing.allpns.channelerror|Errori canale|Conteggio|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|nessuno|
|outgoing.allpns.badorexpiredchannel|Errori canale non valido o scaduto|Conteggio|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|nessuno|
|outgoing.wns.success|Notifiche WNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|nessuno|
|outgoing.wns.invalidcredentials|Errori di autorizzazione WNS (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|nessuno|
|outgoing.wns.badchannel|Errore canale WNS non valido|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|nessuno|
|outgoing.wns.expiredchannel|Errore canale WNS scaduto|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|nessuno|
|outgoing.wns.throttled|Notifiche WNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|nessuno|
|outgoing.wns.tokenproviderunreachable|Errori di autorizzazione WNS (non disponibile)|Conteggio|Totale|Windows Live non è raggiungibile.|nessuno|
|outgoing.wns.invalidtoken|Errori di autorizzazione WNS (token non valido)|Conteggio|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|nessuno|
|outgoing.wns.wrongtoken|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|nessuno|
|outgoing.wns.invalidnotificationformat|Formato notifica WNS non valido|Conteggio|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|nessuno|
|outgoing.wns.invalidnotificationsize|Errore dimensioni notifica WNS non valide|Conteggio|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|nessuno|
|outgoing.wns.channelthrottled|Canale WNS limitato|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|nessuno|
|outgoing.wns.channeldisconnected|Canale WNS disconnesso|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|nessuno|
|outgoing.wns.dropped|Notifiche WNS eliminate|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|nessuno|
|outgoing.wns.pnserror|Errori WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|nessuno|
|outgoing.wns.authenticationerror|Errori di autenticazione WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|nessuno|
|outgoing.apns.success|Notifiche APNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|nessuno|
|outgoing.apns.invalidcredentials|Errori di autorizzazione del servizio APN|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|nessuno|
|outgoing.apns.badchannel|Errore canale APNS non valido|Conteggio|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|nessuno|
|outgoing.apns.expiredchannel|Errore canale APNS scaduto|Conteggio|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|nessuno|
|outgoing.apns.invalidnotificationsize|Errore dimensioni notifica APNS non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|nessuno|
|outgoing.apns.pnserror|Errori APNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|nessuno|
|outgoing.gcm.success|Notifiche GCM completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|nessuno|
|outgoing.gcm.invalidcredentials|Errori di autorizzazione GCM (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|nessuno|
|outgoing.gcm.badchannel|Errore canale GCM non valido|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|nessuno|
|outgoing.gcm.expiredchannel|Errore canale GCM scaduto|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|nessuno|
|outgoing.gcm.throttled|Notifiche GCM limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|nessuno|
|outgoing.gcm.invalidnotificationformat|Formato notifiche GCM non valido|Conteggio|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|nessuno|
|outgoing.gcm.invalidnotificationsize|Errore dimensioni notifica GCM non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|nessuno|
|outgoing.gcm.wrongchannel|Errore canale GCM errato|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|nessuno|
|outgoing.gcm.pnserror|Errori GCM|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|nessuno|
|outgoing.gcm.authenticationerror|Errori di autenticazione GCM|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|nessuno|
|outgoing.mpns.success|Notifiche MPNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|nessuno|
|outgoing.mpns.invalidcredentials|Credenziali MPNS non valide|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|nessuno|
|outgoing.mpns.badchannel|Errori canale MPNS errato|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|nessuno|
|outgoing.mpns.throttled|Notifiche MPNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|nessuno|
|outgoing.mpns.invalidnotificationformat|Formato notifiche MPNS non valido|Conteggio|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|nessuno|
|outgoing.mpns.channeldisconnected|Canale MPNS disconnesso|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|nessuno|
|outgoing.mpns.dropped|Notifiche MPNS eliminate|Conteggio|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|nessuno|
|outgoing.mpns.pnserror|Errori MPNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|nessuno|
|outgoing.mpns.authenticationerror|Errori di autenticazione MPNS|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|nessuno|
|notificationhub.pushes|Tutte le notifiche in uscita|Conteggio|Totale|Tutte le notifiche in uscita dell'hub di notifica|nessuno|
|incoming.all.requests|Tutte le richieste in ingresso|Conteggio|Totale|Totale richieste in ingresso per un hub di notifica|nessuno|
|incoming.all.failedrequests|Tutte le richieste in ingresso non riuscite|Conteggio|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|nessuno|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Conteggio|Media|Average_% Free Inodes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Free Space|% Free Space|Conteggio|Media|Average_% Free Space|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Used Inodes|% Used Inodes|Conteggio|Media|Average_% Used Inodes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Used Space|% Used Space|Conteggio|Media|Average_% Used Space|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Disk Read Bytes/sec|Byte letti da disco/sec |Conteggio|Media|Average_Disk Read Bytes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Disk Reads/sec|Letture disco/sec |Conteggio|Media|Average_Disk Reads/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Disk Transfers/sec|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Disk Write Bytes/sec|Byte scritti su disco/sec|Conteggio|Media|Average_Disk Write Bytes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Disk Writes/sec|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Free Megabytes|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Conteggio|Media|Average_Logical Disk Bytes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Available Memory|% Available Memory|Conteggio|Media|Average_% Available Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Available Swap Space|% Available Swap Space|Conteggio|Media|Average_% Available Swap Space|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Used Memory|% Used Memory|Conteggio|Media|Average_% Used Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Used Swap Space|% Used Swap Space|Conteggio|Media|Average_% Used Swap Space|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Available MBytes Memory|Available MBytes Memory|Conteggio|Media|Average_Available MBytes Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Available MBytes Swap|Available MBytes Swap|Conteggio|Media|Average_Available MBytes Swap|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Page Reads/sec|Page Reads/sec|Conteggio|Media|Average_Page Reads/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Page Writes/sec|Page Writes/sec|Conteggio|Media|Average_Page Writes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Pages/sec|Pages/sec|Conteggio|Media|Average_Pages/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Conteggio|Media|Average_Used MBytes Swap Space|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Used Memory MBytes|Used Memory MBytes|Conteggio|Media|Average_Used Memory MBytes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Conteggio|Media|Average_Total Bytes Transmitted|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Bytes Received|Total Bytes Received|Conteggio|Media|Average_Total Bytes Received|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Bytes|Total Bytes|Conteggio|Media|Average_Total Bytes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Packets Transmitted|Total Packets Transmitted|Conteggio|Media|Average_Total Packets Transmitted|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Packets Received|Total Packets Received|Conteggio|Media|Average_Total Packets Received|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Rx Errors|Total Rx Errors|Conteggio|Media|Average_Total Rx Errors|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Tx Errors|Total Tx Errors|Conteggio|Media|Average_Total Tx Errors|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Total Collisions|Total Collisions|Conteggio|Media|Average_Total Collisions|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Avg. Disk sec/Read|Media letture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Read|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Avg. Disk sec/Transfer|Media disco/trasferimento|Conteggio|Media|Average_Avg. Disk sec/Transfer|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Avg. Disk sec/Write|Media Scritture disco/sec|Conteggio|Media|Average_Avg. Disk sec/Write|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Conteggio|Media|Average_Physical Disk Bytes/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Pct Privileged Time|Pct Privileged Time|Conteggio|Media|Average_Pct Privileged Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Pct User Time|Pct User Time|Conteggio|Media|Average_Pct User Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Used Memory kBytes|Used Memory kBytes|Conteggio|Media|Average_Used Memory kBytes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Virtual Shared Memory|Virtual Shared Memory|Conteggio|Media|Average_Virtual Shared Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% DPC Time|% DPC Time|Conteggio|Media|Average_% DPC Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Idle Time|% Idle Time|Conteggio|Media|Average_% Idle Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Interrupt Time|% Interrupt Time|Conteggio|Media|Average_% Interrupt Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% IO Wait Time|% IO Wait Time|Conteggio|Media|Average_% IO Wait Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Nice Time|% Nice Time|Conteggio|Media|Average_% Nice Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Privileged Time|% Privileged Time|Conteggio|Media|Average_% Privileged Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Processor Time|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% User Time|% User Time|Conteggio|Media|Average_% User Time|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Free Physical Memory|Free Physical Memory|Conteggio|Media|Average_Free Physical Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Free Space in Paging Files|Free Space in Paging Files|Conteggio|Media|Average_Free Space in Paging Files|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Free Virtual Memory|Free Virtual Memory|Conteggio|Media|Average_Free Virtual Memory|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Processes|Processi|Conteggio|Media|Average_Processes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Uptime|Uptime|Conteggio|Media|Average_Uptime|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Users|Utenti|Conteggio|Media|Average_Users|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Current Disk Queue Length|Lunghezza corrente coda del disco|Conteggio|Media|Average_Current Disk Queue Length|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Available MBytes|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_% Committed Bytes In Use|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Bytes Received/sec|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Bytes Sent/sec|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Bytes Total/sec|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Average_Processor Queue Length|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer,NomeOggetto,NomeIstanza,PercorsoContatore,SistemaOrigine|
|Heartbeat|Heartbeat|Conteggio|Totale|Heartbeat|Computer,TipoSistema,Versione,IdComputerOrigineComputerOrigine|
|Aggiornamento|Aggiornamento|Conteggio|Media|Aggiornamento|Computer,Prodotto,Classificazione,UpdateState,Facoltativo,Approvato|
|Event|Event|Conteggio|Media|Event|Origine,Registroeventi,Computer,CategoriaEvento,LivelloEvento,NomeLivelloEvento,IDEvento|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|PrefixLatency (Latenza prefisso)|Latenza prefissoPrefix Latency|Millisecondi|Media|Latenza prefisso mediana|NomePrefisso|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilità sessione V4|Percentuale|Media|Disponibilità della sessione V4|ConnectionId|
|SessionAvailabilityV6|Disponibilità sessione V6|Percentuale|Media|Disponibilità della sessione V6|ConnectionId|
|IngressTrafficRate|Velocità traffico in ingresso|BitsPerSecondo|Media|Velocità del traffico in ingresso in bit al secondo|ConnectionId|
|EgressTrafficRate|Velocità traffico in uscita|BitsPerSecondo|Media|Velocità del traffico in uscita in bit al secondo|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueryDuration|Durata delle query|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Thread: lunghezza della coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|
|qpu_high_utilization_metric|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|memory_metric|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/account

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuzione delle risorse per classificazione|Conteggio|Totale|Indica il numero di attività a cui è assegnata una determinata classificazione, ovvero sono classificati con tale etichetta.|Classificazione,Origine,IDRisorsa|
|AssetDistributionByStorageType|Distribuzione delle risorse per tipo di risorsa di archiviazione|Conteggio|Totale|Indica il numero di risorse con un determinato tipo di archiviazione.|StorageType,ResourceId|
|CatalogActiveUsers|Utenti attivi giornalieri|Conteggio|Totale|Numero di utenti attivi al giorno|ResourceId|
|CatalogUsage|Distribuzione dell'utilizzo per operazione|Conteggio|Totale|Indicare il numero di operazioni che l'utente effettua al catalogo, ad esempio Accesso, Ricerca, Glossario.|Operazione,ResourceId|
|NumberOfAssetsWithClassifications|Numero di attività con almeno una classificazione|Conteggio|Media|Indica il numero di risorse con almeno una classificazione tag.|ResourceId|
|ScanCancelled|Scansione annullata|Conteggio|Totale|Indica il numero di scansioni annullate.|ResourceId|
|ScanCompleted (Completato)|Scansione completata|Conteggio|Totale|Indica il numero di analisi completate.|ResourceId|
|ScanFailed (ScanFailed)|Scansione non riuscita|Conteggio|Totale|Indica che il numero di scansioni non è riuscita.|ResourceId|
|ScansioneTimeTaken|Tempo di scansione impiegato|Secondi|Totale|Indica il tempo totale di scansione in secondi.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Conteggio|Totale|Connessioni listener riuscite per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Conteggio|Totale|Errore del client su connessioni listener per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Conteggio|Totale|Errore del server su connessioni listener per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
|Connessioni mittente - Operazione riuscita|Connessioni mittente - Operazione riuscita|Conteggio|Totale|Connessioni mittente riuscite per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
|SenderConnections-ClientError|SenderConnections-ClientError|Conteggio|Totale|Errore del client su connessioni mittente per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
|SenderConnections-ServerError|SenderConnections-ServerError|Conteggio|Totale|Errore del server su connessioni mittente per Microsoft.Relay.|NomeEntità,RisultatoOperazione|
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
|SearchLatency|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|nessuno|
|SearchQueriesPerSecond|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|nessuno|
|ThrottledSearchQueriesPercentage|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|nessuno|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SuccessfulRequests|Richieste riuscite|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi|NomeEntità,RisultatoOperazione|
|ServerErrors|Errori server.|Conteggio|Totale|Errori del server per Microsoft.ServiceBus.|NomeEntità,RisultatoOperazione|
|UserErrors|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.ServiceBus.|NomeEntità,RisultatoOperazione|
|ThrottledRequests|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus.|NomeEntità,RisultatoOperazione|
|IncomingRequests|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus.|nessuno|
|ConnectionsOpened|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.ServiceBus.|EntityName|
|Dimensione|Dimensione|Byte|Media|Dimensioni di una coda o di un argomento in byte.|EntityName|
|Messaggi|Numero di messaggi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi contenuti in una coda o in un argomento.|EntityName|
|ActiveMessages|Numero di messaggi attivi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi attivi contenuti in una coda o in un argomento.|EntityName|
|DeadletteredMessages|Numero di messaggi non recapitabili in una coda/argomento.|Conteggio|Media|Numero di messaggi non recapitabili in una coda/argomento.|EntityName|
|Messaggi pianificati|Numero di messaggi pianificati in una coda/argomento.|Conteggio|Media|Numero di messaggi pianificati in una coda/argomento.|EntityName|
|NamespaceCpuUsage|CPU|Percentuale|Massimo|Metrica di utilizzo della CPU dello spazio dei nomi Premium del bus di servizio.|Replica|
|NamespaceMemoryUsage|Utilizzo della memoria|Percentuale|Massimo|Metriche di utilizzo della memoria dello spazio dei nomi Premium del bus del servizio.|Replica|
|CPUXNS|CPU (obsoleto)|Percentuale|Massimo|Metrica di utilizzo della CPU dello spazio dei nomi Premium del bus di servizio. Questa metrica viene depricata. Utilizzare invece la metrica della CPU (NamespaceCpuUsage).|Replica|
|WSXNS|Utilizzo memoria (obsoleto)|Percentuale|Massimo|Metriche di utilizzo della memoria dello spazio dei nomi Premium del bus del servizio. Questa metrica è deprecata. Utilizzare invece la metrica Utilizzo memoria (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applicazioni

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|AllocazioneCpu|AllocazioneCpu|Conteggio|Media|Cpu allocata a questo contenitore in core milli|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|AllocatedMemory|AllocatedMemory|Byte|Media|Memoria allocata a questo contenitore in MBMemory allocated to this container in MB|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|Cpu effettiva|Cpu effettiva|Conteggio|Media|Utilizzo effettivo della CPU in core milli|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|ActualMemory (Memoria effettiva)|ActualMemory (Memoria effettiva)|Byte|Media|Utilizzo effettivo della memoria in MB|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|Utilizzo Cpu|Utilizzo Cpu|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|Utilizzo della memoria|Utilizzo della memoria|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio|
|Stato applicazione|Stato applicazione|Conteggio|Media|Stato dell'applicazione Service Fabric Mesh|NomeApplicazione,Stato|
|ServiceStatus|ServiceStatus|Conteggio|Media|Stato di integrità di un servizio nell'applicazione Service Fabric Mesh|NomeApplicazione,Stato,NomeServizio|
|ServiceReplicaStatus (ReplicaServizio)|ServiceReplicaStatus (ReplicaServizio)|Conteggio|Media|Stato di integrità di una replica del servizio nell'applicazione Service Fabric MeshHealth Status of a service replica in Service Fabric Mesh application|NomeApplicazione,Stato,NomeServizio,NomeReplicaServizio|
|Proprietà ContainerStatus|Proprietà ContainerStatus|Conteggio|Media|Stato del contenitore nell'applicazione Service Fabric Mesh|NomeApplicazione,NomeServizio,NomePacchetti,NomeReplicaServizio,Stato|
|Conteggio riavvio|Conteggio riavvio|Conteggio|Media|Conteggio del riavvio di un contenitore nell'applicazione Mesh di Service FabricRestart count of a container in Service Fabric Mesh application|NomeApplicazione,Stato,NomeServizio,NomeReplicaServizio,NomePacchettoCode|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ConnectionCount|Numero di connessioni|Conteggio|Massimo|Quantità di connessioni utente.|Endpoint|
|MessageCount|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|nessuno|
|InboundTraffic|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|nessuno|
|OutboundTraffic|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|nessuno|
|UserErrors|Errori utente|Percentuale|Massimo|Percentuale di errori utente|nessuno|
|SystemErrors|Errori di sistema|Percentuale|Massimo|Percentuale di errori di sistema|nessuno|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|nessuno|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Registra percentuale di I/O. Non applicabile ai data warehouse.|nessuno|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale DTU. Si applica ai database basati su DTU.|nessuno|
|storage|Spazio dati usato|Byte|Massimo|Spazio dati utilizzato. Non applicabile ai data warehouse.|nessuno|
|connection_successful|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|nessuno|
|connection_failed|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|nessuno|
|blocked_by_firewall|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|nessuno|
|deadlock|Deadlock|Conteggio|Totale|Deadlock. Non applicabile ai data warehouse.|nessuno|
|storage_percent|Spazio dati utilizzato percentuale|Percentuale|Massimo|Spazio dati utilizzato percentuale. Non applicabile ai data warehouse o ai database di iperscalabili.|nessuno|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria. Non applicabile ai data warehouse.|nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale di lavoratori. Non applicabile ai data warehouse.|nessuno|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale di sessioni. Non applicabile ai data warehouse.|nessuno|
|dtu_limit|Limite DTU|Conteggio|Media|Limite DTU. Si applica ai database basati su DTU.|nessuno|
|dtu_used|Uso DTU|Conteggio|Media|DTU utilizzato. Si applica ai database basati su DTU.|nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite della CPU. Si applica ai database basati su vCore.|nessuno|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai database basati su vCore.|nessuno|
|dwu_limit|Limite DWU|Conteggio|Massimo|limite DWU. Si applica solo ai data warehouse.|nessuno|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Massimo|percentuale DWU. Si applica solo ai data warehouse.|nessuno|
|dwu_used|Uso DWU|Conteggio|Massimo|DWU utilizzato. Si applica solo ai data warehouse.|nessuno|
|cache_hit_percent|Percentuale dei riscontri nella cache|Percentuale|Massimo|Percentuale di riscontri nella cache. Si applica solo ai data warehouse.|nessuno|
|cache_used_percent|Percentuale della cache utilizzata|Percentuale|Massimo|Percentuale utilizzata nella cache. Si applica solo ai data warehouse.|nessuno|
|sqlserver_process_core_percent|Percentuale core del processo di SQL ServerSQL Server process core percent|Percentuale|Massimo|Percentuale di utilizzo della CPU per il processo di SQL Server, misurata dal sistema operativo.|nessuno|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della memoria per il processo di SQL Server, misurata dal sistema operativo.|nessuno|
|tempdb_data_size|Kilobyte delle dimensioni del file di dati Tempdb|Conteggio|Massimo|Dimensioni file di dati Tempdb Kilobyte. Non applicabile ai data warehouse.|nessuno|
|tempdb_log_size|Kilobyte delle dimensioni del file di registro di Tempdb|Conteggio|Massimo|Kilobyte della dimensione del file di registro di Tempdb. Non applicabile ai data warehouse.|nessuno|
|tempdb_log_used_percent|Registro percentuale Tempdb utilizzato|Percentuale|Massimo|Registro percentuale Tempdb utilizzato. Non applicabile ai data warehouse.|nessuno|
|local_tempdb_usage_percent|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|nessuno|
|app_cpu_billed|CPU dell'app fatturata|Conteggio|Totale|CPU dell'app fatturata. Si applica ai database senza server.|nessuno|
|app_cpu_percent|Percentuale CPU app|Percentuale|Media|Percentuale della CPU dell'app. Si applica ai database senza server.|nessuno|
|app_memory_percent|Percentuale memoria app|Percentuale|Media|Percentuale di memoria dell'app. Si applica ai database senza server.|nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Archiviazione dei dati allocati. Non applicabile ai data warehouse.|nessuno|
|memory_usage_percent|Percentuale memoria|Percentuale|Massimo|Percentuale di memoria. Si applica solo ai data warehouse.|nessuno|
|dw_backup_size_gb|Dimensioni di archiviazione dei dati|Conteggio|Totale|La dimensione di archiviazione dei dati è costituita dalle dimensioni dei dati e dal log delle transazioni. La metrica viene conteggiata per la parte "Archiviazione" della fattura. Si applica solo ai data warehouse.|nessuno|
|dw_snapshot_size_gb|Dimensioni archiviazione snapshot|Conteggio|Totale|Dimensioni archiviazione snapshot indica le dimensioni delle modifiche incrementali acquisite dagli snapshot per creare punti di ripristino automatici e definiti dall'utente. La metrica viene conteggiata per la parte "Archiviazione" della fattura. Si applica solo ai data warehouse.|nessuno|
|dw_geosnapshot_size_gb|Dimensioni di archiviazione per il ripristino di emergenzaDisaster Recovery Storage Size|Conteggio|Totale|La dimensione dell'archiviazione per il ripristino di emergenza viene rispecchiata come "Archiviazione di ripristino di emergenza" nella fattura. Si applica solo ai data warehouse.|nessuno|
|wlg_allocation_relative_to_system_percent|Allocazione del gruppo di carico di lavoro in base alla percentuale di sistemaWorkload group allocation by system|Percentuale|Massimo|Percentuale assegnata di risorse relative all'intero sistema per ogni gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Allocazione del gruppo di carico di lavoro per percentuale di risorse limiteWorkload group allocation by cap resource percent|Percentuale|Massimo|Percentuale allocata di risorse rispetto alle risorse limite specificate per ogni gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|wlg_active_queries|Query attive del gruppo del carico di lavoroWorkload group active queries|Conteggio|Totale|Query attive all'interno del gruppo del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Query in coda del gruppo di carico di lavoroWorkload group queued queries|Conteggio|Totale|Query in coda all'interno del gruppo del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|active_queries|Query attive|Conteggio|Totale|Query attive in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|nessuno|
|queued_queries|Query in coda|Conteggio|Totale|Query in coda in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|nessuno|
|wlg_active_queries_timeouts|Timeout delle query del gruppo di carico di lavoroWorkload group query timeouts|Conteggio|Totale|Query con timeout per il gruppo del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Percentuale effettiva risorsa min|Percentuale|Massimo|Percentuale minima di risorse riservate e isolate per il gruppo del carico di lavoro, tenendo conto del livello minimo di servizio. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Percentuale di risorse limite efficace|Percentuale|Massimo|Limite rigido della percentuale di risorse consentite per il gruppo di carico di lavoro, tenendo conto della percentuale di risorse min efficaci allocata per altri gruppi di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName,IsUserDefinedWorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Dimensioni di archiviazione di backup complete|Byte|Massimo|Dimensioni cumulative di archiviazione di backup completo. Si applica ai database basati su vCore. Non applicabile ai database Hyperscale.|nessuno|
|diff_backup_size_bytes|Dimensioni di archiviazione di backup differenziali|Byte|Massimo|Dimensioni di archiviazione differenziali di backup cumulative. Si applica ai database basati su vCore. Non applicabile ai database Hyperscale.|nessuno|
|log_backup_size_bytes|Dimensioni dell'archiviazione del backup del logLog backup storage size|Byte|Massimo|Dimensioni di archiviazione del backup del log cumulativo. Si applica ai database basati su vCore e Hyperscale.|nessuno|
|snapshot_backup_size_bytes|Dimensioni di archiviazione del backup snapshot|Byte|Massimo|Dimensioni di archiviazione del backup di snapshot cumulativo. Si applica ai database Hyperscale.|nessuno|
|base_blob_size_bytes|Dimensioni di archiviazione BLOB di base|Byte|Massimo|Dimensioni di archiviazione BLOB di base. Si applica ai database Hyperscale.|nessuno|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|nessuno|
|database_cpu_percent|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|nessuno|
|database_physical_data_read_percent|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|nessuno|
|database_log_write_percent|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale DTU. Si applica ai pool elastici basati su DTU.|nessuno|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|storage_percent|Spazio dati utilizzato percentuale|Percentuale|Media|Spazio dati utilizzato percentuale|nessuno|
|workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|nessuno|
|database_workers_percent|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|nessuno|
|database_sessions_percent|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Conteggio|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|nessuno|
|storage_limit|Dimensioni massime dei dati|Byte|Media|Dimensioni massime dei dati|nessuno|
|eDTU_used|Uso eDTU|Conteggio|Media|eDTU utilizzato. Si applica ai pool elastici basati su DTU.|nessuno|
|database_eDTU_used|Uso eDTU|Conteggio|Media|Uso eDTU|DatabaseResourceId|
|storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|nessuno|
|database_storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|DatabaseResourceId|
|xtp_storage_percent|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|nessuno|
|cpu_limit|Limite CPU|Conteggio|Media|Limite della CPU. Si applica ai pool elastici basati su vCore.|nessuno|
|database_cpu_limit|Limite CPU|Conteggio|Media|Limite CPU|DatabaseResourceId|
|cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|nessuno|
|database_cpu_used|CPU utilizzata|Conteggio|Media|CPU utilizzata|DatabaseResourceId|
|sqlserver_process_core_percent|Percentuale core del processo di SQL ServerSQL Server process core percent|Percentuale|Massimo|Percentuale di utilizzo della CPU per il processo di SQL Server, misurata dal sistema operativo. Si applica alle piscine elastiche.|nessuno|
|sqlserver_process_memory_percent|Percentuale memoria processo SQL Server|Percentuale|Massimo|Percentuale di utilizzo della memoria per il processo di SQL Server, misurata dal sistema operativo. Si applica alle piscine elastiche.|nessuno|
|tempdb_data_size|Kilobyte delle dimensioni del file di dati Tempdb|Conteggio|Massimo|Kilobyte delle dimensioni del file di dati Tempdb|nessuno|
|tempdb_log_size|Kilobyte delle dimensioni del file di registro di Tempdb|Conteggio|Massimo|Kilobyte delle dimensioni del file di registro di Tempdb|nessuno|
|tempdb_log_used_percent|Registro percentuale Tempdb utilizzato|Percentuale|Massimo|Registro percentuale Tempdb utilizzato|nessuno|
|allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|nessuno|
|database_allocated_data_storage|Spazio dati allocato|Byte|Media|Spazio dati allocato|DatabaseResourceId|
|allocated_data_storage_percent|Percentuale spazio dati allocato|Percentuale|Massimo|Percentuale spazio dati allocato|nessuno|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|ElasticPoolResourceId|
|database_storage_used|Spazio dati usato|Byte|Media|Spazio dati usato|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Uso DTU|Conteggio|Media|Uso DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|virtual_core_count|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|nessuno|
|avg_cpu_percent|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|nessuno|
|reserved_storage_mb|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|nessuno|
|storage_space_used_mb|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|nessuno|
|io_requests|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|nessuno|
|io_bytes_read|Byte di I/O letti|Byte|Media|Byte di I/O letti|nessuno|
|io_bytes_written|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|nessuno|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|UsedCapacity|Capacità usata|Byte|Media|Capacità usata account|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BlobCapacity|Capacità BLOB|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio BLOB dell'account di archiviazione in byte.|BlobType,Livello|
|BlobCount|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType,Livello|
|ContainerCount|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|nessuno|
|IndexCapacity|Capacità dell'indice|Byte|Media|Quantità di spazio di archiviazione utilizzato dall'indice ADLS Gen2 (gerarchico) in byte.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|TableCapacity|Capacità tabella|Byte|Media|Quantità di spazio di archiviazione usata dal servizio tabelle dell'account di archiviazione in byte.|nessuno|
|TableCount|Numero di tabella|Conteggio|Media|Numero di tabelle nel servizio tabelle dell'account di archiviazione.|nessuno|
|TableEntityCount|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|FileCapacity|Capacità file|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio file dell'account di archiviazione in byte.|FileShare|
|FileCount|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|nessuno|
|FileShareSnapshotCount|Conteggio snapshot condivisione file|Conteggio|Media|Numero di snapshot presenti nella condivisione nel servizio File dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|Dimensioni snapshot condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|FileShareQuota|Dimensione quota condivisione file|Byte|Media|Limite superiore alla quantità di spazio di archiviazione che può essere usata dal servizio file di Azure in byte.|FileShare|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione,CondivisioneFile|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione,CondivisioneFile|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|QueueCapacity|Capacità coda|Byte|Media|Quantità di spazio di archiviazione utilizzato dal servizio di coda dell'account di archiviazione in byte.|nessuno|
|QueueCount|Numero di coda|Conteggio|Media|Numero di code nel servizio di coda dell'account di archiviazione.|nessuno|
|QueueMessageCount|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo di messaggi della coda nel servizio di coda dell'account di archiviazione.|nessuno|
|Transazioni|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType,GeoType,ApiName,Autenticazione|
|Dati in ingresso|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoTipo,NomeApi,Autenticazione|
|Egress|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoTipo,NomeApi,Autenticazione|
|SuccessServerLatency|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza media usata da Archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in AverageE2ELatency.|GeoTipo,NomeApi,Autenticazione|
|SuccessE2ELatency|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoTipo,NomeApi,Autenticazione|
|Disponibilità|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoTipo,NomeApi,Autenticazione|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ClientIOPS|Numero totale di operazioni di I/O al secondo client|Conteggio|Media|Frequenza delle operazioni dei file client elaborate dalla cache.|nessuno|
|ClientLatency (Latenza client)|Latenza media client|Millisecondi|Media|Latenza media delle operazioni sui file client nella cache di archiviazione.|nessuno|
|ClientReadIOPS|IOPS di lettura clientClient Read IOPS|Conteggio al secondo|Media|Operazioni di lettura client al secondo.|nessuno|
|ClientReadThroughput|Velocità effettiva media lettura cache|Byte al secondo|Media|Velocità di trasferimento dati di lettura client.|nessuno|
|ClientWriteIOPS|IOPS di scrittura clientClient Write IOPS|Conteggio al secondo|Media|Operazioni di scrittura client al secondo.|nessuno|
|ClientWriteThroughputClientWriteThroughput|Velocità effettiva media della scrittura nella cache|Byte al secondo|Media|Velocità di trasferimento dei dati in scrittura del client.|nessuno|
|ClientMetadataReadIOPS|IOPS di lettura metadati clientClient Metadata Read IOPS|Conteggio al secondo|Media|Frequenza delle operazioni sui file client inviate alla cache, escluse le letture di dati, che non modificano lo stato persistente.|nessuno|
|ClientMetadataWriteIOPS|IOPS di scrittura metadati clientClient Metadata Write IOPS|Conteggio al secondo|Media|Frequenza delle operazioni sui file client inviate alla cache, escluse le scritture di dati, che modificano lo stato persistente.|nessuno|
|ClientLockIOPS|IOPS di blocco clientClient Lock IOPS|Conteggio al secondo|Media|Operazioni di blocco dei file client al secondo.|nessuno|
|StorageTargetHealth|Integrità di destinazione archiviazioneStorage Target Health|Conteggio|Media|Risultati booleani del test di connettività tra le destinazioni di cache e di archiviazione.|nessuno|
|Uptime|Uptime|Conteggio|Media|Risultati booleani del test di connettività tra la cache e il sistema di monitoraggio.|nessuno|
|StorageTargetIOPS|Numero totale di operazioni di I/O al secondo StorageTargetTotal StorageTarget IOPS|Conteggio|Media|The rate of all file operations the Cache sends to a particular StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|IOPS di scrittura StorageTargetStorageTarget Write IOPS|Conteggio|Media|Frequenza delle operazioni di scrittura dei file che la cache invia a un particolare StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Velocità effettiva di scrittura asincrona StorageTargetStorageTarget Asynchronous Write Throughput|Byte al secondo|Media|Frequenza di scrittura asincrona dei dati in un oggetto StorageTarget specifico da parte della cache. Si tratta di scritture opportunistiche che non causano il blocco dei client.|StorageTarget|
|StorageTargetSyncWriteThroughput|Velocità effettiva di scrittura sincrona StorageTargetStorageTarget Synchronous Write Throughput|Byte al secondo|Media|Frequenza di scrittura sincrona dei dati in un oggetto StorageTarget specifico da parte della cache. Si tratta di scritture che causano il blocco dei client.|StorageTarget|
|StorageTargetTotalWriteThroughput|Velocità effettiva di scrittura totale storageTarget|Byte al secondo|Media|Frequenza totale con cui la cache scrive i dati in un particolare StorageTarget.|StorageTarget|
|StorageTargetLatency (Latenza destinazione)|Latenza StorageTarget|Millisecondi|Media|Latenza media andata e ritorno di tutte le operazioni sui file che la cache invia a un oggetto StorageTarget parziale.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS di lettura dei metadati StorageTargetStorageTarget Metadata Read IOPS|Conteggio al secondo|Media|Frequenza delle operazioni sui file che non modificano lo stato persistente ed escludendo l'operazione di lettura, che la cache invia a un particolare StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS di scrittura dei metadati StorageTargetStorageTarget Metadata Write IOPS|Conteggio al secondo|Media|Frequenza delle operazioni sui file che modificano lo stato persistente ed escludendo l'operazione di scrittura, che la cache invia a un particolare StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Operazioni di i/O al secondo di lettura StorageTargetStorageTarget Read IOPS|Conteggio al secondo|Media|Frequenza delle operazioni di lettura dei file che la cache invia a un particolare StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Velocità effettiva di lettura di StorageTargetStorageTarget Read Ahead Throughput|Byte al secondo|Media|Frequenza di lettura opportunistica dei dati da StorageTarget da parte della cache.|StorageTarget|
|StorageTargetFillThroughput|Velocità effettiva di riempimento StorageTargetStorageTarget Fill Throughput|Byte al secondo|Media|Frequenza della cache legge i dati da StorageTarget per gestire un mancato riscontro nella cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Velocità effettiva di lettura totale storageTarget|Byte al secondo|Media|Frequenza totale con cui la cache legge i dati da un particolare StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerSyncSessionResult (Informazioni in base a ServerSyncSession)|Risultato sessione di sincronizzazione|Conteggio|Media|Metrica che registra un valore pari a 1 ogni volta che l'endpoint server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Impossibile sincronizzare il numero di file|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali dei file trasferiti per le sessioni di sincronizzazione|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Stato del server online|Conteggio|Massimo|Metrica che registra un valore pari a 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensione totale dei dati richiamati dal server|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Dimensioni di richiamo livelli cloudCloud tiering recall size|Byte|Totale|Dimensione dei dati richiamati|SyncGroupName,NomeServer|
|StorageSyncRecallThroughputBytesPerSecond|Velocità effettiva di richiamata dei livelli cloudCloud tiering recall throughput|Byte al secondo|Media|Velocità effettiva di richiamo delle dimensioni dei dati|SyncGroupName,NomeServer|
|StorageSyncRecalledNetworkBytesByApplication|Dimensione del richiamo della suddivisione in livelli cloud per applicazioneCloud tiering recall size by application|Byte|Totale|Dimensioni dei dati richiamati dall'applicazione|SyncGroupName,NomeServer,NomeApplicazione|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Impossibile sincronizzare il numero di file|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali dei file trasferiti per le sessioni di sincronizzazione|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|NomeEndpointServer,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|File non sincronizzati|Conteggio|Totale|Impossibile sincronizzare il numero di file|NomeEndpointServer,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Byte sincronizzati|Byte|Totale|Dimensioni totali dei file trasferiti per le sessioni di sincronizzazione|NomeEndpointServer,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ServerHeartbeat (ServerHeartbeat)|Stato del server online|Conteggio|Massimo|Metrica che registra un valore pari a 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerResourceId,NomeServer|
|ServerRecallIOTotalSizeBytes|Richiamo cloud a livelli|Byte|Totale|Dimensione totale dei dati richiamati dal server|ServerResourceId,NomeServer|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|ResourceUtilization|% utilizzo unità di streaming|Percentuale|Massimo|% utilizzo unità di streaming|LogicalName,PartitionId|
|InputEvents|Eventi di input|Conteggio|Totale|Eventi di input|LogicalName,PartitionId|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName,PartitionId|
|LateInputEvents|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|LogicalName,PartitionId|
|OutputEvents|Eventi di output|Conteggio|Totale|Eventi di output|LogicalName,PartitionId|
|ConversionErrors|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|LogicalName,PartitionId|
|Errors|Errori di runtime|Conteggio|Totale|Errori di runtime|LogicalName,PartitionId|
|DroppedOrAdjustedEvents|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|LogicalName,PartitionId|
|AMLCalloutRequests|Richieste di funzioni|Conteggio|Totale|Richieste di funzioni|LogicalName,PartitionId|
|AMLCalloutFailedRequests|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste di funzioni non riuscite|LogicalName,PartitionId|
|AMLCalloutInputEvents|Eventi di funzioni|Conteggio|Totale|Eventi di funzioni|LogicalName,PartitionId|
|DeserializationError|Errori di deserializzazione dell'input|Conteggio|Totale|Errori di deserializzazione dell'input|LogicalName,PartitionId|
|EarlyInputEvents|Eventi di input anticipati|Conteggio|Totale|Eventi di input anticipati|LogicalName,PartitionId|
|OutputWatermarkDelaySeconds|Ritardo limite|Secondi|Massimo|Ritardo limite|LogicalName,PartitionId|
|InputEventsSourcesBacklogged|Eventi di input con backlog|Conteggio|Massimo|Eventi di input con backlog|LogicalName,PartitionId|
|InputEventsSourcesPerSecond|Origini di input ricevute|Conteggio|Totale|Origini di input ricevute|LogicalName,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/aree di lavoro

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEndedOrchestrationPipelineRunsEnded|Esecuzioni di tubazioni terminate|Conteggio|Totale|Numero di esecuzioni della pipeline di orchestrazione completate, non riuscite o annullateCount of orchestration pipeline runs that succeed, failed, or were cancelled|Risultato,FailureType,PipelineResult,FailureType,Pipeline|
|OrchestrationActivityRunsEnded|Le esecuzioni di attività sono terminate|Conteggio|Totale|Numero di attività di orchestrazione riuscite, non riuscite o annullate|Risultato,FailureType,Activity,ActivityType,Pipeline|
|OrchestrazioneTriggersEnded|Trigger terminati|Conteggio|Totale|Numero di trigger di orchestrazione che hanno avuto esito positivo, negativo o sono stati annullatiCount of orchestration triggers that succeed, failed, or were cancelled|Risultato,FailureType,Trigger|
|SQLOnDemandLoginAttempts|Tentativi di accesso|Conteggio|Totale|Numero di tentativi di accesso riusciti o non riusciti|Risultato|
|SQLOnDemandQueriesEnded|Query terminate|Conteggio|Totale|Numero di query completate, non riuscite o annullate|Risultato|
|SQLOnDemandQueryProcessedBytes|Dati elaborati|Byte|Totale|Quantità di dati elaborati dalle query|nessuno|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/aree di lavoro/bigDataPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Processi SparkEnded|Applicazioni terminate|Conteggio|Totale|Numero di applicazioni terminate|JobType,JobResult|
|CoresCapacity|Capacità dei nuclei|Conteggio|Massimo|Capacità dei nuclei|nessuno|
|MemoryCapacityGB|Capacità di memoria (GB)|Conteggio|Massimo|Capacità di memoria (GB)|nessuno|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Limite DWU|Limite DWU|Conteggio|Massimo|Obiettivo del livello di servizio del pool SQLService level objective of the SQL pool|nessuno|
|DWUUsato|Uso DWU|Conteggio|Massimo|Rappresenta una rappresentazione generale dell'utilizzo nel pool SQL. Misurato in base al limite DWU - percentuale di DWU|nessuno|
|DWUUsedPercent|DWU percentuale utilizzata|Percentuale|Massimo|Rappresenta una rappresentazione generale dell'utilizzo nel pool SQL. Misurato prendendo il massimo tra la percentuale di CPU e la percentuale di I/O dei dati|nessuno|
|ConnectionsBlockedByFirewall|Connessioni bloccate dal firewall|Conteggio|Totale|Numero di connessioni bloccate dalle regole del firewall. Rivedere i criteri di controllo di accesso per il pool SQL e monitorare queste connessioni se il conteggio è elevato|nessuno|
|AdaptiveCacheHitPercent|Percentuale riscontri cache adattiva|Percentuale|Massimo|Misura il numero di carichi di lavoro che utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale di riscontri nella cache per determinare se eseguire la scalabilità per capacità aggiuntiva o eseguire di nuovo i carichi di lavoro per idratare la cacheUse this metric with the cache hit percentage metric to determine whether to scale for additional capacity or rerun workloads to hydrate the cache|nessuno|
|AdaptiveCacheUsedPercent|Percentuale cache adattiva utilizzata|Percentuale|Massimo|Misura il numero di carichi di lavoro che utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale utilizzata nella cache per determinare se eseguire la scalabilità per capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cacheUse this metric with the cache used percentage metric to determine whether to scale for additional capacity or rerun workloads to hydrate the cache|nessuno|
|LocalTempDBUsedPercent|Percentuale locale tempdb used|Percentuale|Massimo|Utilizzo di tempdb locale in tutti i nodi di calcolo: i valori vengono generati ogni cinque minutiLocal tempdb utilization across all compute nodes - values are emitted every five minute|nessuno|
|MemoryUsedPercent|Percentuale di memoria utilizzata|Percentuale|Massimo|Utilizzo della memoria in tutti i nodi del pool SQLMemory utilization across all nodes in the SQL pool|nessuno|
|Connessioni|Connessioni|Conteggio|Totale|Numero totale di account di accesso totali per il pool SQLCount of Total logins to the SQL pool|Risultato|
|WLGActiveQueries|Query attive del gruppo del carico di lavoroWorkload group active queries|Conteggio|Totale|Le query attive all'interno del gruppo del carico di lavoro. L'utilizzo di questa metrica non filtrata e non divisa consente di visualizzare tutte le query attive in esecuzione nel sistema|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Timeout delle query del gruppo di carico di lavoroWorkload group query timeouts|Conteggio|Totale|Query per il gruppo del carico di lavoro con timeout. I timeout delle query segnalati da questa metrica sono solo una volta avviata l'esecuzione della query (non include il tempo di attesa a causa del blocco o delle attese delle risorse)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Allocazione del gruppo di carico di lavoro in base alla percentuale di sistemaWorkload group allocation by system|Percentuale|Massimo|La percentuale di allocazione delle risorse rispetto all'intero sistema|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Allocazione del gruppo di carico di lavoro per percentuale di risorse max|Percentuale|Massimo|Visualizza la percentuale di allocazione delle risorse rispetto alla percentuale di risorse limite effettivo per gruppo di carico di lavoro. Questa metrica fornisce l'utilizzo efficace del gruppo di carico di lavoro|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Percentuale di risorse limite efficace|Percentuale|Massimo|Percentuale di risorse limite effettivo per il gruppo di carico di lavoro. Se sono presenti altri gruppi di carico di lavoro con min_percentage_resource > 0, l'effective_cap_percentage_resource viene abbassato proporzionalmente|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Percentuale effettiva risorsa min|Percentuale|Minima|L'impostazione percentuale di risorse min effettiva consentito considerare il livello di servizio e le impostazioni del gruppo del carico di lavoro. L'min_percentage_resource effettivo può essere regolato più in alto sui livelli di servizio più bassi|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Query in coda del gruppo di carico di lavoroWorkload group queued queries|Conteggio|Totale|Numero cumulativo di richieste in coda dopo il raggiungimento del limite di concorrenza massimo|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|nessuno|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|nessuno|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|nessuno|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|nessuno|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|nessuno|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|nessuno|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione dell'origine eventi e il numero di sequenza dei messaggi elaborati in Ingresso|nessuno|
|WarmStorageMaxProperties|Warm Storage Max Proprietà|Conteggio|Massimo|Numero massimo di proprietà utilizzate dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite dall'archivio caldo per lo SKU PAYG|nessuno|
|WarmStorageUsedProperties|Warm Storage Proprietà utilizzate |Conteggio|Massimo|Numero di proprietà utilizzate dall'ambiente per lo SKU S1/S2 e numero di proprietà utilizzate da Warm Store per SKU PAYG|nessuno|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|nessuno|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|nessuno|
|IngressReceivedBytes|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|nessuno|
|IngressStoredBytes|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|nessuno|
|IngressStoredEvents|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|nessuno|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|nessuno|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione dell'origine eventi e il numero di sequenza dei messaggi elaborati in Ingresso|nessuno|
|WarmStorageMaxProperties|Warm Storage Max Proprietà|Conteggio|Massimo|Numero massimo di proprietà utilizzate dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite dall'archivio caldo per lo SKU PAYG|nessuno|
|WarmStorageUsedProperties|Warm Storage Proprietà utilizzate |Conteggio|Massimo|Numero di proprietà utilizzate dall'ambiente per lo SKU S1/S2 e numero di proprietà utilizzate da Warm Store per SKU PAYG|nessuno|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond (Informazioni in due per il secondo)|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta alle operazioni di lettura nel periodo di campionamento.|nessuno|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|nessuno|
|Byte letti da disco|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di lettura nel periodo di campionamento.|nessuno|
|Byte scritti su disco|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|nessuno|
|DiskReadOperations (Operazioni di DiscoReadOperations)|Operazioni di lettura su disco|Conteggio|Totale|Numero di operazioni di lettura I/O nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|nessuno|
|DiskWriteOperations|Operazioni di scrittura su discoDisk Write Operations|Conteggio|Totale|Numero di operazioni di scrittura I/O nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|nessuno|
|Operazioni lettura disco/sec|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di lettura I/O nel periodo di campionamento precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|nessuno|
|Operazioni scrittura disco/sec|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura I/O nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|nessuno|
|DiskReadLatency (DiskReadLatency)|Latenza di lettura da disco|Millisecondi|Media|Latenza totale di lettura. La somma delle latenze di lettura del dispositivo e del kernel.|nessuno|
|DiskWriteLatency (DiscoWriteLatency)|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. La somma delle latenze di scrittura del dispositivo e del kernel.|nessuno|
|NetworkInBytesPerSecond (Informazioni in un server incuiminonle|Byte in rete/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|nessuno|
|NetworkOutBytesPerSecondo|Byte in uscita in rete/sec|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|nessuno|
|Rete in ingresso|Rete in ingresso|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|nessuno|
|Rete in uscita|Rete in uscita|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|nessuno|
|MemoryUsed (Memoriautilizzata)|Memoria utilizzata|Byte|Media|Quantità di memoria del computer in uso dalla macchina virtuale.|nessuno|
|MemoryGranted|Memoria concessa|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host finché non viene toccata una sola volta e la memoria concessa può essere scambiata o sbollata se il server Virtuale richiede la memoria.|nessuno|
|MemoryActive (MemoryActive)|Memoria attiva|Byte|Media|Quantità di memoria utilizzata dalla macchina virtuale nell'ultimo piccolo intervallo di tempo. Questo è il numero "vero" della quantità di memoria di cui la macchina virtuale ha attualmente bisogno. Ulteriore, la memoria inutilizzata può essere scambiata o bollata senza alcun impatto sulle prestazioni dell'ospite.|nessuno|
|CPU percentuale|CPU percentuale|Percentuale|Media|Utilizzo della CPU. Questo valore viene riportato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale bidirezionale che usa il 50% di un sistema a quattro core usa completamente due core.|nessuno|
|PercentageCpuReady|Percentuale CPU pronta|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento passato.|nessuno|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|DiskQueueLength|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|TcpSynSent|TCP Syn inviato|Conteggio|Media|TCP Syn inviato|Istanza|
|TcpSynReceived|TCP Syn ricevuto|Conteggio|Media|TCP Syn ricevuto|Istanza|
|TcpEstablished|TCP stabilito|Conteggio|Media|TCP stabilito|Istanza|
|TcpFinWait1|TCP Fin Attesa 1|Conteggio|Media|TCP Fin Attesa 1|Istanza|
|TcpFinWait2|TCP Fin Attesa 2|Conteggio|Media|TCP Fin Attesa 2|Istanza|
|Chiusura tcp|Chiusura TCP|Conteggio|Media|Chiusura TCP|Istanza|
|TcpCloseWait (Attesa di tcp3)|Tcp Chiudi attesa|Conteggio|Media|Tcp Chiudi attesa|Istanza|
|TcpLastAck|Ultimi Ack TCP|Conteggio|Media|Ultimi Ack TCP|Istanza|
|TcpTimeWait (Attesa di TcpTime)|Tempo di attesa TCP|Conteggio|Media|Tempo di attesa TCP|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni) 

> [!NOTE]
> **Utilizzo del file system** è una nuova metrica in fase di implementazione a livello globale, non è previsto alcun dato a meno che non si è stati inseriti nella whitelist per l'anteprima privata.

> [!IMPORTANT]
> **Il tempo medio** di risposta sarà deprecato per evitare confusione con le aggregazioni delle metriche. Utilizzare **il tempo** di risposta in sostituzione.

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
|ResponseTime|Tempo di risposta|Secondi|Totale|Tempo di risposta|Istanza|
|AverageResponseTime|Tempo medio di risposta (deprecato)|Secondi|Media|Tempo medio di risposta|Istanza|
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
|HealthCheckStatus (Stato HealthCheckStatus)|Stato del controllo dell'integrità|Conteggio|Media|Stato del controllo dell'integrità|Istanza|
|FileSystemUsage|Utilizzo del file system|Byte|Media|Utilizzo del file system|nessuno|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

> [!NOTE]
> **Utilizzo del file system** è una nuova metrica in fase di implementazione a livello globale, non è previsto alcun dato a meno che non si è stati inseriti nella whitelist per l'anteprima privata.

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|BytesReceived|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|Http5xx|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|MemoryWorkingSet|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|AverageMemoryWorkingSet|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|FunctionExecutionUnits|Unità di esecuzione della funzione|MB / Millisecondi|Totale|[Unità di esecuzione della funzione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
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
|HealthCheckStatus (Stato HealthCheckStatus)|Stato del controllo dell'integrità|Conteggio|Media|Stato del controllo dell'integrità|Istanza|
|FileSystemUsage|Utilizzo del file system|Byte|Media|Utilizzo del file system|nessuno|

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
|HttpResponseTime (Informazioni in base a Un'ora|Tempo di risposta|Secondi|Media|Tempo di risposta|Istanza|
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
|HealthCheckStatus (Stato HealthCheckStatus)|Stato del controllo dell'integrità|Conteggio|Media|Stato del controllo dell'integrità|Istanza|
|FileSystemUsage|Utilizzo del file system|Byte|Media|Utilizzo del file system|nessuno|

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
|TotalFrontEnds|Front end totali|Conteggio|Media|Front end totali|nessuno|
|SmallAppServicePlanInstances|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|nessuno|
|MediumAppServicePlanInstances|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|nessuno|
|LargeAppServicePlanInstances|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|nessuno|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|WorkersTotal|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|nessuno|
|WorkersAvailable|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|nessuno|
|WorkersUsed|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|nessuno|
|CpuPercentage|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Creare avvisi relativi alle metriche](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](platform-logs-overview.md)

