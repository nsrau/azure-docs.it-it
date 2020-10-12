---
title: Metriche supportate dal Monitoraggio di Azure per tipo di risorsa
description: Elenco delle metriche disponibili per ogni tipo di risorsa con il monitoraggio di Azure.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 59df49d320b23686a3d053335ea2b95e98125b28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88135556"
---
# <a name="supported-metrics-with-azure-monitor"></a>Metriche supportate con il monitoraggio di Azure

> [!NOTE]
> Questo elenco è in gran parte generato automaticamente dall'API REST delle metriche di monitoraggio di Azure. Tutte le modifiche apportate a questo elenco tramite GitHub possono essere scritte senza preavviso. Per informazioni dettagliate su come effettuare gli aggiornamenti permanenti, contattare l'autore di questo articolo.

Il monitoraggio di Azure offre diversi modi per interagire con le metriche, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. 

Questo articolo è un elenco completo di metriche di tutte le piattaforme (ovvero raccolte automaticamente) attualmente disponibili con la pipeline metrica consolidata di monitoraggio di Azure. L'ultimo aggiornamento dell'elenco è stato eseguito il 27 marzo 2020. Le metriche modificate o aggiunte dopo questa data potrebbero non essere visualizzate di seguito. Per eseguire una query e accedere all'elenco di metriche a livello di codice, usare la [versione api 2018-01-01](/rest/api/monitor/metricdefinitions). Altre metriche non presenti in questo elenco possono essere disponibili nel portale o usando le API legacy.

Le metriche sono organizzate in base ai provider di risorse e al tipo di risorsa. Per un elenco dei servizi e dei provider di risorse appartenenti, vedere [provider di risorse per i servizi di Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 

## <a name="exporting-platform-metrics-to-other-locations"></a>Esportazione delle metriche della piattaforma in altre posizioni

È possibile esportare le metriche di piattaforma dalla pipeline di Monitoraggio di Azure in altre posizioni in uno dei due modi seguenti.
1. Uso dell'[API REST delle metriche](/rest/api/monitor/metrics/list)
2. Usare [le impostazioni di diagnostica](diagnostic-settings.md) per instradare le metriche della piattaforma a 
    - Archiviazione di Azure
    - Log di monitoraggio di Azure (e quindi Log Analytics)
    - Hub eventi, il modo in cui vengono recuperati i sistemi non Microsoft 

L'uso delle impostazioni di diagnostica è il modo più semplice per instradare le metriche, ma esistono alcune limitazioni: 

- **Alcune non esportabili** : tutte le metriche sono esportabili con l'API REST, ma alcune non possono essere esportate usando le impostazioni di diagnostica a causa di complessità nel back-end di monitoraggio di Azure. La colonna *esportabile tramite le impostazioni di diagnostica* nelle tabelle seguenti elenca le metriche che possono essere esportate in questo modo.  

- **Metriche multidimensionali** : l'invio di metriche multidimensionali ad altre posizioni tramite le impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione. *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.

## <a name="guest-os-and-host-os-metrics"></a>Metriche del sistema operativo guest e dell'host

> [!WARNING]
> Le metriche per il sistema operativo guest (sistema operativo guest) che viene eseguito in macchine virtuali di Azure, Service Fabric e servizi cloud **non** sono elencate qui. Le metriche del sistema operativo Guest devono essere raccolte tramite uno o più agenti che vengono eseguiti in o come parte del sistema operativo guest.  Le metriche del sistema operativo guest includono contatori delle prestazioni che tengono traccia della percentuale di CPU Guest o dell'utilizzo della memoria, entrambi usati spesso per la scalabilità automatica o gli avvisi. 
>
> **Le metriche del sistema operativo host sono disponibili ed elencate di seguito.** Non sono uguali. Le metriche del sistema operativo host sono correlate alla sessione Hyper-V che ospita la sessione del sistema operativo guest. 

> [!TIP]
> La procedura consigliata consiste nell'usare e configurare l' [estensione diagnostica di Azure](diagnostics-extension-overview.md) per inviare le metriche delle prestazioni del sistema operativo guest nello stesso database delle metriche di monitoraggio di Azure in cui sono archiviate le metriche della piattaforma. L'estensione instrada le metriche del sistema operativo guest tramite l'API [metrica personalizzata](metrics-custom-overview.md) . Sarà quindi possibile tracciare, avvisare e usare le metriche del sistema operativo guest come le metriche della piattaforma. In alternativa, è possibile usare l'agente di Log Analytics per inviare le metriche del sistema operativo guest ai log/Log Analytics di monitoraggio di Azure. Qui è possibile eseguire query su tali metriche in combinazione con dati non di metrica. 

Per informazioni aggiuntive importanti, vedere [Panoramica degli agenti di monitoraggio](agents-overview.md).    

## <a name="table-formatting"></a>Formattazione tabella

> [!IMPORTANT] 
> Questo aggiornamento più recente aggiunge una nuova colonna e riordina le metriche in modo che siano alfabetiche. Le informazioni aggiuntive indicano che le tabelle seguenti possono avere una barra di scorrimento orizzontale nella parte inferiore, a seconda della larghezza della finestra del browser. Se si ritiene di non disporre di informazioni, utilizzare la barra di scorrimento per visualizzare l'intera tabella.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Sì|Memoria: prezzo corrente pulitura memoria|Conteggio|Media|Prezzo corrente della memoria, in €/byte/tempo, moltiplicato per 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Sì|Memoria: pulitura memoria non compattabile|Byte|Media|Quantità di memoria, in byte, non soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|CleanerMemoryShrinkable|Sì|Memoria: pulitura memoria compattabile|Byte|Media|Quantità di memoria, in byte, soggetta a compattazione da parte della pulitura della memoria in background.|ServerResourceType|
|CommandPoolBusyThreads|Sì|Thread: thread occupati nel pool di comandi|Conteggio|Media|Numero dei thread occupati nel pool dei thread dei comandi.|ServerResourceType|
|CommandPoolIdleThreads|Sì|Thread: thread inattivi nel pool di comandi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread dei comandi.|ServerResourceType|
|CommandPoolJobQueueLength|Sì|Lunghezza coda processi nel pool di comandi|Conteggio|Media|Numero dei processi nella coda del pool dei thread dei comandi.|ServerResourceType|
|CurrentConnections|Sì|Connessione: connessioni correnti|Conteggio|Media|Numero corrente delle connessioni client stabilite.|ServerResourceType|
|CurrentUserSessions|Sì|Sessioni utente correnti|Conteggio|Media|Numero corrente di sessioni utente attive.|ServerResourceType|
|LongParsingBusyThreads|Sì|Thread: thread occupati per analisi dei thread lunghi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingIdleThreads|Sì|Thread: thread inattivi per analisi dei thread lunghi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|LongParsingJobQueueLength|Sì|Thread: lunghezza coda processi di analisi dei thread lunghi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread lunghi.|ServerResourceType|
|mashup_engine_memory_metric|Sì|Memoria Motore M|Byte|Media|Utilizzo della memoria da parte dei processi del motore mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Sì|Byte privati del motore M|Byte|Media|Utilizzo di byte privati da processi del motore mashup.|ServerResourceType|
|mashup_engine_qpu_metric|Sì|QPU Motore M|Conteggio|Media|Utilizzo QPU da parte dei processi motore mashup|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Sì|Byte virtuali del motore M|Byte|Media|Utilizzo dei byte virtuali da processi del motore mashup.|ServerResourceType|
|memory_metric|Sì|Memoria|Byte|Media|Memoria. Intervallo 0-25 GB per S1, 0-50 GB per S2 e 0-100 GB per S4|ServerResourceType|
|memory_thrashing_metric|Sì|Thrashing di memoria|Percentuale|Media|Thrashing di memoria medio.|ServerResourceType|
|MemoryLimitHard|Sì|Memoria: limite memoria assoluto|Byte|Media|Limite assoluto della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitHigh|Sì|Memoria: limite memoria massimo|Byte|Media|Limite superiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitLow|Sì|Memoria: limite memoria minimo|Byte|Media|Limite inferiore della memoria, dal file di configurazione.|ServerResourceType|
|MemoryLimitVertiPaq|Sì|Memoria: limite memoria VertiPaq|Byte|Media|Limite in memoria dal file di configurazione.|ServerResourceType|
|MemoryUsage|Sì|Memoria: Utilizzo della memoria|Byte|Media|Utilizzo della memoria del processo server utilizzato per il calcolo del prezzo di una memoria più pulita. Equivale al contatore Process\PrivateBytes più le dimensioni dei dati con mapping in memoria, ignorando la memoria con mapping o allocazione eseguita dal motore di analisi in memoria xVelocity (VertiPaq) in eccesso rispetto al limite di memoria del motore xVelocity.|ServerResourceType|
|private_bytes_metric|Sì|Private Bytes|Byte|Media|Byte privati.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Sì|Thread: thread di processi di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero di thread che eseguono processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Sì|Thread: thread non di I/O occupati nel pool di elaborazione|Conteggio|Media|Numero dei thread che eseguono processi non di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Sì|Thread: thread di processi di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Sì|Thread: thread non di I/O inattivi nel pool di elaborazione|Conteggio|Media|Numero di thread inattivi nel pool dei thread di elaborazione dedicato a processi non di I/O.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Sì|Thread: lunghezza coda processi di I/O nel pool di elaborazione|Conteggio|Media|Numero di processi di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|ProcessingPoolJobQueueLength|Sì|Lunghezza coda processi nel pool di elaborazione|Conteggio|Media|Numero dei processi non di I/O nella coda del pool dei thread di elaborazione.|ServerResourceType|
|qpu_metric|Sì|QPU|Conteggio|Media|QPU. Intervallo 0-100 per S1, 0-200 per S2 e 0-400 per S4|ServerResourceType|
|QueryPoolBusyThreads|Sì|Thread occupati pool di query|Conteggio|Media|Numero dei thread occupati nel pool dei thread di query.|ServerResourceType|
|QueryPoolIdleThreads|Sì|Thread: thread inattivi nel pool di query|Conteggio|Media|Numero di thread inattivi per i processi di I/O nel pool dei thread di elaborazione.|ServerResourceType|
|QueryPoolJobQueueLength|Sì|Thread: lunghezza coda processi nel pool di query|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|ServerResourceType|
|Quota|Sì|Memoria: Quota|Byte|Media|Quota di memoria corrente, in byte. Le quote di memoria sono note anche come concessioni di memoria o prenotazioni di memoria.|ServerResourceType|
|QuotaBlocked|Sì|Memoria: quota bloccata|Conteggio|Media|Numero corrente di richieste di quota bloccate fino a quando non vengono liberate altre quote di memoria.|ServerResourceType|
|RowsConvertedPerSec|Sì|Elaborazione: righe convertite al secondo|Conteggio al secondo|Media|Velocità di conversione delle righe durante l'elaborazione.|ServerResourceType|
|RowsReadPerSec|Sì|Elaborazione: righe lette al secondo|Conteggio al secondo|Media|Velocità di lettura delle righe da tutti i database relazionali.|ServerResourceType|
|RowsWrittenPerSec|Sì|Elaborazione: righe scritte al secondo|Conteggio al secondo|Media|Velocità di scrittura delle righe durante l'elaborazione.|ServerResourceType|
|ShortParsingBusyThreads|Sì|Thread: thread occupati per analisi dei thread brevi|Conteggio|Media|Numero dei thread occupati nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingIdleThreads|Sì|Thread: thread inattivi per analisi dei thread brevi|Conteggio|Media|Numero dei thread inattivi nel pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|ShortParsingJobQueueLength|Sì|Thread: lunghezza coda processi di analisi dei thread brevi|Conteggio|Media|Numero dei processi nella coda del pool dei thread per l'analisi dei thread brevi.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sì|Connessioni riuscite al secondo|Conteggio al secondo|Media|Numero delle connessioni completate correttamente al secondo.|ServerResourceType|
|TotalConnectionFailures|Sì|Numero totale di errori di connessione|Conteggio|Media|Numero totale dei tentativi di connessione non riusciti.|ServerResourceType|
|TotalConnectionRequests|Sì|Numero totale di richieste di connessione|Conteggio|Media|Numero totale delle richieste di connessione in arrivo.|ServerResourceType|
|VertiPaqNonpaged|Sì|Memoria: VertiPaq non di paging|Byte|Media|Byte di memoria bloccata nel working set per l'uso da parte del motore in memoria.|ServerResourceType|
|VertiPaqPaged|Sì|Memoria: VertiPaq di paging|Byte|Media|Byte di memoria di paging in uso per i dati in memoria.|ServerResourceType|
|virtual_bytes_metric|Sì|Virtual Bytes|Byte|Media|Byte virtuali.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BackendDuration|Sì|Duration of Backend Requests (Durata delle richieste back-end)|Millisecondi|Media|Durata delle richieste back-end in millisecondi|Location, Hostname|
|Capacità|Sì|Capacità|Percentuale|Media|Metrica di utilizzo per il servizio ApiManagement|Location|
|Duration|Sì|Durata complessiva delle richieste del gateway|Millisecondi|Media|Durata complessiva delle richieste del gateway in millisecondi|Location, Hostname|
|EventHubDroppedEvents|Sì|Dropped EventHub Events (Eventi EventHub rimossi)|Conteggio|Totale|Numero di eventi ignorati a causa del raggiungimento del limite delle dimensioni della coda|Location|
|EventHubRejectedEvents|Sì|Rejected EventHub Events (Eventi EventHub rifiutati)|Conteggio|Totale|Numero di eventi EventHub rifiutati (configurazione errata o non autorizzata)|Location|
|EventHubSuccessfulEvents|Sì|Successful EventHub Events (Eventi EventHub riusciti)|Conteggio|Totale|Numero di eventi EventHub riusciti|Location|
|EventHubThrottledEvents|Sì|Throttled EventHub Events (Eventi EventHub limitati)|Conteggio|Totale|Numero di eventi EventHub limitati|Location|
|EventHubTimedoutEvents|Sì|Timed Out EventHub Events (Timeout eventi EventHub)|Conteggio|Totale|Numero di eventi EventHub di timeout|Location|
|EventHubTotalBytesSent|Sì|Size of EventHub Events (Dimensione degli eventi EventHub)|Byte|Totale|Dimensioni totali degli eventi EventHub in byte|Location|
|EventHubTotalEvents|Sì|Total EventHub Events (Eventi EventHub totali)|Conteggio|Totale|Numero di eventi inviati a EventHub|Location|
|EventHubTotalFailedEvents|Sì|Failed EventHub Events (Eventi EventHub non riusciti)|Conteggio|Totale|Numero di eventi EventHub non riusciti|Location|
|FailedRequests|Sì|Failed Gateway Requests (Richieste gateway non riuscite) (deprecato)|Conteggio|Totale|Numero di errori nelle richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Location, Hostname|
|Problemidi|Sì|Stato connettività di rete delle risorse (anteprima)|Conteggio|Media|Stato della connettività di rete dei tipi di risorsa dipendenti dal servizio gestione API|Località, ResourceType|
|OtherRequests|Sì|Other Gateway Requests (Altre richieste del gateway) (deprecato)|Conteggio|Totale|Numero di altre richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Location, Hostname|
|Requests|Sì|Requests|Conteggio|Totale|Metriche delle richieste del gateway con più dimensioni|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Sì|Successful Gateway Requests (Richieste gateway riuscite) (deprecato)|Conteggio|Totale|Numero di richieste gateway riuscite: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Location, Hostname|
|TotalRequests|Sì|Total Gateway Requests (Totale richieste gateway) (deprecato)|Conteggio|Totale|Numero di richieste del gateway: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Location, Hostname|
|UnauthorizedRequests|Sì|Unauthorized Gateway Requests (Richieste del gateway non autorizzate) (deprecato)|Conteggio|Totale|Numero di richieste del gateway non autorizzate: usare la metrica di richiesta MULTIDIMENSIONE con la dimensione GatewayResponseCodeCategory|Location, Hostname|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Sì|HttpIncomingRequestCount|Conteggio|Conteggio|Numero totale di richieste HTTP in ingresso.|StatusCode, autenticazione|
|HttpIncomingRequestDuration|Sì|HttpIncomingRequestDuration|Conteggio|Media|Latenza su una richiesta HTTP.|StatusCode, autenticazione|
|ThrottledHttpRequestCount|Sì|ThrottledHttpRequestCount|Conteggio|Conteggio|Richieste http limitate.|Nessuna dimensione|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|JVM. GC. Live. Data. size|Sì|JVM. GC. Live. Data. size|Byte|Media|Dimensioni del pool di memoria di generazione precedente dopo un catalogo globale completo|Distribuzione, AppName, Pod|
|JVM. GC. max. Data. size|Sì|JVM. GC. max. Data. size|Byte|Media|Dimensioni massime del pool di memoria di generazione precedente|Distribuzione, AppName, Pod|
|JVM. GC. memory. allocato|Sì|JVM. GC. memory. allocato|Byte|Massimo|Incrementato per un aumento delle dimensioni del pool di memoria di generazione giovani dopo un GC a prima del successivo|Distribuzione, AppName, Pod|
|JVM. GC. memory. promossi|Sì|JVM. GC. memory. promossi|Byte|Massimo|Numero di aumenti positivi nelle dimensioni del pool di memoria precedente prima della GC dopo GC|Distribuzione, AppName, Pod|
|JVM. GC. pause. Total. Count|Sì|JVM. GC. pause. Total. Count|Conteggio|Totale|GC Pause Count (Conteggio pausa GC)|Distribuzione, AppName, Pod|
|JVM. GC. pause. Total. Time|Sì|JVM. GC. pause. Total. Time|Millisecondi|Totale|GC Pause Total Time (Tempo totale pausa GC)|Distribuzione, AppName, Pod|
|JVM. memory. commit|Sì|JVM. memory. commit|Byte|Media|Memoria assegnata a JVM in byte|Distribuzione, AppName, Pod|
|JVM. memory. max|Sì|JVM. memory. max|Byte|Massimo|Quantità massima di memoria in byte che può essere utilizzata per la gestione della memoria|Distribuzione, AppName, Pod|
|JVM. memory. used|Sì|JVM. memory. used|Byte|Media|Memoria app utilizzata in byte|Distribuzione, AppName, Pod|
|Process. CPU. Usage|Sì|Process. CPU. Usage|Percentuale|Media|Percentuale di utilizzo CPU JVM app|Distribuzione, AppName, Pod|
|System. CPU. Usage|Sì|System. CPU. Usage|Percentuale|Media|Utilizzo della CPU recente per l'intero sistema|Distribuzione, AppName, Pod|
|Tomcat. Global. Error|Sì|Tomcat. Global. Error|Conteggio|Totale|Tomcat Global Error (Errore globale Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Global. received|Sì|Tomcat. Global. received|Byte|Totale|Tomcat Total Received Bytes (Byte totali ricevuti Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Global. Request. AVG. Time|Sì|Tomcat. Global. Request. AVG. Time|Millisecondi|Media|Tomcat Request Average Time (Tempo medio richiesta Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Global. Request. max|Sì|Tomcat. Global. Request. max|Millisecondi|Massimo|Tomcat Request Max Time (Tempo max richiesta Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Global. Request. Total. Count|Sì|Tomcat. Global. Request. Total. Count|Conteggio|Totale|Tomcat Request Total Count (Conteggio totale richieste Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Global. Request. Total. Time|Sì|Tomcat. Global. Request. Total. Time|Millisecondi|Totale|Tempo totale richiesta Tomcat|Distribuzione, AppName, Pod|
|Tomcat. Global. Sent|Sì|Tomcat. Global. Sent|Byte|Totale|Tomcat Total Sent Bytes (Byte totali inviati Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Sessions. Active. Current|Sì|Tomcat. Sessions. Active. Current|Conteggio|Totale|Conteggio attivo sessione Tomcat|Distribuzione, AppName, Pod|
|Tomcat. Sessions. Active. max|Sì|Tomcat. Sessions. Active. max|Conteggio|Totale|Tomcat Session Max Active Count (Conteggio max attivo sessioni Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Sessions. Alive. max|Sì|Tomcat. Sessions. Alive. max|Millisecondi|Massimo|Tomcat Session Max Alive Time (Tempo max attivo sessioni Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Sessions. created|Sì|Tomcat. Sessions. created|Conteggio|Totale|Tomcat Session Created Count (Conteggio creazione sessioni Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Sessions. scaduto|Sì|Tomcat. Sessions. scaduto|Conteggio|Totale|Tomcat Session Expired Count (Conteggio scadenza sessioni Tomcat)|Distribuzione, AppName, Pod|
|Tomcat. Sessions. rifiutato|Sì|Tomcat. Sessions. rifiutato|Conteggio|Totale|Tomcat Session Rejected Count (Conteggio rifiuto sessioni Tomcat)|Distribuzione, AppName, Pod|
|tomcat.threads.config. max|Sì|tomcat.threads.config. max|Conteggio|Totale|Numero massimo thread di configurazione Tomcat|Distribuzione, AppName, Pod|
|Tomcat. Threads. Current|Sì|Tomcat. Threads. Current|Conteggio|Totale|Conteggio thread correnti Tomcat|Distribuzione, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|TotalJob|Sì|Processi totali|Conteggio|Totale|Numero totale di processi|Runbook, stato|
|TotalUpdateDeploymentMachineRuns|Sì|Total Update Deployment Machine Runs (Esecuzioni totali della distribuzione di aggiornamenti del computer)|Conteggio|Totale|Il computer di distribuzione degli aggiornamenti software totale viene eseguito in un'esecuzione di distribuzione degli aggiornamenti software|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Sì|Total Update Deployment Runs (Esecuzioni totali della distribuzione di aggiornamenti)|Conteggio|Totale|Totale esecuzioni di distribuzioni di aggiornamenti software|SoftwareUpdateConfigurationName, stato|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CoreCount|No|Numero di core dedicati|Conteggio|Totale|Numero totale di core dedicati nell'account Batch|Nessuna dimensione|
|CreatingNodeCount|No|Numero nodi creati|Conteggio|Totale|Il numero di nodi in fase di creazione|Nessuna dimensione|
|IdleNodeCount|No|Numero nodi inattivi|Conteggio|Totale|Il numero di nodi inattivi|Nessuna dimensione|
|JobDeleteCompleteEvent|Sì|Eventi eliminazione processi completati|Conteggio|Totale|Numero totale dei processi eliminati.|jobId|
|JobDeleteStartEvent|Sì|Eventi eliminazione processi avviati|Conteggio|Totale|Numero totale di processi da eliminare.|jobId|
|JobDisableCompleteEvent|Sì|Eventi disabilitazione processi completati|Conteggio|Totale|Numero totale dei processi disabilitati.|jobId|
|JobDisableStartEvent|Sì|Eventi disabilitazione processi avviati|Conteggio|Totale|Numero totale di processi da disabilitare.|jobId|
|JobStartEvent|Sì|Eventi di avvio processi|Conteggio|Totale|Numero totale di processi avviati.|jobId|
|JobTerminateCompleteEvent|Sì|Eventi terminazione processi completati|Conteggio|Totale|Numero totale di processi terminati.|jobId|
|JobTerminateStartEvent|Sì|Eventi terminazione processi avviati|Conteggio|Totale|Numero totale di processi da terminare.|jobId|
|LeavingPoolNodeCount|No|Numero nodi usciti dal pool|Conteggio|Totale|Il numero di nodi in uscita dal pool|Nessuna dimensione|
|LowPriorityCoreCount|No|Numero di core a bassa priorità|Conteggio|Totale|Numero totale di core a bassa priorità nell'account Batch|Nessuna dimensione|
|OfflineNodeCount|No|Numero nodi offline|Conteggio|Totale|Il numero di nodi offline|Nessuna dimensione|
|PoolCreateEvent|Sì|Eventi pool creati|Conteggio|Totale|Il numero totale di pool che sono stati creati|poolId|
|PoolDeleteCompleteEvent|Sì|Eventi eliminazione pool completati|Conteggio|Totale|Il numero totale di eliminazioni di pool completate|poolId|
|PoolDeleteStartEvent|Sì|Eventi eliminazione pool avviati|Conteggio|Totale|Il numero totale di eliminazioni di pool avviate|poolId|
|PoolResizeCompleteEvent|Sì|Eventi ridimensionamento pool completati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool completati|poolId|
|PoolResizeStartEvent|Sì|Eventi ridimensionamento pool avviati|Conteggio|Totale|Il numero totale di ridimensionamenti dei pool avviati|poolId|
|PreemptedNodeCount|No|Numero di nodi annullati|Conteggio|Totale|Numero di nodi annullati|Nessuna dimensione|
|RebootingNodeCount|No|Numero nodi riavviati|Conteggio|Totale|Il numero di nodi in fase di riavvio|Nessuna dimensione|
|ReimagingNodeCount|No|Numero nodi con immagine ricreata|Conteggio|Totale|Il numero di nodi in fase di ricreazione dell'immagine|Nessuna dimensione|
|RunningNodeCount|No|Numero nodi eseguiti|Conteggio|Totale|Il numero di nodi in esecuzione|Nessuna dimensione|
|StartingNodeCount|No|Numero nodi avviati|Conteggio|Totale|Il numero di nodi in fase di avvio|Nessuna dimensione|
|StartTaskFailedNodeCount|No|Numero nodi con attività di avvio non riuscita|Conteggio|Totale|Il numero di nodi in cui l'attività di avvio non è riuscita|Nessuna dimensione|
|TaskCompleteEvent|Sì|Eventi attività completate|Conteggio|Totale|Il numero totale di attività completate|poolId, jobId|
|TaskFailEvent|Sì|Eventi attività non riuscite|Conteggio|Totale|Il numero totale di attività completate con lo stato Non riuscito|poolId, jobId|
|TaskStartEvent|Sì|Eventi attività avviate|Conteggio|Totale|Il numero totale di attività avviate|poolId, jobId|
|TotalLowPriorityNodeCount|No|Numero di nodi a bassa priorità|Conteggio|Totale|Numero totale di nodi a bassa priorità nell'account Batch|Nessuna dimensione|
|TotalNodeCount|No|Numero di nodi dedicati|Conteggio|Totale|Numero totale di nodi dedicati nell'account Batch|Nessuna dimensione|
|UnusableNodeCount|No|Numero nodi non usabili|Conteggio|Totale|Il numero di nodi che non possono essere usati|Nessuna dimensione|
|WaitingForStartTaskNodeCount|No|Numero nodi in attesa dell'attività di avvio|Conteggio|Totale|Il numero di nodi in attesa del completamento dell'attività di avvio|Nessuna dimensione|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Active Cores (Core attivi)|Sì|Active Cores (Core attivi)|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Active Nodes (Nodi attivi)|Sì|Active Nodes (Nodi attivi)|Conteggio|Media|Il numero di nodi in esecuzione|Scenario, clustername|
|Idle Cores (Core inattivi)|Sì|Idle Cores (Core inattivi)|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Idle Nodes (Nodi inattivi)|Sì|Idle Nodes (Nodi inattivi)|Conteggio|Media|Il numero di nodi inattivi|Scenario, clustername|
|Job Completed (Processo completato)|Sì|Job Completed (Processo completato)|Conteggio|Totale|Numero di processi completati|Scenario, clustername, ResultType|
|Processo inviato|Sì|Processo inviato|Conteggio|Totale|Numero di processi inviati|Scenario, clustername|
|Leaving Cores (Core di uscita)|Sì|Leaving Cores (Core di uscita)|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Leaving Nodes (Nodi di uscita)|Sì|Leaving Nodes (Nodi di uscita)|Conteggio|Media|Numero di nodi in uscita|Scenario, clustername|
|Preempted Cores (Core superati)|Sì|Preempted Cores (Core superati)|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Preempted Nodes (Nodi superati)|Sì|Preempted Nodes (Nodi superati)|Conteggio|Media|Numero di nodi annullati|Scenario, clustername|
|Quota Utilization Percentage (Percentuale di utilizzo quota)|Sì|Quota Utilization Percentage (Percentuale di utilizzo quota)|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|
|Core totali|Sì|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Totale nodi|Sì|Totale nodi|Conteggio|Media|Numero di nodi totali|Scenario, clustername|
|Unusable Cores (Core non utilizzabili)|Sì|Unusable Cores (Core non utilizzabili)|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Unusable Nodes (Nodi non utilizzabili)|Sì|Unusable Nodes (Nodi non utilizzabili)|Conteggio|Media|Il numero di nodi che non possono essere usati|Scenario, clustername|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Sì|Conteggio elaborazione broadcast|Conteggio|Media|Numero di transazioni elaborate|Nodo, canale, tipo, stato|
|ConnectionAccepted|Sì|Accepted Connections (Connessioni accettate)|Conteggio|Totale|Accepted Connections (Connessioni accettate)|Nodo|
|ConnectionActive|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nodo|
|ConnectionHandled|Sì|Handled Connections (Connessioni gestite)|Conteggio|Totale|Handled Connections (Connessioni gestite)|Nodo|
|ConsensusEtcdraftCommittedBlockNumber|Sì|Numero blocco con commit Etcdraft di consenso|Conteggio|Media|Numero di blocco dell'ultimo blocco di cui è stato eseguito il commit|Nodo, canale|
|CpuUsagePercentageInDouble|Sì|CPU Usage Percentage (Percentuale di utilizzo CPU)|Percentuale|Massimo|CPU Usage Percentage (Percentuale di utilizzo CPU)|Nodo|
|EndorserEndorsementFailures|Sì|Verifica dell'autenticità degli sponsor|Conteggio|Media|Numero di convalide non riuscite.|Node, Channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Sì|Leader delle elezioni per gossip leader|Conteggio|Totale|Peer è leader (1) o follower (0)|Nodo, canale|
|GossipMembershipTotalPeersKnown|Sì|Totale peer di appartenenza a Gossip noti|Conteggio|Media|Totale peer noti|Nodo, canale|
|GossipStateHeight|Sì|Altezza stato gossip|Conteggio|Media|Altezza Ledger corrente|Nodo, canale|
|IOReadBytes|Sì|IO Read Bytes (Byte operazioni di I/O in lettura)|Byte|Totale|IO Read Bytes (Byte operazioni di I/O in lettura)|Nodo|
|IOWriteBytes|Sì|IO Write Bytes (Byte operazioni di I/O in scrittura)|Byte|Totale|IO Write Bytes (Byte operazioni di I/O in scrittura)|Nodo|
|LedgerTransactionCount|Sì|Conteggio transazioni Ledger|Conteggio|Media|Numero di transazioni elaborate|Node, Channel, transaction_type, chaincode, validation_code|
|MemoryLimit|Sì|Limite memoria|Byte|Media|Limite memoria|Nodo|
|MemoryUsage|Sì|Utilizzo della memoria|Byte|Media|Utilizzo memoria|Nodo|
|MemoryUsagePercentageInDouble|Sì|Memory Usage Percentage (Percentuale di utilizzo memoria)|Percentuale|Media|Memory Usage Percentage (Percentuale di utilizzo memoria)|Nodo|
|PendingTransactions|Sì|Pending Transactions (Transazioni in sospeso)|Conteggio|Media|Pending Transactions (Transazioni in sospeso)|Nodo|
|ProcessedBlocks|Sì|Processed Blocks (Blocchi elaborati)|Conteggio|Totale|Processed Blocks (Blocchi elaborati)|Nodo|
|ProcessedTransactions|Sì|Processed Transactions (Transazioni elaborate)|Conteggio|Totale|Processed Transactions (Transazioni elaborate)|Nodo|
|QueuedTransactions|Sì|Queued Transactions (Transazioni in coda)|Conteggio|Media|Queued Transactions (Transazioni in coda)|Nodo|
|RequestHandled|Sì|Handled Requests (Richieste gestite)|Conteggio|Totale|Handled Requests (Richieste gestite)|Nodo|
|StorageUsage|Sì|Utilizzo dello spazio di archiviazione|Byte|Media|Utilizzo dello spazio di archiviazione|Nodo|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|cachehits|Sì|Riscontri cache|Conteggio|Totale||ShardId|
|cachehits0|Sì|Riscontri cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachehits1|Sì|Riscontri cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachehits2|Sì|Riscontri cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachehits3|Sì|Riscontri cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachehits4|Sì|Riscontri cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachehits5|Sì|Riscontri cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachehits6|Sì|Riscontri cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachehits7|Sì|Riscontri cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachehits8|Sì|Riscontri cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachehits9|Sì|Riscontri cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cacheLatency|Sì|Microsecondi di latenza della cache (anteprima)|Conteggio|Media||ShardId|
|cachemisses|Sì|Mancati riscontri nella cache|Conteggio|Totale||ShardId|
|cachemisses0|Sì|Mancati riscontri nella cache (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|cachemisses1|Sì|Mancati riscontri nella cache (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|cachemisses2|Sì|Mancati riscontri nella cache (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|cachemisses3|Sì|Mancati riscontri nella cache (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|cachemisses4|Sì|Mancati riscontri nella cache (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|cachemisses5|Sì|Mancati riscontri nella cache (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|cachemisses6|Sì|Mancati riscontri nella cache (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|cachemisses7|Sì|Mancati riscontri nella cache (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|cachemisses8|Sì|Mancati riscontri nella cache (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|cachemisses9|Sì|Mancati riscontri nella cache (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|cachemissrate|Sì|Frequenza di mancata memorizzazione nella cache|Percentuale|cachemissrate||ShardId|
|cacheRead|Sì|Lettura da cache|Byte al secondo|Massimo||ShardId|
|cacheRead0|Sì|Lettura da cache (partizione 0)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead1|Sì|Lettura da cache (partizione 1)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead2|Sì|Lettura da cache (partizione 2)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead3|Sì|Lettura da cache (partizione 3)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead4|Sì|Lettura da cache (partizione 4)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead5|Sì|Lettura da cache (partizione 5)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead6|Sì|Lettura da cache (partizione 6)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead7|Sì|Lettura da cache (partizione 7)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead8|Sì|Lettura da cache (partizione 8)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheRead9|Sì|Lettura da cache (partizione 9)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite|Sì|Scrittura nella cache|Byte al secondo|Massimo||ShardId|
|cacheWrite0|Sì|Scrittura nella cache (partizione 0)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite1|Sì|Scrittura nella cache (partizione 1)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite2|Sì|Scrittura nella cache (partizione 2)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite3|Sì|Scrittura nella cache (partizione 3)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite4|Sì|Scrittura nella cache (partizione 4)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite5|Sì|Scrittura nella cache (partizione 5)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite6|Sì|Scrittura nella cache (partizione 6)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite7|Sì|Scrittura nella cache (partizione 7)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite8|Sì|Scrittura nella cache (partizione 8)|Byte al secondo|Massimo||Nessuna dimensione|
|cacheWrite9|Sì|Scrittura nella cache (partizione 9)|Byte al secondo|Massimo||Nessuna dimensione|
|connectedclients|Sì|Client connessi|Conteggio|Massimo||ShardId|
|connectedclients0|Sì|Client connessi (partizione 0)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients1|Sì|Client connessi (partizione 1)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients2|Sì|Client connessi (partizione 2)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients3|Sì|Client connessi (partizione 3)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients4|Sì|Client connessi (partizione 4)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients5|Sì|Client connessi (partizione 5)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients6|Sì|Client connessi (partizione 6)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients7|Sì|Client connessi (partizione 7)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients8|Sì|Client connessi (partizione 8)|Conteggio|Massimo||Nessuna dimensione|
|connectedclients9|Sì|Client connessi (partizione 9)|Conteggio|Massimo||Nessuna dimensione|
|errori|Sì|Errors|Conteggio|Massimo||ShardId, ErrorType|
|evictedkeys|Sì|Chiavi rimosse|Conteggio|Totale||ShardId|
|evictedkeys0|Sì|Chiavi rimosse (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys1|Sì|Chiavi rimosse (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys2|Sì|Chiavi rimosse (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys3|Sì|Chiavi rimosse (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys4|Sì|Chiavi rimosse (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys5|Sì|Chiavi rimosse (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys6|Sì|Chiavi rimosse (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys7|Sì|Chiavi rimosse (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys8|Sì|Chiavi rimosse (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|evictedkeys9|Sì|Chiavi rimosse (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys|Sì|Chiavi scadute|Conteggio|Totale||ShardId|
|expiredkeys0|Sì|Chiavi scadute (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys1|Sì|Chiavi scadute (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys2|Sì|Chiavi scadute (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys3|Sì|Chiavi scadute (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys4|Sì|Chiavi scadute (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys5|Sì|Chiavi scadute (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys6|Sì|Chiavi scadute (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys7|Sì|Chiavi scadute (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys8|Sì|Chiavi scadute (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|expiredkeys9|Sì|Chiavi scadute (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|getcommands|Sì|Operazioni Get|Conteggio|Totale||ShardId|
|getcommands0|Sì|Operazioni Get (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|getcommands1|Sì|Operazioni Get (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|getcommands2|Sì|Operazioni Get (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|getcommands3|Sì|Operazioni Get (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|getcommands4|Sì|Operazioni Get (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|getcommands5|Sì|Operazioni Get (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|getcommands6|Sì|Operazioni Get (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|getcommands7|Sì|Operazioni Get (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|getcommands8|Sì|Operazioni Get (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|getcommands9|Sì|Operazioni Get (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|operationsPerSecond|Sì|Operazioni al secondo|Conteggio|Massimo||ShardId|
|operationsPerSecond0|Sì|Operazioni al secondo (partizione 0)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond1|Sì|Operazioni al secondo (partizione 1)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond2|Sì|Operazioni al secondo (partizione 2)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond3|Sì|Operazioni al secondo (partizione 3)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond4|Sì|Operazioni al secondo (partizione 4)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond5|Sì|Operazioni al secondo (partizione 5)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond6|Sì|Operazioni al secondo (partizione 6)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond7|Sì|Operazioni al secondo (partizione 7)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond8|Sì|Operazioni al secondo (partizione 8)|Conteggio|Massimo||Nessuna dimensione|
|operationsPerSecond9|Sì|Operazioni al secondo (partizione 9)|Conteggio|Massimo||Nessuna dimensione|
|percentProcessorTime|Sì|CPU|Percentuale|Massimo||ShardId|
|percentProcessorTime0|Sì|CPU (partizione 0)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime1|Sì|CPU (partizione 1)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime2|Sì|CPU (partizione 2)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime3|Sì|CPU (partizione 3)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime4|Sì|CPU (partizione 4)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime5|Sì|CPU (partizione 5)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime6|Sì|CPU (partizione 6)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime7|Sì|CPU (partizione 7)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime8|Sì|CPU (partizione 8)|Percentuale|Massimo||Nessuna dimensione|
|percentProcessorTime9|Sì|CPU (partizione 9)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad|Sì|Carico server|Percentuale|Massimo||ShardId|
|serverLoad0|Sì|Carico server (partizione 0)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad1|Sì|Carico server (partizione 1)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad2|Sì|Carico server (partizione 2)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad3|Sì|Carico server (partizione 3)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad4|Sì|Carico server (partizione 4)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad5|Sì|Carico server (partizione 5)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad6|Sì|Carico server (partizione 6)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad7|Sì|Carico server (partizione 7)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad8|Sì|Carico server (partizione 8)|Percentuale|Massimo||Nessuna dimensione|
|serverLoad9|Sì|Carico server (partizione 9)|Percentuale|Massimo||Nessuna dimensione|
|setcommands|Sì|Set|Conteggio|Totale||ShardId|
|setcommands0|Sì|Operazioni Set (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|setcommands1|Sì|Operazioni Set (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|setcommands2|Sì|Operazioni Set (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|setcommands3|Sì|Operazioni Set (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|setcommands4|Sì|Operazioni Set (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|setcommands5|Sì|Operazioni Set (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|setcommands6|Sì|Operazioni Set (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|setcommands7|Sì|Operazioni Set (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|setcommands8|Sì|Operazioni Set (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|setcommands9|Sì|Operazioni Set (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed|Sì|Totale operazioni|Conteggio|Totale||ShardId|
|totalcommandsprocessed0|Sì|Totale operazioni (partizione 0)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed1|Sì|Totale operazioni (partizione 1)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed2|Sì|Totale operazioni (partizione 2)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed3|Sì|Totale operazioni (partizione 3)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed4|Sì|Totale operazioni (partizione 4)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed5|Sì|Totale operazioni (partizione 5)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed6|Sì|Totale operazioni (partizione 6)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed7|Sì|Totale operazioni (partizione 7)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed8|Sì|Totale operazioni (partizione 8)|Conteggio|Totale||Nessuna dimensione|
|totalcommandsprocessed9|Sì|Totale operazioni (partizione 9)|Conteggio|Totale||Nessuna dimensione|
|totalkeys|Sì|Totale chiavi|Conteggio|Massimo||ShardId|
|totalkeys0|Sì|Totale chiavi (partizione 0)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys1|Sì|Totale chiavi (partizione 1)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys2|Sì|Totale chiavi (partizione 2)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys3|Sì|Totale chiavi (partizione 3)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys4|Sì|Totale chiavi (partizione 4)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys5|Sì|Totale chiavi (partizione 5)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys6|Sì|Totale chiavi (partizione 6)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys7|Sì|Totale chiavi (partizione 7)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys8|Sì|Totale chiavi (partizione 8)|Conteggio|Massimo||Nessuna dimensione|
|totalkeys9|Sì|Totale chiavi (partizione 9)|Conteggio|Massimo||Nessuna dimensione|
|usedmemory|Sì|Memoria utilizzata|Byte|Massimo||ShardId|
|usedmemory0|Sì|Memoria usata (partizione 0)|Byte|Massimo||Nessuna dimensione|
|usedmemory1|Sì|Memoria usata (partizione 1)|Byte|Massimo||Nessuna dimensione|
|usedmemory2|Sì|Memoria usata (partizione 2)|Byte|Massimo||Nessuna dimensione|
|usedmemory3|Sì|Memoria usata (partizione 3)|Byte|Massimo||Nessuna dimensione|
|usedmemory4|Sì|Memoria usata (partizione 4)|Byte|Massimo||Nessuna dimensione|
|usedmemory5|Sì|Memoria usata (partizione 5)|Byte|Massimo||Nessuna dimensione|
|usedmemory6|Sì|Memoria usata (partizione 6)|Byte|Massimo||Nessuna dimensione|
|usedmemory7|Sì|Memoria usata (partizione 7)|Byte|Massimo||Nessuna dimensione|
|usedmemory8|Sì|Memoria usata (partizione 8)|Byte|Massimo||Nessuna dimensione|
|usedmemory9|Sì|Memoria usata (partizione 9)|Byte|Massimo||Nessuna dimensione|
|usedmemorypercentage|Sì|Percentuale memoria usata|Percentuale|Massimo||ShardId|
|usedmemoryRss|Sì|Memoria utilizzata RSS|Byte|Massimo||ShardId|
|usedmemoryRss0|Sì|Memoria usata RSS (partizione 0)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss1|Sì|Memoria usata RSS (partizione 1)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss2|Sì|Memoria usata RSS (partizione 2)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss3|Sì|Memoria usata RSS (partizione 3)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss4|Sì|Memoria usata RSS (partizione 4)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss5|Sì|Memoria usata RSS (partizione 5)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss6|Sì|Memoria usata RSS (partizione 6)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss7|Sì|Memoria usata RSS (partizione 7)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss8|Sì|Memoria usata RSS (partizione 8)|Byte|Massimo||Nessuna dimensione|
|usedmemoryRss9|Sì|Memoria usata RSS (partizione 9)|Byte|Massimo||Nessuna dimensione|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Sì|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|No|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|RoleInstanceId|
|Disk Write Bytes/Sec|No|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|RoleInstanceId|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|RoleInstanceId|
|Rete in ingresso|Sì|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|RoleInstanceId|
|Rete in uscita|Sì|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|RoleInstanceId|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|No|Lettura disco|Byte al secondo|Media|Numero medio di byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero di IOPS letti dal disco|Nessuna dimensione|
|Disk Write Bytes/Sec|No|Scrittura disco|Byte al secondo|Media|Numero medio di byte scritti sul disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero di IOPS scritti sul disco|Nessuna dimensione|
|Rete in ingresso|Sì|Rete in ingresso|Byte|Totale|Numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Sì|Rete in uscita|Byte|Totale|Numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|Percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Capacità usata|Byte|Media|Capacità usata account|Nessuna dimensione|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|BlobCapacity|No|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|No|Numero di BLOB|Conteggio|Media|Numero di BLOB nel servizio BLOB dell'account di archiviazione.|BlobType, livello|
|ContainerCount|Sì|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nel servizio BLOB dell'account di archiviazione.|Nessuna dimensione|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|IndexCapacity|No|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata dall'indice ADLS Gen2 (gerarchico) in byte.|Nessuna dimensione|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|FileCapacity|No|Capacità file|Byte|Media|Quantità di memoria usata dal servizio file dell'account di archiviazione, in byte.|FileShare|
|FileCount|No|Numero di file|Conteggio|Media|Numero di file nel servizio file dell'account di archiviazione.|FileShare|
|FileShareCount|No|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nel servizio file dell'account di archiviazione.|Nessuna dimensione|
|FileShareQuota|No|Dimensioni quota di condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|FileShareSnapshotCount|No|Numero di snapshot in condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|No|Dimensioni snapshot in condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Sì|Capacità coda|Byte|Media|Quantità di memoria usata da Servizio di accodamento dell'account di archiviazione, in byte.|Nessuna dimensione|
|QueueCount|Sì|Numero di coda|Conteggio|Media|Numero di coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|QueueMessageCount|Sì|Numero di messaggi in coda|Conteggio|Media|Numero approssimativo dei messaggi in coda in Servizio di accodamento dell'account di archiviazione.|Nessuna dimensione|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end delle richieste riuscite effettuate a un servizio di archiviazione o all'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Latenza usata dall'archiviazione di Azure per elaborare una richiesta con esito positivo, in millisecondi. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Sì|Capacità tabella|Byte|Media|Quantità di memoria usata dal servizio tabelle dell'account di archiviazione, in byte.|Nessuna dimensione|
|TableCount|Sì|Numero di tabella|Conteggio|Media|Numero di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|TableEntityCount|Sì|Numero di entità di tabella|Conteggio|Media|Numero di entità di tabella nel servizio tabelle dell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BlockedCalls|Sì|Chiamate bloccate|Conteggio|Totale|Numero di chiamate che hanno superato il limite di frequenza o di quota.|ApiName, OperationName, Region|
|CharactersTrained|Sì|Characters Trained (Caratteri di cui è stato eseguito il training)|Conteggio|Totale|Numero totale di caratteri sottoposti a training.|ApiName, OperationName, Region|
|CharactersTranslated|Sì|Caratteri convertiti|Conteggio|Totale|Numero totale di caratteri nella richiesta di testo in ingresso.|ApiName, OperationName, Region|
|ClientErrors|Sì|Errori client|Conteggio|Totale|Numero di chiamate con errore sul lato client (codice di risposta HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Sì|Dati in entrata|Byte|Totale|Dimensione in byte dei dati in entrata.|ApiName, OperationName, Region|
|DataOut|Sì|Dati in uscita|Byte|Totale|Dimensione in byte dei dati in uscita.|ApiName, OperationName, Region|
|Latenza|Sì|Latenza|Millisecondi|Media|Latenza in millisecondi.|ApiName, OperationName, Region|
|ProcessedImages|Sì|Immagini elaborate|Conteggio|Totale| Numero di transazioni per l'elaborazione di immagini.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|Sì|Errori server|Conteggio|Totale|Numero di chiamate con errore interno del servizio (codice di risposta HTTP 5xx).|ApiName, OperationName, Region|
|SpeechSessionDuration|Sì|Durata della sessione vocale|Secondi|Totale|Durata totale della sessione vocale in secondi.|ApiName, OperationName, Region|
|SuccessfulCalls|Sì|Chiamate riuscite|Conteggio|Totale|Numero di chiamate riuscite.|ApiName, OperationName, Region|
|TotalCalls|Sì|Totale chiamate|Conteggio|Totale|Numero totale di chiamate.|ApiName, OperationName, Region|
|TotalErrors|Sì|Totale errori|Conteggio|Totale|Numero totale di chiamate con risposta di errore (codice di risposta HTTP 4xx o 5xx).|ApiName, OperationName, Region|
|TotalTokenCalls|Sì|Totale chiamate token|Conteggio|Totale|Numero totale di chiamate token.|ApiName, OperationName, Region|
|TotalTransactions|Sì|Totale transazioni|Conteggio|Totale|Numero totale di transazioni.|Nessuna dimensione|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Crediti CPU usati|Sì|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|
|Crediti CPU rimanenti|Sì|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Profondità coda per un disco dati|Sì|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti da disco/sec per un disco dati|Sì|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura da disco/sec per un disco dati|Sì|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco/sec per un disco dati|Sì|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco/sec per un disco dati|Sì|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte letti da disco|Sì|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Byte scritti su disco|Sì|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Flussi in ingresso|Sì|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in ingresso|Sì|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuna dimensione|
|Rete in ingresso|Sì|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuna dimensione|
|Rete in ingresso (totale)|Sì|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Sì|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuna dimensione|
|Rete in uscita (totale)|Sì|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Profondità coda per un disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per un disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per un disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Flussi in uscita|Sì|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in uscita|Sì|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Profondità coda per disco dati|Sì|Profondità coda per un disco dati (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco|Sì|Byte letti da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Sì|Operazioni di lettura da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Sì|Byte scritti su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Sì|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Riscontri letture cache per un disco dati Premium|Sì|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN|
|Mancati riscontri letture cache per un disco dati Premium|Sì|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN|
|Riscontri letture cache per un disco del sistema operativo Premium|Sì|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Sì|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Crediti CPU usati|Sì|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|
|Crediti CPU rimanenti|Sì|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Profondità coda per un disco dati|Sì|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN, VMName|
|Byte letti da disco/sec per un disco dati|Sì|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di lettura da disco/sec per un disco dati|Sì|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte scritti su disco/sec per un disco dati|Sì|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Operazioni di scrittura su disco/sec per un disco dati|Sì|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN, VMName|
|Byte letti da disco|Sì|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|VMName|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|VMName|
|Byte scritti su disco|Sì|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|VMName|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|VMName|
|Flussi in ingresso|Sì|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in ingresso|Sì|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|VMName|
|Rete in ingresso|Sì|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|VMName|
|Rete in ingresso (totale)|Sì|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|VMName|
|Rete in uscita|Sì|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|VMName|
|Rete in uscita (totale)|Sì|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|VMName|
|Profondità coda per un disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|VMName|
|Byte letti da disco/sec per un disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Byte scritti su disco/sec per un disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|VMName|
|Profondità coda per disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Flussi in uscita|Sì|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Numero massimo richieste di creazione flussi in uscita|Sì|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|VMName|
|Profondità coda per disco dati|Sì|Profondità coda per un disco dati (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco|Sì|Byte letti da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Sì|Operazioni di lettura da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Sì|Byte scritti su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Sì|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|VMName|
|Riscontri letture cache per un disco dati Premium|Sì|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN, VMName|
|Mancati riscontri letture cache per un disco dati Premium|Sì|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN, VMName|
|Riscontri letture cache per un disco del sistema operativo Premium|Sì|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|VMName|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Sì|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Crediti CPU usati|Sì|Crediti CPU usati|Conteggio|Media|Numero totale di crediti usati dalla macchina virtuale|Nessuna dimensione|
|Crediti CPU rimanenti|Sì|Crediti CPU rimanenti|Conteggio|Media|Numero totale di crediti disponibili per il burst|Nessuna dimensione|
|Profondità coda per un disco dati|Sì|Profondità coda per un disco dati (anteprima)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|LUN|
|Byte letti da disco/sec per un disco dati|Sì|Byte letti da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di lettura da disco/sec per un disco dati|Sì|Operazioni di lettura da disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte scritti su disco/sec per un disco dati|Sì|Byte scritti su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|LUN|
|Operazioni di scrittura su disco/sec per un disco dati|Sì|Operazioni di scrittura su disco/sec per un disco dati (anteprima)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|LUN|
|Byte letti da disco|Sì|Byte letti da disco|Byte|Totale|Byte letti dal disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Il numero di IOPS letti dal disco|Nessuna dimensione|
|Byte scritti su disco|Sì|Byte scritti su disco|Byte|Totale|Byte scritti su disco durante il periodo di monitoraggio|Nessuna dimensione|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Il numero di IOPS scritti sul disco|Nessuna dimensione|
|Flussi in ingresso|Sì|Flussi in ingresso|Conteggio|Media|I flussi in ingresso sono il numero di flussi correnti nella direzione in ingresso (il traffico passa alla macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in ingresso|Sì|Numero massimo richieste di creazione flussi in ingresso|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in ingresso (traffico verso la macchina virtuale)|Nessuna dimensione|
|Rete in ingresso|Sì|Rete in ingresso (fatturabile) (deprecata)|Byte|Totale|Numero di byte fatturabili ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso) (deprecato)|Nessuna dimensione|
|Rete in ingresso (totale)|Sì|Rete in ingresso (totale)|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete dalle macchine virtuali (traffico in ingresso)|Nessuna dimensione|
|Rete in uscita|Sì|Rete in uscita (fatturabile) (deprecata)|Byte|Totale|Il numero di byte fatturabili in tutte le interfacce di rete dalle macchine virtuali (traffico in uscita) (deprecato)|Nessuna dimensione|
|Rete in uscita (totale)|Sì|Rete in uscita (totale)|Byte|Totale|Il numero di byte inviati su tutte le interfacce di rete dalle macchine virtuali (traffico in uscita)|Nessuna dimensione|
|Profondità coda per un disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (anteprima)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per un disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per un disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per un disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per un disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (anteprima)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Profondità coda per disco del sistema operativo|Sì|Profondità coda per un disco del sistema operativo (deprecato)|Conteggio|Media|Profondità coda del disco del sistema operativo (o lunghezza coda)|Nessuna dimensione|
|Byte letti da disco/sec per disco del sistema operativo|Sì|Byte letti da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di lettura da disco/sec per disco del sistema operativo|Sì|Operazioni di lettura da disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Byte scritti su disco/sec per disco del sistema operativo|Sì|Byte scritti su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Operazioni di scrittura su disco/sec per disco del sistema operativo|Sì|Operazioni di scrittura su disco/sec per un disco del sistema operativo (deprecato)|Conteggio al secondo|Media|Scrivere IOPS da un singolo disco durante il periodo di monitoraggio per il disco del sistema operativo|Nessuna dimensione|
|Flussi in uscita|Sì|Flussi in uscita|Conteggio|Media|I flussi in uscita sono il numero di flussi correnti nella direzione in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Numero massimo richieste di creazione flussi in uscita|Sì|Numero massimo richieste di creazione flussi in uscita|Conteggio al secondo|Media|Velocità massima di creazione dei flussi in uscita (traffico in uscita dalla macchina virtuale)|Nessuna dimensione|
|Profondità coda per disco dati|Sì|Profondità coda per un disco dati (deprecato)|Conteggio|Media|Profondità coda del disco dati (o lunghezza coda)|SlotId|
|Byte letti da disco/sec per disco|Sì|Byte letti da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec letti da un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di lettura da disco/sec per disco|Sì|Operazioni di lettura da disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Leggere IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Byte scritti su disco/sec per disco|Sì|Byte scritti su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Byte/sec scritti su un singolo disco durante il periodo di monitoraggio|SlotId|
|Operazioni di scrittura su disco/sec per disco|Sì|Operazioni di scrittura su disco/sec per un disco dati (deprecato)|Conteggio al secondo|Media|Scrivi IOPS da un singolo disco durante il periodo di monitoraggio|SlotId|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|La percentuale di unità di calcolo allocate attualmente usate dalle macchine virtuali|Nessuna dimensione|
|Riscontri letture cache per un disco dati Premium|Sì|Riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco dati Premium|LUN|
|Mancati riscontri letture cache per un disco dati Premium|Sì|Mancati riscontri letture cache per un disco dati Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco dati Premium|LUN|
|Riscontri letture cache per un disco del sistema operativo Premium|Sì|Riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|
|Mancati riscontri letture cache per un disco del sistema operativo Premium|Sì|Mancati riscontri letture cache per un disco del sistema operativo Premium (anteprima)|Percentuale|Media|Mancati riscontri letture cache per un disco del sistema operativo Premium|Nessuna dimensione|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CpuUsage|Sì|Utilizzo di CPU|Conteggio|Media|Uso della CPU in tutti i core in millicore.|containerName|
|MemoryUsage|Sì|Utilizzo della memoria|Byte|Media|Uso della memoria totale in byte.|containerName|
|NetworkBytesReceivedPerSecond|Sì|Byte di rete ricevuti al secondo|Byte|Media|Byte di rete ricevuti al secondo.|Nessuna dimensione|
|NetworkBytesTransmittedPerSecond|Sì|Byte di rete trasmessi al secondo|Byte|Media|Byte di rete trasmessi al secondo.|Nessuna dimensione|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Sì|Tempo CPU AgentPool|Secondi|Totale|Tempo di CPU AgentPool in secondi|Nessuna dimensione|
|RunDuration|Sì|Durata esecuzione|Millisecondi|Totale|Durata esecuzione in millisecondi|Nessuna dimensione|
|SuccessfulPullCount|Sì|Successful Pull Count (Conteggio pull riuscito)|Conteggio|Media|Numero di pull di immagini riuscite|Nessuna dimensione|
|SuccessfulPushCount|Sì|Successful Push Count (Conteggio push riuscito)|Conteggio|Media|Numero di push di immagini riusciti|Nessuna dimensione|
|TotalPullCount|Sì|Total Pull Count (Conteggio pull totale)|Conteggio|Media|Numero totale di pull di immagini|Nessuna dimensione|
|TotalPushCount|Sì|Total Push Count (Conteggio push totale)|Conteggio|Media|Numero totale di push di immagini|Nessuna dimensione|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|No|Numero totale di core CPU disponibili in un cluster gestito|Conteggio|Media|Numero totale di core CPU disponibili in un cluster gestito|Nessuna dimensione|
|kube_node_status_allocatable_memory_bytes|No|Numero totale di memoria disponibile in un cluster gestito|Byte|Media|Numero totale di memoria disponibile in un cluster gestito|Nessuna dimensione|
|kube_node_status_condition|No|Stati per diverse condizioni dei nodi|Conteggio|Media|Stati per diverse condizioni dei nodi|Condition, status, status2, node|
|kube_pod_status_phase|No|Numero di pod per fase|Conteggio|Media|Numero di pod per fase|fase, spazio dei nomi, pod|
|kube_pod_status_ready|No|Numero di pod in stato Pronto|Conteggio|Media|Numero di pod in stato Pronto|spazio dei nomi, pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|FailedRequests|Sì|Richieste non riuscite|Conteggio|Totale|Ottiene i log disponibili per i provider di risorse personalizzati|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Sì|Richieste riuscite|Conteggio|Totale|Richieste riuscite effettuate dal provider personalizzato|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AvailableCapacity|Sì|Capacità disponibile|Byte|Media|Capacità disponibile in byte durante il periodo di Reporting.|Nessuna dimensione|
|BytesUploadedToCloud|Sì|Byte cloud caricati (dispositivo)|Byte|Media|Il numero totale di byte caricati in Azure da un dispositivo durante il periodo di Reporting.|Nessuna dimensione|
|BytesUploadedToCloudPerShare|Sì|Byte cloud caricati (condivisione)|Byte|Media|Il numero totale di byte caricati in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|CloudReadThroughput|Sì|Velocità effettiva download cloud|Byte al secondo|Media|La velocità effettiva di download del cloud in Azure durante il periodo di Reporting.|Nessuna dimensione|
|CloudReadThroughputPerShare|Sì|Velocità effettiva download cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di download in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|CloudUploadThroughput|Sì|Velocità effettiva caricamento cloud|Byte al secondo|Media|La velocità effettiva di caricamento nel cloud in Azure durante il periodo di Reporting.|Nessuna dimensione|
|CloudUploadThroughputPerShare|Sì|Velocità effettiva caricamento cloud (condivisione)|Byte al secondo|Media|Velocità effettiva di caricamento in Azure da una condivisione durante il periodo di Reporting.|Condivisione|
|HyperVMemoryUtilization|Sì|Calcolo Edge - Utilizzo memoria|Percentuale|Media|Quantità di RAM in uso|InstanceName|
|HyperVVirtualProcessorUtilization|Sì|Calcolo Edge - Percentuale CPU|Percentuale|Media|Percentuale utilizzo CPU|InstanceName|
|NICReadThroughput|Sì|Velocità effettiva lettura (rete)|Byte al secondo|Media|Velocità effettiva di lettura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|NICWriteThroughput|Sì|Velocità effettiva scrittura (rete)|Byte al secondo|Media|Velocità effettiva di scrittura dell'interfaccia di rete nel dispositivo nel periodo di Reporting per tutti i volumi del gateway.|InstanceName|
|TotalCapacity|Sì|Capacità totale|Byte|Media|Capacità totale|Nessuna dimensione|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|FailedRuns|Sì|Esecuzioni non riuscite|Conteggio|Totale||PipelineName, ActivityName|
|SuccessfulRuns|Sì|Esecuzioni riuscite|Conteggio|Totale||PipelineName, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Sì|Cancelled activity runs metrics (Metrica esecuzioni attività annullate)|Conteggio|Totale||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Sì|Failed activity runs metrics (Metrica esecuzioni attività non riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Sì|Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)|Conteggio|Totale||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Sì|Total factory size (GB unit) (Dimensione totale factory (unità GB))|Conteggio|Massimo||Nessuna dimensione|
|IntegrationRuntimeAvailableMemory|Sì|Memoria disponibile di Integration Runtime|Byte|Media||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Sì|Numero di nodi disponibili in Integration Runtime|Conteggio|Media||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Sì|Integration runtime queue duration (Durata coda runtime di integrazione)|Secondi|Media||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Sì|Uso della CPU di Integration runtime|Percentuale|Media||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Sì|Integration runtime queue length (Lunghezza coda runtime di integrazione)|Conteggio|Media||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Sì|Maximum allowed factory size (GB unit) (Dimensione massima consentita per la factory (unità GB))|Conteggio|Massimo||Nessuna dimensione|
|MaxAllowedResourceCount|Sì|Maximum allowed entities count (Conteggio massimo consentito per le entità)|Conteggio|Massimo||Nessuna dimensione|
|PipelineCancelledRuns|Sì|Cancelled pipeline runs metrics (Metrica esecuzioni pipeline annullate)|Conteggio|Totale||FailureType, Name|
|PipelineFailedRuns|Sì|Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)|Conteggio|Totale||FailureType, Name|
|PipelineSucceededRuns|Sì|Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)|Conteggio|Totale||FailureType, Name|
|ResourceCount|Sì|Total entities count (Conteggio totale entità)|Conteggio|Massimo||Nessuna dimensione|
|TriggerCancelledRuns|Sì|Cancelled trigger runs metrics (Metrica esecuzioni trigger annullate)|Conteggio|Totale||Name, FailureType|
|TriggerFailedRuns|Sì|Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)|Conteggio|Totale||Name, FailureType|
|TriggerSucceededRuns|Sì|Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)|Conteggio|Totale||Name, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|DataRead|Sì|Dati letti|Byte|Totale|Quantità totale di dati letti dall'account|Nessuna dimensione|
|DataWritten|Sì|Dati scritti|Byte|Totale|Quantità totale di dati scritti nell'account|Nessuna dimensione|
|ReadRequests|Sì|Richieste di lettura|Conteggio|Totale|Numero di richieste di lettura dati all'account|Nessuna dimensione|
|TotalStorage|Sì|Spazio di archiviazione totale|Byte|Massimo|Quantità totale di dati archiviati nell'account|Nessuna dimensione|
|WriteRequests|Sì|Richieste di scrittura|Conteggio|Totale|Numero di richieste di scrittura dati all'account|Nessuna dimensione|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_connections|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|backup_storage_used|Sì|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuna dimensione|
|connections_failed|Sì|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|io_consumption_percent|Sì|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|memory_percent|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|network_bytes_egress|Sì|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Sì|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|seconds_behind_master|Sì|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuna dimensione|
|serverlog_storage_limit|Sì|Limite di archiviazione dei log del server|Byte|Media|Limite di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_percent|Sì|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Sì|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|storage_limit|Sì|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuna dimensione|
|storage_percent|Sì|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Sì|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_connections|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|backup_storage_used|Sì|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuna dimensione|
|connections_failed|Sì|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|io_consumption_percent|Sì|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|memory_percent|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|network_bytes_egress|Sì|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Sì|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|seconds_behind_master|Sì|Intervallo di replica in secondi|Conteggio|Massimo|Intervallo di replica in secondi|Nessuna dimensione|
|serverlog_storage_limit|Sì|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_percent|Sì|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Sì|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|storage_limit|Sì|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuna dimensione|
|storage_percent|Sì|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Sì|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_connections|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|backup_storage_used|Sì|Backup Storage used (Archiviazione di backup usata)|Byte|Media|Backup Storage used (Archiviazione di backup usata)|Nessuna dimensione|
|connections_failed|Sì|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|io_consumption_percent|Sì|Percentuale IO|Percentuale|Media|Percentuale IO|Nessuna dimensione|
|memory_percent|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|network_bytes_egress|Sì|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Sì|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|pg_replica_log_delay_in_bytes|Sì|Max Lag Across Replicas (Ritardo massimo tra repliche)|Byte|Massimo|Ritardo in byte della replica più in ritardo|Nessuna dimensione|
|pg_replica_log_delay_in_seconds|Sì|Replica Lag (Ritardo replica)|Secondi|Massimo|Ritardo replica in secondi|Nessuna dimensione|
|serverlog_storage_limit|Sì|Limite di archiviazione dei log del server|Byte|Massimo|Limite di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_percent|Sì|Percentuale di archiviazione dei log del server|Percentuale|Media|Percentuale di archiviazione dei log del server|Nessuna dimensione|
|serverlog_storage_usage|Sì|Archiviazione dei log del server usata|Byte|Media|Archiviazione dei log del server usata|Nessuna dimensione|
|storage_limit|Sì|Limite archiviazione|Byte|Massimo|Limite archiviazione|Nessuna dimensione|
|storage_percent|Sì|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Sì|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_connections|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|iops|Sì|IOPS|Conteggio|Media|Operazioni di i/o al secondo|Nessuna dimensione|
|memory_percent|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|network_bytes_egress|Sì|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Sì|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|storage_percent|Sì|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Sì|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_connections|Sì|Connessioni attive|Conteggio|Media|Connessioni attive|Nessuna dimensione|
|connections_failed|Sì|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|connections_succeeded|Sì|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|iops|Sì|IOPS|Conteggio|Media|Operazioni di i/o al secondo|Nessuna dimensione|
|maximum_used_transactionIDs|Sì|Numero massimo di ID transazione utilizzati|Conteggio|Media|Numero massimo di ID transazione utilizzati|Nessuna dimensione|
|memory_percent|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Nessuna dimensione|
|network_bytes_egress|Sì|Rete in uscita|Byte|Totale|Output di rete tra connessioni attive|Nessuna dimensione|
|network_bytes_ingress|Sì|Rete in ingresso|Byte|Totale|Input di rete tra connessioni attive|Nessuna dimensione|
|storage_percent|Sì|Percentuale di archiviazione|Percentuale|Media|Percentuale di archiviazione|Nessuna dimensione|
|storage_used|Sì|Uso archiviazione|Byte|Media|Uso archiviazione|Nessuna dimensione|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Sì|C2D messages abandoned (Messaggi C2D abbandonati)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.complete.success|Sì|C2D message deliveries completed (Recapiti messaggi C2D completati)|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|Nessuna dimensione|
|c2d.commands.egress.reject.success|Sì|C2D messages rejected (Messaggi C2D rifiutati)|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|Nessuna dimensione|
|c2d.methods.failure|Sì|Chiamate a metodi diretti non riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|Nessuna dimensione|
|c2d.methods.requestSize|Sì|Dimensioni delle richieste di chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle richieste di chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.responseSize|Sì|Dimensioni delle risposte a chiamate a metodi diretti|Byte|Media|Dimensioni medie, minime e massime delle risposte a chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.methods.success|Sì|Chiamate a metodi diretti riuscite|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|Nessuna dimensione|
|c2d.twin.read.failure|Sì|Letture dei dispositivi gemelli non riuscite per il back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|Nessuna dimensione|
|c2d.twin.read.size|Sì|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.read.success|Sì|Letture dei dispositivi gemelli completate dal back-end|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|Nessuna dimensione|
|c2d.twin.update.failure|Sì|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|Nessuna dimensione|
|c2d.twin.update.size|Sì|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|c2d.twin.update.success|Sì|Aggiornamenti dei dispositivi gemelli completati dal back-end|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|Nessuna dimensione|
|C2DMessagesExpired|Sì|C2D Messages Expired (preview) (Messaggi C2D scaduti (anteprima))|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|Nessuna dimensione|
|configurazioni|Sì|Metriche di configurazione|Conteggio|Totale|Metriche per le operazioni di configurazione|Nessuna dimensione|
|connectedDeviceCount|No|Dispositivi connessi (anteprima)|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|d2c.endpoints.egress.builtIn.events|Sì|Routing: messaggi recapitati a messaggi/eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c.endpoints.egress.eventHubs|Sì|Routing: messaggi inviati all'hub eventi|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusQueues|Sì|Routing: messaggi recapitati alla coda del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.serviceBusTopics|Sì|Routing: messaggi recapitati all'argomento del bus di servizio|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.egress.storage|Sì|Routing: messaggi recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.blobs|Sì|Routing: BLOB recapitati all'archiviazione|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.egress.storage.bytes|Sì|Routing: dati recapitati all'archiviazione|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|Nessuna dimensione|
|d2c.endpoints.latency.builtIn.events|Sì|Routing: latenza dei messaggi per messaggi/eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi).|Nessuna dimensione|
|d2c.endpoints.latency.eventHubs|Sì|Routing: latenza dei messaggi per l'hub eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub IoT e l'ingresso del messaggio in un endpoint dell'hub eventi.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusQueues|Sì|Routing: latenza dei messaggi per la coda del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint della coda del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.serviceBusTopics|Sì|Routing: latenza dei messaggi per l'argomento del bus di servizio|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint dell'argomento del bus di servizio.|Nessuna dimensione|
|d2c.endpoints.latency.storage|Sì|Routing: latenza dei messaggi per l'archiviazione|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso dei messaggi all'hub IoT e l'ingresso dei messaggi di telemetria in un endpoint di archiviazione.|Nessuna dimensione|
|d2c.telemetry.egress.dropped|Sì|Routing: messaggi di telemetria eliminati |Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|Nessuna dimensione|
|d2c.telemetry.egress.fallback|Sì|Routing: messaggi recapitati al fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|Nessuna dimensione|
|d2c.telemetry.egress.invalid|Sì|Routing: messaggi di telemetria incompatibili|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Questo valore non include i nuovi tentativi.|Nessuna dimensione|
|d2c.telemetry.egress.orphaned|Sì|Routing: messaggi di telemetria orfani |Conteggio|Totale|Numero di volte in cui i messaggi sono stati resi orfani dal routing dell'hub IoT perché non corrispondono ad alcuna regola di routing (inclusa la regola di fallback). |Nessuna dimensione|
|d2c.telemetry.egress.success|Sì|Routing: messaggi di telemetria recapitati|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|Nessuna dimensione|
|d2c.telemetry.ingress.allProtocol|Sì|Tentativi di invio di messaggi di telemetria|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|Nessuna dimensione|
|d2c.telemetry.ingress.sendThrottle|Sì|Number of throttling errors (Numero di errori di limitazione)|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|Nessuna dimensione|
|d2c.telemetry.ingress.success|Sì|Messaggi di telemetria inviati|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|Nessuna dimensione|
|d2c.twin.read.failure|Sì|Letture dei dispositivi gemelli non riuscite per i dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|Nessuna dimensione|
|d2c.twin.read.size|Sì|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|Byte|Media|Numero medio, minimo e massimo di letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.read.success|Sì|Letture dei dispositivi gemelli completate dai dispositivi|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|Nessuna dimensione|
|d2c.twin.update.failure|Sì|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|Nessuna dimensione|
|d2c.twin.update.size|Sì|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|Byte|Media|Dimensioni medie, minime e massime degli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|d2c.twin.update.success|Sì|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|Nessuna dimensione|
|dailyMessageQuotaUsed|Sì|Total number of messages used (Numero totale di messaggi usati)|Conteggio|Massimo|Numero totale di messaggi usati nella data odierna|Nessuna dimensione|
|deviceDataUsage|Sì|Total device data usage (Utilizzo totale dati dispositivo)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|deviceDataUsageV2|Sì|Uso totale dei dati del dispositivo (anteprima)|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|Nessuna dimensione|
|devices.connectedDevices.allProtocol|Sì|Dispositivi connessi (deprecati) |Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|Nessuna dimensione|
|devices.totalDevices|Sì|Dispositivi totali (deprecati)|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|EventGridDeliveries|Sì|Recapiti di griglia di eventi (anteprima)|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento ( https://aka.ms/ioteventgrid) .|Risultato, EventType|
|EventGridLatency|Sì|Event Grid latency (preview) (Latenza Griglia di eventi (anteprima))|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|EventType|
|jobs.cancelJob.failure|Sì|Annullamenti di processi non riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|Nessuna dimensione|
|jobs.cancelJob.success|Sì|Annullamenti di processi riusciti|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|Nessuna dimensione|
|jobs.completed|Sì|Processi completati|Conteggio|Totale|Numero di tutti i processi completati.|Nessuna dimensione|
|jobs.createDirectMethodJob.failure|Sì|Creazioni di processi di chiamata al metodo non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|Nessuna dimensione|
|jobs.createDirectMethodJob.success|Sì|Creazioni di processi di chiamata al metodo completate|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|Nessuna dimensione|
|jobs.createTwinUpdateJob.failure|Sì|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|jobs.createTwinUpdateJob.success|Sì|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|Nessuna dimensione|
|jobs.failed|Sì|Processi non riusciti|Conteggio|Totale|Numero di tutti i processi non riusciti.|Nessuna dimensione|
|jobs.listJobs.failure|Sì|Chiamate per elencare i processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|Nessuna dimensione|
|jobs.listJobs.success|Sì|Chiamate per elencare i processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|Nessuna dimensione|
|jobs.queryJobs.failure|Sì|Query sui processi non riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|Nessuna dimensione|
|jobs.queryJobs.success|Sì|Query sui processi riuscite|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|Nessuna dimensione|
|RoutingDataSizeInBytesDelivered|Sì|Dimensioni del messaggio di recapito di routing in byte (anteprima)|Byte|Totale|Dimensioni totali in byte dei messaggi recapitati dall'hub Internet a un endpoint. È possibile utilizzare le dimensioni EndpointName e EndpointType per visualizzare le dimensioni dei messaggi in byte recapitati ai diversi endpoint. Il valore della metrica aumenta per ogni messaggio recapitato, compreso se il messaggio viene recapitato a più endpoint o se il messaggio viene recapitato più volte allo stesso endpoint.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Sì|Recapito di routing (anteprima)|Conteggio|Totale|Il numero di tentativi di recapito di messaggi a tutti gli endpoint tramite l'hub. Per visualizzare il numero di tentativi riusciti o non riusciti, utilizzare la dimensione risultato. Per visualizzare il motivo dell'errore, ad esempio non valido, eliminato o orfano, utilizzare la dimensione FailureReasonCategory. È anche possibile usare le dimensioni EndpointName e EndpointType per comprendere il numero di messaggi recapitati ai diversi endpoint. Il valore della metrica aumenta di uno per ogni tentativo di recapito, incluso se il messaggio viene recapitato a più endpoint o se il messaggio viene recapitato più volte allo stesso endpoint.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Sì|Latenza recapito routing (anteprima)|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e il messaggio di telemetria in ingresso in un endpoint. È possibile usare le dimensioni EndpointName e EndpointType per comprendere la latenza per i diversi endpoint.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Dispositivi totali (anteprima)|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|Nessuna dimensione|
|twinQueries.failure|Sì|Query dei dispositivi gemelli non riuscite|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|Nessuna dimensione|
|twinQueries.resultSize|Sì|Dimensioni dei risultati delle query dei dispositivi gemelli|Byte|Media|Dimensioni medie, minime e massime dei risultati delle query dei dispositivi gemelli.|Nessuna dimensione|
|twinQueries.success|Sì|Query dei dispositivi gemelli completate|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|Nessuna dimensione|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AttestationAttempts|Sì|Tentativi di attestazione|Conteggio|Totale|Numero di tentativi di attestazioni dispositivo|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Sì|Dispositivi assegnati|Conteggio|Totale|Numero di dispositivi assegnati a un hub IoT|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Sì|Tentativi di registrazione|Conteggio|Totale|Numero di tentativi di registrazione dispositivo|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AddRegion|Sì|Area aggiunta|Conteggio|Conteggio|Area aggiunta|Region|
|AutoscaleMaxThroughput|No|Scalabilità automatica max velocità effettiva|Conteggio|Massimo|Scalabilità automatica max velocità effettiva|DatabaseName, CollectionName|
|AvailableStorage|No|deprecato Archiviazione disponibile|Byte|Totale|"Spazio di archiviazione disponibile" verrà rimosso da monitoraggio di Azure alla fine del 2020 settembre. Cosmos DB dimensioni di archiviazione della raccolta sono ora illimitate. L'unica restrizione è che le dimensioni di archiviazione per ogni chiave di partizione logica sono 20 GB. È possibile abilitare PartitionKeyStatistics nel log di diagnostica per verificare il consumo di spazio di archiviazione per le chiavi di partizione principali. Per ulteriori informazioni sulla quota di archiviazione Cosmos DB, consultare il documento https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Dopo la deprecazione, le regole di avviso rimanenti ancora definite nella metrica deprecata verranno disabilitate automaticamente dopo la data di deprecazione.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|No|Chiusure di connessione Cassandra|Conteggio|Totale|Numero di connessioni Cassandra chiuse, segnalate a una granularità di 1 minuto|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Media ReplicationLatency connettore Cassandra|Millisecondi|Media|Media ReplicationLatency connettore Cassandra|Nessuna dimensione|
|CassandraConnectorReplicationHealthStatus|No|Stato di integrità della replica del connettore Cassandra|Conteggio|Conteggio|Stato di integrità della replica del connettore Cassandra|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|No|Spazio di portadi Cassandra creato|Conteggio|Conteggio|Spazio di portadi Cassandra creato|ResourceName |
|CassandraKeyspaceDelete|No|Spazio di portadi Cassandra eliminato|Conteggio|Conteggio|Spazio di portadi Cassandra eliminato|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Velocità effettiva del tasto di Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva del tasto di Cassandra aggiornata|ResourceName |
|CassandraKeyspaceUpdate|No|Spazio del tasto Cassandra aggiornato|Conteggio|Conteggio|Spazio del tasto Cassandra aggiornato|ResourceName |
|CassandraRequestCharges|No|Addebiti richieste Cassandra|Conteggio|Totale|Ur utilizzate per le richieste Cassandra effettuate|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|No|Richieste di Cassandra|Conteggio|Conteggio|Numero di richieste Cassandra effettuate|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Tabella Cassandra creata|Conteggio|Conteggio|Tabella Cassandra creata|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Tabella Cassandra eliminata|Conteggio|Conteggio|Tabella Cassandra eliminata|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Velocità effettiva della tabella Cassandra aggiornata|Conteggio|Conteggio|Velocità effettiva della tabella Cassandra aggiornata|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Tabella Cassandra aggiornata|Conteggio|Conteggio|Tabella Cassandra aggiornata|ResourceName, ChildResourceName, |
|CreateAccount|Sì|Account creato|Conteggio|Conteggio|Account creato|Nessuna dimensione|
|DataUsage|No|Utilizzo dei dati|Byte|Totale|Utilizzo totale dei dati segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DeleteAccount|Sì|Account eliminato|Conteggio|Conteggio|Account eliminato|Nessuna dimensione|
|DocumentCount|No|Conteggio documenti|Conteggio|Totale|Numero totale di documenti segnalato a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|DocumentQuota|No|Quota documenti|Byte|Totale|Quota di archiviazione totale segnalata con granularità di 5 minuti|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|No|Database Gremlin creato|Conteggio|Conteggio|Database Gremlin creato|ResourceName |
|GremlinDatabaseDelete|No|Database Gremlin eliminato|Conteggio|Conteggio|Database Gremlin eliminato|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Velocità effettiva del database Gremlin aggiornata|Conteggio|Conteggio|Velocità effettiva del database Gremlin aggiornata|ResourceName |
|GremlinDatabaseUpdate|No|Database Gremlin aggiornato|Conteggio|Conteggio|Database Gremlin aggiornato|ResourceName |
|GremlinGraphCreate|No|Grafo Gremlin creato|Conteggio|Conteggio|Grafo Gremlin creato|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Grafo Gremlin eliminato|Conteggio|Conteggio|Grafo Gremlin eliminato|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Velocità effettiva del grafo Gremlin aggiornata|Conteggio|Conteggio|Velocità effettiva del grafo Gremlin aggiornata|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Grafico Gremlin aggiornato|Conteggio|Conteggio|Grafico Gremlin aggiornato|ResourceName, ChildResourceName, |
|IndexUsage|No|Utilizzo indice|Byte|Totale|Totale utilizzo di indici restituiti a granularità di 5 minuti|CollectionName, DatabaseName, Region|
|MetadataRequests|No|Metadata Requests (Richieste di metadati)|Conteggio|Conteggio|Conteggio delle richieste di metadati. Cosmos DB gestisce la raccolta dei metadati di sistema per ogni account, consentendo di enumerare le raccolte, i database e così via e le relative configurazioni gratuitamente.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionCreate|No|Raccolta Mongo creata|Conteggio|Conteggio|Raccolta Mongo creata|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Raccolta Mongo eliminata|Conteggio|Conteggio|Raccolta Mongo eliminata|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Velocità effettiva raccolta Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva raccolta Mongo aggiornata|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Raccolta Mongo aggiornata|Conteggio|Conteggio|Raccolta Mongo aggiornata|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Database Mongo eliminato|Conteggio|Conteggio|Database Mongo eliminato|ResourceName |
|MongoDatabaseThroughputUpdate|No|Velocità effettiva del database Mongo aggiornata|Conteggio|Conteggio|Velocità effettiva del database Mongo aggiornata|ResourceName |
|MongoDBDatabaseCreate|No|Database Mongo creato|Conteggio|Conteggio|Database Mongo creato|ResourceName |
|MongoDBDatabaseUpdate|No|Database Mongo aggiornato|Conteggio|Conteggio|Database Mongo aggiornato|ResourceName |
|MongoRequestCharge|Sì|Mongo Request Charge (Addebito richiesta Mongo)|Conteggio|Totale|Unità richiesta Mongo utilizzate|DatabaseName, CollectionName, Region, CommandName, ErrorCode, stato|
|MongoRequests|Sì|Richieste Mongo|Conteggio|Conteggio|Numero di richieste Mongo eseguite|DatabaseName, CollectionName, Region, CommandName, ErrorCode, stato|
|MongoRequestsCount|No|Mongo Request Rate (Frequenza richieste Mongo)|Conteggio al secondo|Media|Numero di richieste Mongo al secondo|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|No|Mongo Delete Request Rate (Frequenza richieste di eliminazione Mongo)|Conteggio al secondo|Media|Richiesta di eliminazione Mongo al secondo|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|No|Mongo Insert Request Rate (Frequenza richieste di inserimento Mongo)|Conteggio al secondo|Media|Numero di inserimento di Mongo al secondo|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|No|Mongo Query Request Rate (Frequenza richieste di query Mongo)|Conteggio al secondo|Media|Richieste di query Mongo al secondo|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|No|Mongo Update Request Rate (Frequenza richieste di aggiornamento Mongo)|Conteggio al secondo|Media|Richiesta di aggiornamento Mongo al secondo|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|No|Consumo ur normalizzato|Percentuale|Massimo|Percentuale di utilizzo massimo delle UR al minuto|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Velocità effettiva sottoposta a provisioning|Conteggio|Massimo|Velocità effettiva sottoposta a provisioning|DatabaseName, CollectionName|
|RegionFailover|Sì|Area sottoposta a failover|Conteggio|Conteggio|Area sottoposta a failover|Nessuna dimensione|
|RemoveRegion|Sì|Area rimossa|Conteggio|Conteggio|Area rimossa|Region|
|ReplicationLatency|Sì|P99 Replication Latency (Latenza di replica P99)|Millisecondi|Media|Latenza di replica P99 tra aree di origine e di destinazione per l'account abilitato per la replica geografica|SourceRegion, TargetRegion|
|ServerSideLatency|No|Latenza lato server|Millisecondi|Media|Latenza lato server|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Disponibilità del servizio|Percentuale|Media|L'account richiede disponibilità a una granularità di un'ora, giorno o mese|Nessuna dimensione|
|SqlContainerCreate|No|Contenitore SQL creato|Conteggio|Conteggio|Contenitore SQL creato|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Contenitore SQL eliminato|Conteggio|Conteggio|Contenitore SQL eliminato|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Velocità effettiva del contenitore SQL aggiornata|Conteggio|Conteggio|Velocità effettiva del contenitore SQL aggiornata|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|Contenitore SQL aggiornato|Conteggio|Conteggio|Contenitore SQL aggiornato|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|Database SQL creato|Conteggio|Conteggio|Database SQL creato|ResourceName |
|SqlDatabaseDelete|No|Database SQL eliminato|Conteggio|Conteggio|Database SQL eliminato|ResourceName |
|SqlDatabaseThroughputUpdate|No|Velocità effettiva del database SQL aggiornata|Conteggio|Conteggio|Velocità effettiva del database SQL aggiornata|ResourceName |
|SqlDatabaseUpdate|No|Database SQL aggiornato|Conteggio|Conteggio|Database SQL aggiornato|ResourceName |
|TableTableCreate|No|Tabella AzureTable creata|Conteggio|Conteggio|Tabella AzureTable creata|ResourceName |
|TableTableDelete|No|Tabella AzureTable eliminata|Conteggio|Conteggio|Tabella AzureTable eliminata|ResourceName |
|TableTableThroughputUpdate|No|Velocità effettiva della tabella AzureTable aggiornata|Conteggio|Conteggio|Velocità effettiva della tabella AzureTable aggiornata|ResourceName |
|TableTableUpdate|No|Tabella AzureTable aggiornata|Conteggio|Conteggio|Tabella AzureTable aggiornata|ResourceName |
|TotalRequests|Sì|Totale richieste|Conteggio|Conteggio|Numero di richieste eseguite|DatabaseName, CollectionName, Region, StatusCode, OperationType, stato|
|TotalRequestUnits|Sì|Total Request Units (Unità richiesta totali)|Conteggio|Totale|Unità richiesta utilizzate|DatabaseName, CollectionName, Region, StatusCode, OperationType, stato|
|UpdateAccountKeys|Sì|Chiavi dell'account aggiornate|Conteggio|Conteggio|Chiavi dell'account aggiornate|KeyType|
|UpdateAccountNetworkSettings|Sì|Impostazioni di rete account aggiornate|Conteggio|Conteggio|Impostazioni di rete account aggiornate|Nessuna dimensione|
|UpdateAccountReplicationSettings|Sì|Impostazioni di replica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di replica dell'account aggiornate|Nessuna dimensione|
|UpdateDiagnosticsSettings|No|Impostazioni di diagnostica dell'account aggiornate|Conteggio|Conteggio|Impostazioni di diagnostica dell'account aggiornate|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Argomento, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|Argomento, ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Argomento|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Error, ErrorType|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|Nessuna dimensione|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|Nessuna dimensione|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|Nessuna dimensione|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sì|Eventi di messaggi non recapitati|Conteggio|Totale|Totale eventi di messaggi non recapitati che corrispondono a questa sottoscrizione di eventi|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventi con recapito non riuscito|Conteggio|Totale|Numero totale di eventi con recapito non riuscito a questa sottoscrizione di eventi|Errore, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sì|Eventi recapitati|Conteggio|Totale|Totali eventi recapitati a questa sottoscrizione di eventi|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Durata di elaborazione della destinazione|Millisecondi|Media|Durata di elaborazione della destinazione in millisecondi|EventSubscriptionName|
|DroppedEventCount|Sì|Eventi eliminati|Conteggio|Totale|Totali eventi eliminati corrispondenti a questa sottoscrizione di eventi|DropReason, EventSubscriptionName|
|MatchedEventCount|Sì|Eventi abbinati|Conteggio|Totale|Numero totale di eventi abbinati a questa sottoscrizione di eventi|EventSubscriptionName|
|PublishFailCount|Sì|Publish Failed Events (Eventi con pubblicazione non riuscita)|Conteggio|Totale|Numero totale di eventi che non è stato possibile pubblicare in questo argomento|ErrorType, Error|
|PublishSuccessCount|Sì|Eventi pubblicati|Conteggio|Totale|Numero totale di eventi pubblicati in questo argomento|Nessuna dimensione|
|PublishSuccessLatencyInMs|Sì|Publish Success Latency (Latenza di pubblicazioni riuscite)|Millisecondi|Totale|Latenza pubblicazione riuscita in millisecondi|Nessuna dimensione|
|UnmatchedEventCount|Sì|Eventi senza corrispondenza|Conteggio|Totale|Totale eventi non corrispondenti ad alcuna sottoscrizione di eventi per questo argomento|Nessuna dimensione|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuna dimensione|
|AvailableMemory|No|Memoria disponibile|Percentuale|Massimo|Memoria disponibile per il cluster di hub eventi come percentuale della memoria totale.|Ruolo|
|CaptureBacklog|No|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|Nessuna dimensione|
|CapturedBytes|No|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|Nessuna dimensione|
|CapturedMessages|No|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|Nessuna dimensione|
|ConnectionsClosed|No|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|Nessuna dimensione|
|ConnectionsOpened|No|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|Nessuna dimensione|
|CPU|No|CPU|Percentuale|Massimo|Uso CPU per il cluster dell'hub eventi in percentuale|Ruolo|
|IncomingBytes|Sì|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|Nessuna dimensione|
|IncomingMessages|Sì|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|Nessuna dimensione|
|IncomingRequests|Sì|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|Nessuna dimensione|
|OutgoingBytes|Sì|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|Nessuna dimensione|
|OutgoingMessages|Sì|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|Nessuna dimensione|
|QuotaExceededErrors|No|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|Nessuna dimensione|
|ServerErrors|No|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|Nessuna dimensione|
|Dimensione|No|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|Ruolo|
|SuccessfulRequests|No|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|Nessuna dimensione|
|ThrottledRequests|No|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|Nessuna dimensione|
|UserErrors|No|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|Nessuna dimensione|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Conteggio|Media|Totale connessioni attive per Microsoft.EventHub.|Nessuna dimensione|
|CaptureBacklog|No|Backlog acquisiti.|Conteggio|Totale|Backlog acquisiti per Microsoft.EventHub.|EntityName|
|CapturedBytes|No|Byte acquisiti.|Byte|Totale|Byte acquisiti per Microsoft.EventHub.|EntityName|
|CapturedMessages|No|Messaggi acquisiti.|Conteggio|Totale|Messaggi acquisiti per Microsoft.EventHub.|EntityName|
|ConnectionsClosed|No|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.EventHub.|EntityName|
|ConnectionsOpened|No|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.EventHub.|EntityName|
|EHABL|Sì|Archiviazione backlog messaggi (deprecato)|Conteggio|Totale|Messaggi di archiviazione dell'hub eventi nel backlog per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHAMBS|Sì|Velocità effettiva archiviazione messaggi (deprecato)|Byte|Totale|Velocità effettiva del messaggio archiviato nell'hub eventi in uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHAMSGS|Sì|Archiviazione messaggi (deprecato)|Conteggio|Totale|Messaggi archiviati dell'hub eventi in uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHINBYTES|Sì|Byte in ingresso (deprecata)|Byte|Totale|Velocità effettiva del messaggio in ingresso dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHINMBS|Sì|Byte in ingresso (obsoleto) (deprecato)|Byte|Totale|Velocità effettiva dei messaggi in ingresso dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in ingresso (deprecata)|Nessuna dimensione|
|EHINMSGS|Sì|Messaggi in ingresso (deprecata)|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHOUTBYTES|Sì|Byte in uscita (deprecata)|Byte|Totale|Velocità effettiva del messaggio in uscita dell'hub eventi per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|EHOUTMBS|Sì|Outgoing bytes (obsolete) (Deprecated) (Byte in uscita (obsoleto) (deprecato))|Byte|Totale|Velocità effettiva dei messaggi in uscita dell'hub eventi per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica byte in uscita (deprecata)|Nessuna dimensione|
|EHOUTMSGS|Sì|Messaggi in uscita (deprecata)|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|FAILREQ|Sì|Richieste non riuscite (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|IncomingBytes|Sì|Byte in ingresso.|Byte|Totale|Byte in ingresso per Microsoft.EventHub.|EntityName|
|IncomingMessages|Sì|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.EventHub.|EntityName|
|IncomingRequests|Sì|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.EventHub.|EntityName|
|INMSGS|Sì|Incoming Messages (obsolete) (Deprecated) (Messaggi in ingresso (obsoleto) (deprecato))|Conteggio|Totale|Totale messaggi in ingresso per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in ingresso (deprecata)|Nessuna dimensione|
|INREQS|Sì|Richieste in ingresso (deprecato)|Conteggio|Totale|Totale richieste di invio in ingresso per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|INTERR|Sì|Errori interni del server (deprecato)|Conteggio|Totale|Totale errori interni del server per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|MISCERR|Sì|Altri errori (deprecato)|Conteggio|Totale|Totale richieste non riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|OutgoingBytes|Sì|Byte in uscita.|Byte|Totale|Byte in uscita per Microsoft.EventHub.|EntityName|
|OutgoingMessages|Sì|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.EventHub.|EntityName|
|OUTMSGS|Sì|Outgoing Messages (obsolete) (Deprecated) (Messaggi in uscita (obsoleto) (deprecato))|Conteggio|Totale|Totale messaggi in uscita per uno spazio dei nomi. Questa metrica è deprecata. Usare invece la metrica messaggi in uscita (deprecata)|Nessuna dimensione|
|QuotaExceededErrors|No|Errori di superamento quota.|Conteggio|Totale|Errori di superamento quota per Microsoft.EventHub.|EntityName, |
|ServerErrors|No|Errori server.|Conteggio|Totale|Errori del server per Microsoft.EventHub.|EntityName, |
|Dimensione|No|Dimensione|Byte|Media|Dimensioni di un hub eventi in byte.|EntityName|
|SuccessfulRequests|No|Richieste riuscite|Conteggio|Totale|Richieste riuscite per Microsoft.EventHub.|EntityName, |
|SUCCREQ|Sì|Richieste completate correttamente (deprecato)|Conteggio|Totale|Totale richieste riuscite per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|SVRBSY|Sì|Errori server occupato (deprecato)|Conteggio|Totale|Totale errori server occupati per uno spazio dei nomi (deprecato)|Nessuna dimensione|
|ThrottledRequests|No|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.EventHub.|EntityName, |
|UserErrors|No|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.EventHub.|EntityName, |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Sì|Richieste del gateway per categoria|Conteggio|Totale|Numero di richieste del gateway per categoria (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Sì|Richieste del gateway|Conteggio|Totale|Numero di richieste del gateway|HttpStatus|
|NumActiveWorkers|Sì|Number of Active Workers (Numero di ruoli di lavoro)|Conteggio|Massimo|Number of Active Workers (Numero di ruoli di lavoro)|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|MetricThreshold|Sì|Soglia della metrica|Conteggio|Media|Soglia di ridimensionamento automatico configurata al momento dell'esecuzione dell'operazione.|MetricTriggerRule|
|ObservedCapacity|Sì|Capacità osservata|Conteggio|Media|Capacità segnalata al ridimensionamento automatico al momento dell'esecuzione dell'operazione.|Nessuna dimensione|
|ObservedMetricValue|Sì|Valore della metrica osservato|Conteggio|Media|Valore calcolato dal ridimensionamento automatico al momento dell'esecuzione dell'operazione|MetricTriggerSource|
|ScaleActionsInitiated|Sì|Azioni di ridimensionamento avviate|Conteggio|Totale|Direzione dell'operazione di ridimensionamento.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Sì|Disponibilità|Percentuale|Media|Percentuale di test di disponibilità completati correttamente|availabilityResult/Name, availabilityResult/location|
|availabilityResults/count|No|Test della disponibilità|Conteggio|Conteggio|Conteggio dei test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Sì|Durata del test di disponibilità|Millisecondi|Media|Durata del test di disponibilità|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Sì|Tempo di connessione alla rete per il caricamento della pagina|Millisecondi|Media|Tempo tra la richiesta utente e la connessione di rete. Include la connessione di trasporto e di ricerca DNS.|Nessuna dimensione|
|browserTimings/processingDuration|Sì|Tempo di elaborazione client|Millisecondi|Media|Tempo compreso tra la ricezione dell'ultimo byte di un documento e il caricamento del DOM. Le richieste asincrone potrebbero essere ancora in fase di elaborazione.|Nessuna dimensione|
|browserTimings/receiveDuration|Sì|Tempo per la ricezione della risposta|Millisecondi|Media|Tempo compreso tra il primo e l'ultimo byte o fino alla disconnessione.|Nessuna dimensione|
|browserTimings/sendDuration|Sì|Tempo per l'invio della richiesta|Millisecondi|Media|Tempo compreso tra la connessione di rete e la ricezione del primo byte.|Nessuna dimensione|
|browserTimings/totalDuration|Sì|Tempo di caricamento della pagina del browser|Millisecondi|Media|Tempo compreso tra la richiesta utente e il caricamento di DOM, fogli di stile, script e immagini.|Nessuna dimensione|
|dependencies/count|No|Chiamate di dipendenza|Conteggio|Conteggio|Numero delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Sì|Durata della dipendenza|Millisecondi|Media|Durata delle chiamate eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/esito positivo, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|dependencies/failed|No|Errori di chiamate di dipendenza|Conteggio|Conteggio|Numero di chiamate di dipendenza non riuscite eseguite dall'applicazione alle risorse esterne.|dipendenza/tipo, dipendenza/performanceBucket, dipendenza/destinazione, dipendenza/resultCode, operazione/sintetica, cloud/roleInstance, cloud/roleName|
|exceptions/browser|No|Eccezioni del browser|Conteggio|Conteggio|Conteggio delle eccezioni non rilevate generate nel browser.|Cloud/ruolo|
|exceptions/count|Sì|Eccezioni|Conteggio|Conteggio|Numero combinato di tutte le eccezioni non rilevate.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|No|Eccezioni del server|Conteggio|Conteggio|Numero delle eccezioni non rilevate generate nell'applicazione server.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Sì|Visualizzazioni pagina|Conteggio|Conteggio|Numero delle visualizzazioni di pagina.|operazione/sintetica, cloud/roleName|
|pageViews/duration|Sì|Tempo di caricamento della visualizzazione pagina|Millisecondi|Media|Tempo di caricamento della visualizzazione pagina|operazione/sintetica, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Sì|Frequenza di eccezioni|Conteggio al secondo|Media|Conteggio delle eccezioni gestite e non gestite restituite in Windows, incluse le eccezioni .NET e non gestite che vengono convertite in eccezioni .NET.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Sì|Memoria disponibile|Byte|Media|Memoria fisica immediatamente disponibile per l'allocazione a un processo o usabile dal sistema.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Sì|CPU processo|Percentuale|Media|Percentuale di tempo trascorso per l'uso del processore da parte di tutti i thread di processo per eseguire le istruzioni. Il valore può variare tra 0 e 100. Questa metrica indica le prestazioni del solo processo w3wp.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Sì|Velocità di I/O di elaborazione|Byte al secondo|Media|Numero totale di byte al secondo letti e scritti in file, rete e dispositivi.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Sì|Tempo processore|Percentuale|Media|Percentuale di tempo che il processore dedica a thread non inattivi.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Sì|Byte privati processo|Byte|Media|Memoria assegnata in modo esclusivo ai processi dell'applicazione monitorata.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Sì|Tempo di esecuzione della richiesta HTTP|Millisecondi|Media|Tempo di esecuzione della richiesta più recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Sì|Richieste HTTP nella coda dell'applicazione|Conteggio|Media|Lunghezza della coda di richieste dell'applicazione.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Sì|Frequenza delle richieste HTTP|Conteggio al secondo|Media|Percentuale di tutte le richieste effettuate all'applicazione da ASP.NET in un secondo.|cloud/roleInstance|
|requests/count|No|Richieste server|Conteggio|Conteggio|Numero delle richieste HTTP completate.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|Sì|Tempo di risposta del server|Millisecondi|Media|Tempo compreso tra la ricezione di una richiesta HTTP e il completamento dell'invio della risposta.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|No|Richieste non riuscite|Conteggio|Conteggio|Numero delle richieste HTTP contrassegnate come non riuscite. Nella maggior parte dei casi si tratta di richieste con un codice di risposta maggiore o uguale a 400 e diverso da 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|No|Frequenza richieste server|Conteggio al secondo|Media|Frequenza delle richieste server al secondo|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Sì|Traces|Conteggio|Conteggio|Numero documenti di analisi|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Sì|Letture della proprietà del dispositivo non riuscite da IoT Central|Conteggio|Totale|Numero di tutte le letture di proprietà non riuscite avviate da IoT Central|Nessuna dimensione|
|C2D. Property. Read. Success|Sì|Letture della proprietà del dispositivo riuscite da IoT Central|Conteggio|Totale|Numero di tutte le letture di proprietà riuscite avviate da IoT Central|Nessuna dimensione|
|C2D. Property. Update. Failure|Sì|Aggiornamenti della proprietà del dispositivo non riusciti da IoT Central|Conteggio|Totale|Numero di tutti gli aggiornamenti di proprietà non riuscite avviati dalla IoT Central|Nessuna dimensione|
|C2D. Property. Update. Success|Sì|Aggiornamenti della proprietà del dispositivo riusciti da IoT Central|Conteggio|Totale|Numero di tutti gli aggiornamenti di proprietà riuscite avviati dal IoT Central|Nessuna dimensione|
|connectedDeviceCount|No|Totale dispositivi connessi|Conteggio|Media|Numero di dispositivi connessi a IoT Central|Nessuna dimensione|
|D2C. Property. Read. Failure|Sì|Letture della proprietà del dispositivo non riuscite dai dispositivi|Conteggio|Totale|Numero di tutte le letture di proprietà non riuscite avviate dai dispositivi|Nessuna dimensione|
|D2C. Property. Read. Success|Sì|Letture della proprietà del dispositivo riuscite dai dispositivi|Conteggio|Totale|Numero di tutte le letture di proprietà riuscite avviate dai dispositivi|Nessuna dimensione|
|D2C. Property. Update. Failure|Sì|Aggiornamenti della proprietà del dispositivo non riusciti dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti delle proprietà non riuscite avviati dai dispositivi|Nessuna dimensione|
|D2C. Property. Update. Success|Sì|Aggiornamenti della proprietà del dispositivo riusciti dai dispositivi|Conteggio|Totale|Numero di tutti gli aggiornamenti della proprietà riusciti avviati dai dispositivi|Nessuna dimensione|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità complessiva dell'insieme di credenziali|Percentuale|Media|Disponibilità richieste di archiviazione|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Saturazione complessiva dell'insieme di credenziali|Percentuale|Media|Capacità dell'insieme di credenziali utilizzata|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Sì|Totale di accessi all'API del servizio|Conteggio|Conteggio|Numero totale di accessi all'API del servizio|ActivityType, ActivityName|
|ServiceApiLatency|Sì|Latenza complessiva dell'API del servizio|Millisecondi|Media|Latenza complessiva delle richieste all'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Sì|Totale dei risultati dell'API del servizio|Conteggio|Conteggio|Numero totale di risultati dell'API del servizio|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BatchBlobCount|Sì|Conteggio BLOB batch|Conteggio|Media|Numero di origini dati in un batch aggregato per l'inserimento.|Database|
|BatchDuration|Sì|Durata batch|Secondi|Media|Durata della fase di aggregazione nel flusso di inserimento.|Database|
|BatchesProcessed|Sì|Batch elaborati|Conteggio|Media|Numero di batch aggregati per l'inserimento. Motivo completamento batch: indica se il batch ha raggiunto il limite di tempo di batch, le dimensioni dei dati o il numero di file impostati dal criterio di invio in batch|Database, SealReason|
|BatchSize|Sì|Dimensioni batch|Byte|Media|Dimensioni dei dati previste non compresse in un batch aggregato per l'inserimento.|Database|
|CacheUtilization|Sì|Utilizzo cache|Percentuale|Media|Livello di uso nell'ambito del cluster|Nessuna dimensione|
|ContinuousExportMaxLatenessMinutes|Sì|Latenza massima di esportazione continua|Conteggio|Massimo|Ritardo (in minuti) segnalato dai processi di esportazione continua nel cluster|Nessuna dimensione|
|ContinuousExportNumOfRecordsExported|Sì|Esportazione continua: numero di record esportati|Conteggio|Totale|Numero di record esportati, generati per ogni artefatto di archiviazione scritto durante l'operazione di esportazione|ContinuousExportName, database|
|ContinuousExportPendingCount|Sì|Continuous Export Pending Count (Esportazione continua - Conteggio in sospeso)|Conteggio|Massimo|Numero di processi di esportazione continua in sospeso pronti per l'esecuzione|Nessuna dimensione|
|ContinuousExportResult|Sì|Continuous Export Result (Risultato esportazione continua)|Conteggio|Conteggio|Indica se l'esportazione continua ha avuto esito positivo o negativo|ContinuousExportName, result, database|
|CPU|Sì|CPU|Percentuale|Media|Livello di uso della CPU|Nessuna dimensione|
|EventsProcessedForEventHubs|Sì|Events processed (for Event/IoT Hubs) (Eventi elaborati (per hub eventi/IoT))|Conteggio|Totale|Numero di eventi elaborati dal cluster durante l'inserimento dall'hub eventi/tutto|EventStatus|
|ExportUtilization|Sì|Export Utilization (Utilizzo esportazione)|Percentuale|Massimo|Esporta utilizzo|Nessuna dimensione|
|IngestionLatencyInSeconds|Sì|Latenza inserimento (in secondi)|Secondi|Media|Tempo di inserimento dall'origine (ad esempio messaggio in EventHub) nel cluster in secondi|Nessuna dimensione|
|IngestionResult|Sì|Risultati inserimento|Conteggio|Conteggio|Numero di operazioni di inserimento|IngestionResultDetails|
|IngestionUtilization|Sì|Utilizzo inserimento|Percentuale|Media|Percentuale di slot di inserimento usati nel cluster|Nessuna dimensione|
|IngestionVolumeInMB|Sì|Ingestion volume (in MB) (Volume inserimento (in MB))|Conteggio|Totale|Volume complessivo dei dati inseriti nel cluster (in MB)|Nessuna dimensione|
|InstanceCount|Sì|Conteggio istanze|Conteggio|Media|Numero totale di istanze|Nessuna dimensione|
|KeepAlive|Sì|Keep alive|Conteggio|Media|Test di integrità che indica che il cluster risponde alle query|Nessuna dimensione|
|QueryDuration|Sì|Durata delle query|Millisecondi|Media|Durata delle query in secondi|QueryStatus|
|SteamingIngestRequestRate|Sì|Streaming Ingest Request Rate (Frequenza richieste inserimento streaming)|Conteggio|RateRequestsPerSecond|Frequenza delle richieste di inserimento in streaming (richieste al secondo)|Nessuna dimensione|
|StreamingIngestDataRate|Sì|Streaming Ingest Data Rate (Velocità dati inserimento streaming)|Conteggio|Media|Velocità dati di inserimento dei flussi (MB al secondo)|Nessuna dimensione|
|StreamingIngestDuration|Sì|Streaming Ingest Duration (Durata inserimento streaming)|Millisecondi|Media|Durata di inserimento del flusso in millisecondi|Nessuna dimensione|
|StreamingIngestResults|Sì|Streaming Ingest Result (Risultato inserimento streaming)|Conteggio|Media|Risultato inserimento flusso|Risultato|
|TotalNumberOfConcurrentQueries|Sì|Numero totale di query simultanee|Conteggio|Totale|Numero totale di query simultanee|Nessuna dimensione|
|TotalNumberOfExtents|Sì|Numero totale di extent|Conteggio|Totale|Numero totale di extent dei dati|Nessuna dimensione|
|TotalNumberOfThrottledCommands|Sì|Numero totale di comandi limitati|Conteggio|Totale|Numero totale di comandi limitati|CommandType|
|TotalNumberOfThrottledQueries|Sì|Numero totale di query limitate|Conteggio|Totale|Numero totale di query limitate|Nessuna dimensione|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActionLatency|Sì|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsCompleted|Sì|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsFailed|Sì|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Sì|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionsStarted|Sì|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsSucceeded|Sì|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionSuccessLatency|Sì|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Sì|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Sì|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Sì|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del connettore per l'ambiente del servizio di integrazione.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Sì|Utilizzo della memoria del flusso di lavoro per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo della memoria del flusso di lavoro per l'ambiente di Integration Services.|Nessuna dimensione|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Sì|Utilizzo del processore del flusso di lavoro per l'ambiente del servizio di integrazione|Percentuale|Media|Utilizzo del processore del flusso di lavoro per l'ambiente di Integration Services.|Nessuna dimensione|
|RunFailurePercentage|Sì|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunLatency|Sì|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsCancelled|Sì|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuna dimensione|
|RunsCompleted|Sì|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsFailed|Sì|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsStarted|Sì|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsSucceeded|Sì|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunStartThrottledEvents|Sì|Eventi di avvio esecuzione limitati|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuna dimensione|
|RunSuccessLatency|Sì|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Sì|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuna dimensione|
|TriggerFireLatency|Sì|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerLatency|Sì|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersCompleted|Sì|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersFailed|Sì|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersFired|Sì|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggersSkipped|Sì|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersStarted|Sì|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersSucceeded|Sì|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerSuccessLatency|Sì|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Sì|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuna dimensione|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActionLatency|Sì|Latenza azioni |Secondi|Media|La latenza delle azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsCompleted|Sì|Azioni completate |Conteggio|Totale|Il numero di azioni del flusso di lavoro completate.|Nessuna dimensione|
|ActionsFailed|Sì|Azioni non riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|ActionsSkipped|Sì|Azioni ignorate |Conteggio|Totale|Il numero di azioni del flusso di lavoro ignorate.|Nessuna dimensione|
|ActionsStarted|Sì|Azioni avviate |Conteggio|Totale|Il numero di azioni del flusso di lavoro avviate.|Nessuna dimensione|
|ActionsSucceeded|Sì|Azioni riuscite |Conteggio|Totale|Il numero di azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionSuccessLatency|Sì|Latenza azioni riuscite |Secondi|Media|La latenza delle azioni del flusso di lavoro riuscite.|Nessuna dimensione|
|ActionThrottledEvents|Sì|Eventi limitati azione|Conteggio|Totale|Il numero di eventi limitati di azione del flusso di lavoro.|Nessuna dimensione|
|BillableActionExecutions|Sì|Esecuzioni di azioni fatturabili|Conteggio|Totale|Numero di esecuzioni di azioni del flusso di lavoro fatturate.|Nessuna dimensione|
|BillableTriggerExecutions|Sì|Esecuzioni di trigger fatturabili|Conteggio|Totale|Numero di esecuzioni di trigger del flusso di lavoro fatturate.|Nessuna dimensione|
|BillingUsageNativeOperation|Sì|Uso fatturazione per esecuzioni di operazioni native|Conteggio|Totale|Numero di esecuzioni di operazioni native fatturate.|Nessuna dimensione|
|BillingUsageStandardConnector|Sì|Uso fatturazione per esecuzioni del connettore standard|Conteggio|Totale|Numero di esecuzioni del connettore standard fatturate.|Nessuna dimensione|
|BillingUsageStorageConsumption|Sì|Uso fatturazione per esecuzioni con uso dell'archiviazione|Conteggio|Totale|Numero di esecuzioni con uso dell'archiviazione fatturate.|Nessuna dimensione|
|RunFailurePercentage|Sì|Percentuale di errori di esecuzione|Percentuale|Totale|Percentuale di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunLatency|Sì|Latenza esecuzioni|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsCancelled|Sì|Esecuzioni annullate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro annullate.|Nessuna dimensione|
|RunsCompleted|Sì|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro completate.|Nessuna dimensione|
|RunsFailed|Sì|Esecuzioni non riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro non riuscite.|Nessuna dimensione|
|RunsStarted|Sì|Esecuzioni avviate|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro avviate.|Nessuna dimensione|
|RunsSucceeded|Sì|Esecuzioni riuscite|Conteggio|Totale|Il numero di esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunStartThrottledEvents|Sì|Eventi di avvio esecuzione limitati|Conteggio|Totale|Numero di eventi di avvio dell'esecuzione del flusso di lavoro limitati.|Nessuna dimensione|
|RunSuccessLatency|Sì|Latenza esecuzioni riuscite|Secondi|Media|La latenza delle esecuzioni del flusso di lavoro riuscite.|Nessuna dimensione|
|RunThrottledEvents|Sì|Eventi limitati esecuzione|Conteggio|Totale|Il numero di eventi limitati di trigger o azione del flusso di lavoro.|Nessuna dimensione|
|TotalBillableExecutions|Sì|Esecuzioni fatturabili totali|Conteggio|Totale|Numero di esecuzioni di flussi di lavoro fatturate.|Nessuna dimensione|
|TriggerFireLatency|Sì|Latenza trigger rimossi |Secondi|Media|La latenza dei trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggerLatency|Sì|Latenza trigger |Secondi|Media|La latenza dei trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersCompleted|Sì|Trigger completati |Conteggio|Totale|Il numero di trigger del flusso di lavoro completati.|Nessuna dimensione|
|TriggersFailed|Sì|Trigger non riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro non riusciti.|Nessuna dimensione|
|TriggersFired|Sì|Trigger rimossi |Conteggio|Totale|Il numero di trigger del flusso di lavoro rimossi.|Nessuna dimensione|
|TriggersSkipped|Sì|Trigger ignorati|Conteggio|Totale|Il numero di trigger del flusso di lavoro ignorati.|Nessuna dimensione|
|TriggersStarted|Sì|Trigger avviati |Conteggio|Totale|Il numero di trigger del flusso di lavoro avviati.|Nessuna dimensione|
|TriggersSucceeded|Sì|Trigger riusciti |Conteggio|Totale|Il numero di trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerSuccessLatency|Sì|Latenza trigger riusciti |Secondi|Media|La latenza dei trigger del flusso di lavoro riusciti.|Nessuna dimensione|
|TriggerThrottledEvents|Sì|Eventi limitati trigger|Conteggio|Totale|Il numero di eventi limitati di trigger del flusso di lavoro.|Nessuna dimensione|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Active Cores (Core attivi)|Sì|Active Cores (Core attivi)|Conteggio|Media|Numero di core attivi|Scenario, clustername|
|Active Nodes (Nodi attivi)|Sì|Active Nodes (Nodi attivi)|Conteggio|Media|Numero di nodi Active. Questi sono i nodi che eseguono attivamente un processo.|Scenario, clustername|
|Annulla esecuzioni richieste|Sì|Annulla esecuzioni richieste|Conteggio|Totale|Numero di esecuzioni in cui è stato richiesto l'annullamento per questa area di lavoro. Il conteggio viene aggiornato quando è stata ricevuta una richiesta di annullamento per un'esecuzione.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni annullate|Sì|Esecuzioni annullate|Conteggio|Totale|Numero di esecuzioni annullate per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione viene annullata correttamente.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni completate|Sì|Esecuzioni completate|Conteggio|Totale|Il numero di esecuzioni è stato completato correttamente per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è stata completata e l'output è stato raccolto.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|CpuUtilization|Sì|CpuUtilization|Conteggio|Media|Percentuale di utilizzo della memoria in un nodo della CPU. L'utilizzo viene segnalato a intervalli di un minuto.|Scenario, runId, NodeId, clustername|
|Errors|Sì|Errors|Conteggio|Totale|Numero di errori di esecuzione in questa area di lavoro. Il conteggio viene aggiornato ogni volta che viene rilevato un errore durante l'esecuzione.|Scenario|
|Esecuzioni non riuscite|Sì|Esecuzioni non riuscite|Conteggio|Totale|Numero di esecuzioni non riuscite per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione ha esito negativo.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Finalizzazione delle esecuzioni|Sì|Finalizzazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni immesse per lo stato di completamento per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è stata completata, ma la raccolta di output è ancora in corso.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|GpuUtilization|Sì|GpuUtilization|Conteggio|Media|Percentuale di utilizzo della memoria in un nodo GPU. L'utilizzo viene segnalato a intervalli di un minuto.|Scenario, runId, NodeId, DeviceId, clustername|
|Idle Cores (Core inattivi)|Sì|Idle Cores (Core inattivi)|Conteggio|Media|Numero di core inattivi|Scenario, clustername|
|Idle Nodes (Nodi inattivi)|Sì|Idle Nodes (Nodi inattivi)|Conteggio|Media|Numero di nodi inattivi. I nodi inattivi sono i nodi che non eseguono alcun processo, ma possono accettare un nuovo processo, se disponibile.|Scenario, clustername|
|Leaving Cores (Core di uscita)|Sì|Leaving Cores (Core di uscita)|Conteggio|Media|Numero di core in uscita|Scenario, clustername|
|Leaving Nodes (Nodi di uscita)|Sì|Leaving Nodes (Nodi di uscita)|Conteggio|Media|Numero di nodi in uscita. Lasciando i nodi sono i nodi che hanno appena terminato l'elaborazione di un processo e passano allo stato inattivo.|Scenario, clustername|
|Model Deploy Failed (Distribuzione di modelli non riuscita)|Sì|Model Deploy Failed (Distribuzione di modelli non riuscita)|Conteggio|Totale|Numero di distribuzioni di modelli non riuscite in questa area di lavoro|Scenario, StatusCode|
|Model Deploy Started (Distribuzione di modelli avviata)|Sì|Model Deploy Started (Distribuzione di modelli avviata)|Conteggio|Totale|Numero di distribuzioni di modelli avviate in questa area di lavoro|Scenario|
|Model Deploy Succeeded (Distribuzione di modelli riuscita)|Sì|Model Deploy Succeeded (Distribuzione di modelli riuscita)|Conteggio|Totale|Numero di distribuzioni di modelli riuscite in questa area di lavoro|Scenario|
|Model Register Failed (Registrazione di modelli non riuscita)|Sì|Model Register Failed (Registrazione di modelli non riuscita)|Conteggio|Totale|Numero di registrazioni del modello non riuscite in questa area di lavoro|Scenario, StatusCode|
|Model Register Succeeded (Registrazione di modelli riuscita)|Sì|Model Register Succeeded (Registrazione di modelli riuscita)|Conteggio|Totale|Numero di registrazioni del modello riuscite in questa area di lavoro|Scenario|
|Esecuzioni non rispondenti|Sì|Esecuzioni non rispondenti|Conteggio|Totale|Numero di esecuzioni non rispondenti per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione entra in uno stato che non risponde.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni non avviate|Sì|Esecuzioni non avviate|Conteggio|Totale|Numero di esecuzioni in stato non avviato per questa area di lavoro. Il conteggio viene aggiornato quando viene ricevuta una richiesta per creare un'esecuzione, ma le informazioni di esecuzione non sono state ancora popolate. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Preempted Cores (Core superati)|Sì|Preempted Cores (Core superati)|Conteggio|Media|Numero di core con precedenza|Scenario, clustername|
|Preempted Nodes (Nodi superati)|Sì|Preempted Nodes (Nodi superati)|Conteggio|Media|Numero di nodi con precedenza. Questi nodi sono i nodi con priorità bassa che vengono sottratti dal pool di nodi disponibile.|Scenario, clustername|
|Preparazione delle esecuzioni|Sì|Preparazione delle esecuzioni|Conteggio|Totale|Numero di esecuzioni preparate per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione entra nello stato di preparazione mentre è in corso la preparazione dell'ambiente di esecuzione.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni del provisioning|Sì|Esecuzioni del provisioning|Conteggio|Totale|Numero di esecuzioni di cui viene eseguito il provisioning per questa area di lavoro. Il conteggio viene aggiornato quando un'esecuzione è in attesa della creazione o del provisioning di una destinazione di calcolo.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Esecuzioni in coda|Sì|Esecuzioni in coda|Conteggio|Totale|Numero di esecuzioni accodate per l'area di lavoro. Il conteggio viene aggiornato quando un'esecuzione viene accodata nella destinazione di calcolo. Può verificarsi durante l'attesa che i nodi di calcolo necessari siano pronti.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Quota Utilization Percentage (Percentuale di utilizzo quota)|Sì|Quota Utilization Percentage (Percentuale di utilizzo quota)|Conteggio|Media|Percentuale di quota utilizzata|Scenario, clustername, VmFamilyName, VmPriority|
|Esecuzioni avviate|Sì|Esecuzioni avviate|Conteggio|Totale|Numero di esecuzioni in esecuzione per l'area di lavoro. Il conteggio viene aggiornato quando viene avviata l'esecuzione delle risorse necessarie.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Avvio delle esecuzioni|Sì|Avvio delle esecuzioni|Conteggio|Totale|Numero di esecuzioni avviate per l'area di lavoro. Il conteggio viene aggiornato dopo la richiesta di creazione delle informazioni di esecuzione ed esecuzione, ad esempio l'ID esecuzione, è stato popolato|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Core totali|Sì|Core totali|Conteggio|Media|Numero di core totali|Scenario, clustername|
|Totale nodi|Sì|Totale nodi|Conteggio|Media|Numero totale di nodi. Il totale include alcuni nodi attivi, nodi inattivi, nodi inutilizzabili, nodi Premepted, lasciando nodi|Scenario, clustername|
|Unusable Cores (Core non utilizzabili)|Sì|Unusable Cores (Core non utilizzabili)|Conteggio|Media|Numero di core inutilizzabili|Scenario, clustername|
|Unusable Nodes (Nodi non utilizzabili)|Sì|Unusable Nodes (Nodi non utilizzabili)|Conteggio|Media|Numero di nodi inutilizzabili. I nodi inutilizzabili non sono funzionali a causa di un problema irrisolvibile. Azure ricicla questi nodi.|Scenario, clustername|
|Avvisi|Sì|Avvisi|Conteggio|Totale|Numero di avvisi di esecuzione in questa area di lavoro. Il conteggio viene aggiornato ogni volta che un'esecuzione rileva un avviso.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Disponibilità delle API|ApiCategory, ApiName|
|Utilizzo|No|Uso|Conteggio|Conteggio|Numero di chiamate API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AssetCount|Sì|Asset count (Conteggio asset)|Conteggio|Media|Numero di asset già creati nell'account del servizio multimediale corrente|Nessuna dimensione|
|AssetQuota|Sì|Asset quota (Quota asset)|Conteggio|Media|Numero di asset consentiti per l'account del servizio multimediale corrente|Nessuna dimensione|
|AssetQuotaUsedPercentage|Sì|Asset quota used percentage (Percentuale usata quota asset)|Percentuale|Media|Percentuale di risorse usate nell'account del servizio multimediale corrente|Nessuna dimensione|
|ContentKeyPolicyCount|Sì|Content Key Policy count (Conteggio criteri chiave simmetrica)|Conteggio|Media|Quanti criteri chiave simmetrica sono già stati creati nell'account del servizio multimediale corrente|Nessuna dimensione|
|ContentKeyPolicyQuota|Sì|Content Key Policy quota (Quota criteri chiave simmetrica)|Conteggio|Media|Numero di criteri di chiave simmetrica consentiti per l'account del servizio multimediale corrente|Nessuna dimensione|
|ContentKeyPolicyQuotaUsedPercentage|Sì|Content Key Policy quota used percentage (Percentuale usata quota criteri chiave simmetrica)|Percentuale|Media|Percentuale di utilizzo del criterio della chiave simmetrica nell'account del servizio multimediale corrente|Nessuna dimensione|
|StreamingPolicyCount|Sì|Streaming Policy count (Conteggio criteri di streaming)|Conteggio|Media|Quanti criteri di streaming sono già stati creati nell'account del servizio multimediale corrente|Nessuna dimensione|
|StreamingPolicyQuota|Sì|Streaming Policy quota (Quota criteri di streaming)|Conteggio|Media|Quanti criteri di streaming sono consentiti per l'account del servizio multimediale corrente|Nessuna dimensione|
|StreamingPolicyQuotaUsedPercentage|Sì|Streaming Policy quota used percentage (Percentuale usata quota criteri di streaming)|Percentuale|Media|Percentuale di utilizzo dei criteri di flusso nell'account del servizio multimediale corrente|Nessuna dimensione|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita, in byte.|OutputFormat|
|Requests|Sì|Requests|Conteggio|Totale|Richieste a un endpoint di streaming.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Sì|Success end to end Latency (Latenza end-to-end riuscita)|Millisecondi|Media|Latenza media per le richieste con esito positivo in millisecondi.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Sì|Dimensioni allocate pool|Byte|Media|Dimensioni del pool di cui è stato effettuato il provisioning|Nessuna dimensione|
|VolumePoolAllocatedUsed|Sì|Pool allocato alle dimensioni del volume|Byte|Media|Dimensioni usate del pool|Nessuna dimensione|
|VolumePoolTotalLogicalSize|Sì|Dimensioni utilizzate pool|Byte|Media|Somma delle dimensioni logiche di tutti i volumi che appartengono al pool|Nessuna dimensione|
|VolumePoolTotalSnapshotSize|Sì|Dimensioni totali dello snapshot per il pool|Byte|Media|Somma delle dimensioni dello snapshot di tutti i volumi in questo pool|Nessuna dimensione|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AverageReadLatency|Sì|Latenza media in lettura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di lettura|Nessuna dimensione|
|AverageWriteLatency|Sì|Latenza media in scrittura|Millisecondi|Media|Latenza media in millisecondi per ogni operazione di scrittura|Nessuna dimensione|
|CbsVolumeBackupActive|Sì|Stato attivo backup del volume|Conteggio|Media|Backup attualmente sospeso per il volume.|Nessuna dimensione|
|CbsVolumeLogicalBackupBytes|Sì|Byte logici sottoposti a backup|Byte|Media|Toatl i byte non compressi/non crittografati sottoposti a backup per questo volume.|Nessuna dimensione|
|CbsVolumeOperationComplete|Sì|Stato dell'operazione|Conteggio|Media|L'ultima operazione di backup/ripristino è stata completata.|Nessuna dimensione|
|CbsVolumeOperationTransferredBytes|Sì|Byte trasferiti per l'operazione|Byte|Media|Totale byte trasferiti per l'ultima operazione di backup/ripristino.|Nessuna dimensione|
|CbsVolumeProtected|Sì|Stato protetto dal volume|Conteggio|Media|Volume protetto dal servizio di backup cloud.|Nessuna dimensione|
|ReadIops|Sì|Operazioni di I/O in lettura|Conteggio al secondo|Media|Operazioni di I/O in lettura al secondo|Nessuna dimensione|
|VolumeAllocatedSize|Sì|Dimensioni allocate del volume|Byte|Media|Dimensioni del volume di cui è stato effettuato il provisioning|Nessuna dimensione|
|VolumeLogicalSize|Sì|Dimensioni utilizzate del volume|Byte|Media|Dimensioni logiche del volume (byte usati)|Nessuna dimensione|
|VolumeSnapshotSize|Sì|Dimensioni dello snapshot del volume|Byte|Media|Dimensioni di tutti gli snapshot nel volume|Nessuna dimensione|
|WriteIops|Sì|Operazioni di I/O in scrittura|Conteggio al secondo|Media|Operazioni di I/O in scrittura al secondo|Nessuna dimensione|
|XregionReplicationHealthy|Sì|Stato replica del volume integro|Conteggio|Media|Condizione della relazione, 1 o 0.|Nessuna dimensione|
|XregionReplicationLagTime|Sì|Tempo di ritardo della replica del volume|Secondi|Media|Quantità di tempo, in secondi, in base alla quale i dati nel mirror sono in ritardo rispetto all'origine.|Nessuna dimensione|
|XregionReplicationLastTransferDuration|Sì|Durata ultimo trasferimento replica volume|Secondi|Media|Quantità di tempo in secondi impiegato per il completamento dell'ultimo trasferimento.|Nessuna dimensione|
|XregionReplicationLastTransferSize|Sì|Dimensioni ultimo trasferimento replica volume|Byte|Media|Numero totale di byte trasferiti come parte dell'ultimo trasferimento.|Nessuna dimensione|
|XregionReplicationRelationshipProgress|Sì|Stato della replica del volume|Byte|Media|Quantità totale di dati trasferiti per l'operazione di trasferimento corrente.|Nessuna dimensione|
|XregionReplicationRelationshipTransferring|Sì|Trasferimento della replica del volume|Conteggio|Media|Indica se lo stato della replica del volume è' Transfer '.|Nessuna dimensione|
|XregionReplicationTotalTransferBytes|Sì|Trasferimento totale replica volume|Byte|Media|Byte cumulativi trasferiti per la relazione.|Nessuna dimensione|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway Total Time (Tempo totale gateway applicazione)|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta e la relativa risposta da inviare. Viene calcolato come media dell'intervallo dal momento in cui il gateway applicazione riceve il primo byte di una richiesta HTTP al momento in cui termina l'operazione di invio della risposta. È importante tenere presente che questo in genere include il tempo di elaborazione del gateway applicazione, il tempo in cui i pacchetti di richiesta e risposta vengono spostati in rete e il tempo impiegato dal server back-end per rispondere.|Listener|
|AvgRequestCountPerHealthyHost|No|Requests per minute per Healthy Host (Richieste al minuto per host integro)|Conteggio|Media|Numero medio di richieste al minuto per host back-end integro in un pool|BackendSettingsPool|
|BackendConnectTime|No|Backend Connect Time (Tempo di connessione back-end)|Millisecondi|Media|Tempo impiegato per stabilire una connessione con un server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Backend First Byte Response Time (Tempo di risposta primo byte back-end)|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione del primo byte dell'intestazione della risposta, il tempo di elaborazione approssimativo del server back-end|Listener, BackendServer, integri, BackendHttpSetting|
|BackendLastByteResponseTime|No|Backend Last Byte Response Time (Tempo di risposta ultimo byte back-end)|Millisecondi|Media|Intervallo di tempo tra l'inizio del tentativo di stabilire una connessione al server back-end e la ricezione dell'ultimo byte del corpo della risposta|Listener, BackendServer, integri, BackendHttpSetting|
|BackendResponseStatus|Sì|Backend Response Status (Stato risposta back-end)|Conteggio|Totale|Numero di codici di risposta HTTP generati dai membri back-end. Non sono inclusi i codici di risposta generati dal gateway applicazione.|BackendServer, integri, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Sì|Web Application Firewall Blocked Requests Rule Distribution (Web application firewall - Distribuzione regole richieste bloccate)|Conteggio|Totale|Distribuzione della regola richieste bloccate del Web Application Firewall|RuleGroup, RuleId|
|BlockedReqCount|Sì|Web Application Firewall Blocked Requests Count (Web application firewall - Conteggio richieste bloccate)|Conteggio|Totale|Conteggio richieste bloccate del Web Application Firewall|Nessuna dimensione|
|BytesReceived|Sì|Byte ricevuti|Byte|Totale|Numero totale di byte ricevuti dal gateway applicazione dai client|Listener|
|BytesSent|Sì|Byte inviati|Byte|Totale|Numero totale di byte inviati dal gateway applicazione ai client|Listener|
|CapacityUnits|No|Current Capacity Units (Unità di capacità correnti)|Conteggio|Media|Unità di capacità utilizzate|Nessuna dimensione|
|ClientRtt|No|Client RTT (Scrittura in tempo reale client)|Millisecondi|Media|Tempo medio round trip tra i client e il gateway applicazione. Questa metrica indica il tempo necessario per stabilire le connessioni e restituire i riconoscimenti|Listener|
|ComputeUnits|No|Current Compute Units (Unità di calcolo correnti)|Conteggio|Media|Unità di calcolo utilizzate|Nessuna dimensione|
|CpuUtilization|No|Utilizzo CPU|Percentuale|Media|Utilizzo corrente della CPU del gateway applicazione|Nessuna dimensione|
|CurrentConnections|Sì|Current Connections (Connessioni correnti)|Conteggio|Totale|Numero di connessioni correnti stabilite con il gateway applicazione|Nessuna dimensione|
|EstimatedBilledCapacityUnits|No|Unità di capacità fatturate stimate|Conteggio|Media|Unità di capacità stimate che verranno addebitate|Nessuna dimensione|
|FailedRequests|Sì|Richieste non riuscite|Conteggio|Totale|Numero di richieste non riuscite restituite dal gateway applicazione|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Unità di capacità fatturabili fisse|Conteggio|Media|Unità di capacità minima che verranno addebitate|Nessuna dimensione|
|HealthyHostCount|Sì|Healthy Host Count (Numero di host integri)|Conteggio|Media|Numero di host di back-end integri|BackendSettingsPool|
|MatchedCount|Sì|Web Application Firewall Total Rule Distribution (Web application firewall - Distribuzione regole totali)|Conteggio|Totale|Distribuzione della regola totale del firewall applicazione Web per il traffico in ingresso|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Nuove connessioni al secondo|Conteggio al secondo|Media|Nuove connessioni al secondo stabilite con il gateway applicazione|Nessuna dimensione|
|ResponseStatus|Sì|Stato della risposta|Conteggio|Totale|Stato della risposta HTTP restituito dal gateway applicazione|HttpStatusGroup|
|Velocità effettiva|No|Velocità effettiva|Byte al secondo|Media|Numero di byte al secondo distribuiti dal gateway applicazione|Nessuna dimensione|
|TlsProtocol|Sì|Client TLS Protocol (Protocollo TLS client)|Conteggio|Totale|Numero di richieste TLS e non TLS avviate dal client che hanno stabilito la connessione al gateway applicazione. Per visualizzare la distribuzione del protocollo TLS, filtrare in base al protocollo TLS della dimensione.|Listener, TlsProtocol|
|TotalRequests|Sì|Totale richieste|Conteggio|Totale|Numero di richieste completate e restituite dal gateway applicazione|BackendSettingsPool|
|UnhealthyHostCount|Sì|Numero di host non integri|Conteggio|Media|Numero di host di back-end non integri|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Sì|Application rules hit count (Numero di passaggi regole applicazione)|Conteggio|Totale|Numero di volte in cui sono state raggiunte le regole applicazione|Stato, motivo, protocollo|
|DataProcessed|Sì|Dati elaborati|Byte|Totale|Quantità totale di dati elaborati da questo firewall|Nessuna dimensione|
|FirewallHealth|Sì|Firewall health state (Stato di integrità firewall)|Percentuale|Media|Indica l'integrità complessiva del firewall|Stato, motivo|
|NetworkRuleHit|Sì|Network rules hit count (Numero di passaggi regole di rete)|Conteggio|Totale|Numero di volte in cui sono state eseguite le regole di rete|Stato, motivo, protocollo|
|SNATPortUtilization|Sì|SNAT port utilization (Utilizzo porte SNAT)|Percentuale|Media|Percentuale di porte SNAT in uscita attualmente in uso|Protocollo|
|Velocità effettiva|No|Velocità effettiva|BitsPerSecond|Media|Velocità effettiva elaborata da questo firewall|Nessuna dimensione|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sì|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|Sì|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|QueryVolume|Sì|Volume della query|Conteggio|Totale|Numero di query per zona DNS|Nessuna dimensione|
|RecordSetCapacityUtilization|No|Uso della capacità di set di record|Percentuale|Massimo|Percentuale della capacità di set di record usata da una zona DNS|Nessuna dimensione|
|RecordSetCount|Sì|Numero di set di record|Conteggio|Massimo|Numero di set di record in una zona DNS|Nessuna dimensione|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ArpAvailability|Sì|Arp Availability (Disponibilità ARP)|Percentuale|Media|Disponibilità ARP da MSEE verso tutti i peer.|Peering, peer|
|BgpAvailability|Sì|Bgp Availability (Disponibilità BGP)|Percentuale|Media|Disponibilità BGP da MSEE verso tutti i peer.|Peering, peer|
|BitsInPerSecond|No|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Peering|
|BitsOutPerSecond|No|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Peering|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|PeeredCircuitSKey|
|QosDropBitsInPerSecond|No|DroppedInBitsPerSecond|Conteggio al secondo|Media|Bit in ingresso dei dati eliminati al secondo|Nessuna dimensione|
|QosDropBitsOutPerSecond|No|DroppedOutBitsPerSecond|Conteggio al secondo|Media|Bit di dati in uscita eliminati al secondo|Nessuna dimensione|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sì|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Nessuna dimensione|
|BitsOutPerSecond|Sì|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Nessuna dimensione|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AdminState|Sì|AdminState|Conteggio|Media|Stato amministratore della porta|Collegamento|
|LineProtocol|Sì|LineProtocol|Conteggio|Media|Stato del protocollo di riga della porta|Collegamento|
|PortBitsInPerSecond|Sì|BitsInPerSecond|Conteggio al secondo|Media|Bit in ingresso in Azure al secondo|Collegamento|
|PortBitsOutPerSecond|Sì|BitsOutPerSecond|Conteggio al secondo|Media|Bit in uscita da Azure al secondo|Collegamento|
|RxLightLevel|Sì|RxLightLevel|Conteggio|Media|Livello di luce RX in dBm|Collegamento, corsia|
|TxLightLevel|Sì|TxLightLevel|Conteggio|Media|Livello di luce TX in dBm|Collegamento, corsia|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Sì|Percentuale di integrità del back-end|Percentuale|Media|Percentuale di probe di integrità riusciti dal proxy HTTP/S ai back-end|Backend, BackendPool|
|BackendRequestCount|Sì|Conteggio delle richieste del back-end|Conteggio|Totale|Numero di richieste inviate dal proxy HTTP/S ai back-end|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Sì|Latenza della richiesta del back-end|Millisecondi|Media|Tempo calcolato da quando la richiesta è stata inviata dal proxy HTTP/S al back-end fino alla ricezione nel proxy HTTP/S dell'ultimo byte di risposta dal back-end|Back-end|
|BillableResponseSize|Sì|Billable Response Size (Dimensione risposta fatturabile)|Byte|Totale|Numero di byte fatturabili (2 KB minimo per richiesta) inviati come risposte dal proxy HTTP/S ai client.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Sì|Conteggio richieste|Conteggio|Totale|Numero di richieste client gestite dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Sì|Dimensioni della richiesta|Byte|Totale|Numero di byte inviati come richieste dai client al proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Sì|Dimensioni della risposta|Byte|Totale|Numero di byte inviati come risposte dal proxy HTTP/S ai client|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Sì|Latenza totale|Millisecondi|Media|Tempo calcolato da quando la richiesta client è stata ricevuta dal proxy HTTP/S fino a quando il client ha confermato l'ultimo byte di risposta dal proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Sì|Conteggio delle richieste web application firewall|Conteggio|Totale|Numero di richieste client elaborate da Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Porte SNAT allocate|Conteggio|Media|Numero totale di porte SNAT allocate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Sì|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Sì|Stato probe di integrità|Conteggio|Media|Stato probe di integrità media di Load Balancer per periodo di tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Sì|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Sì|Numero di connessioni SNAT|Conteggio|Totale|Numero totale di nuove connessioni SNAT create nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Sì|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Porte SNAT utilizzate|Conteggio|Media|Numero totale di porte SNAT usate nel periodo di tempo|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Sì|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media del percorso dati di Load Balancer per periodo di tempo|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Sì|Byte ricevuti|Byte|Totale|Numero di byte ricevuti dall'interfaccia di rete|Nessuna dimensione|
|BytesSentRate|Sì|Byte inviati|Byte|Totale|Numero di byte inviati dall'interfaccia di rete|Nessuna dimensione|
|PacketsReceivedRate|Sì|Pacchetti ricevuti|Conteggio|Totale|Numero di pacchetti ricevuti dall'interfaccia di rete|Nessuna dimensione|
|PacketsSentRate|Sì|Pacchetti inviati|Conteggio|Totale|Numero di pacchetti inviati dall'interfaccia di rete|Nessuna dimensione|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Sì|Avg. Tempo di round trip (ms)|Millisecondi|Media|Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione|Nessuna dimensione|
|ChecksFailedPercent|Sì|Checks Failed Percent (Preview) (Percentuale controlli non superati (anteprima))|Percentuale|Media|% dei controlli di monitoraggio della connettività non riusciti|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Sì|% di probe non riusciti|Percentuale|Media|% di probe di monitoraggio connettività non riusciti|Nessuna dimensione|
|RoundTripTimeMs|Sì|Round-Trip Time (ms) (Preview) (Tempo di round trip (ms) (anteprima))|Millisecondi|Media|Tempo di round trip in millisecondi per i controlli di monitoraggio della connettività|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinaname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ByteCount|Sì|Numero di byte|Conteggio|Totale|Numero totale di byte trasmessi nel periodo di tempo|Port, Direction|
|BytesDroppedDDoS|Sì|DDoS byte in ingresso eliminati|Byte al secondo|Massimo|DDoS byte in ingresso eliminati|Nessuna dimensione|
|BytesForwardedDDoS|Sì|DDoS byte in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte in ingresso inoltrati|Nessuna dimensione|
|BytesInDDoS|Sì|DDoS byte in ingresso|Byte al secondo|Massimo|DDoS byte in ingresso|Nessuna dimensione|
|DDoSTriggerSYNPackets|Sì|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Conteggio al secondo|Massimo|Inbound SYN packets to trigger DDoS mitigation (Pacchetti SYN in ingresso per attivare la mitigazione DDoS)|Nessuna dimensione|
|DDoSTriggerTCPPackets|Sì|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti TCP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|DDoSTriggerUDPPackets|Sì|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Conteggio al secondo|Massimo|Pacchetti UDP in ingresso per attivare la mitigazione DDoS|Nessuna dimensione|
|IfUnderDDoSAttack|Sì|Sotto attacco DDoS o no|Conteggio|Massimo|Sotto attacco DDoS o no|Nessuna dimensione|
|PacketCount|Sì|Numero di pacchetti|Conteggio|Totale|Numero totale di pacchetti trasmessi nel periodo di tempo|Port, Direction|
|PacketsDroppedDDoS|Sì|DDoS pacchetti in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso eliminati|Nessuna dimensione|
|PacketsForwardedDDoS|Sì|DDoS pacchetti in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso inoltrati|Nessuna dimensione|
|PacketsInDDoS|Sì|DDoS pacchetti in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti in ingresso|Nessuna dimensione|
|SynCount|Sì|Numero di SYN|Conteggio|Totale|Numero totale di pacchetti SYN trasmessi nel periodo di tempo|Port, Direction|
|TCPBytesDroppedDDoS|Sì|DDoS byte TCP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte TCP in ingresso eliminati|Nessuna dimensione|
|TCPBytesForwardedDDoS|Sì|DDoS byte TCP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte TCP in ingresso inoltrati|Nessuna dimensione|
|TCPBytesInDDoS|Sì|DDoS byte TCP in ingresso|Byte al secondo|Massimo|DDoS byte TCP in ingresso|Nessuna dimensione|
|TCPPacketsDroppedDDoS|Sì|DDoS pacchetti TCP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso eliminati|Nessuna dimensione|
|TCPPacketsForwardedDDoS|Sì|DDoS pacchetti TCP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso inoltrati|Nessuna dimensione|
|TCPPacketsInDDoS|Sì|DDoS pacchetti TCP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti TCP in ingresso|Nessuna dimensione|
|UDPBytesDroppedDDoS|Sì|DDoS byte UDP in ingresso eliminati|Byte al secondo|Massimo|DDoS byte UDP in ingresso eliminati|Nessuna dimensione|
|UDPBytesForwardedDDoS|Sì|DDoS byte UDP in ingresso inoltrati|Byte al secondo|Massimo|DDoS byte UDP in ingresso inoltrati|Nessuna dimensione|
|UDPBytesInDDoS|Sì|DDoS byte UDP in ingresso|Byte al secondo|Massimo|DDoS byte UDP in ingresso|Nessuna dimensione|
|UDPPacketsDroppedDDoS|Sì|DDoS pacchetti UDP in ingresso eliminati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso eliminati|Nessuna dimensione|
|UDPPacketsForwardedDDoS|Sì|DDoS pacchetti UDP in ingresso inoltrati|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso inoltrati|Nessuna dimensione|
|UDPPacketsInDDoS|Sì|DDoS pacchetti UDP in ingresso|Conteggio al secondo|Massimo|DDoS pacchetti UDP in ingresso|Nessuna dimensione|
|VipAvailability|Sì|Disponibilità del percorso dati|Conteggio|Media|Disponibilità media dell'indirizzo IP per periodo di tempo|Porta|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Sì|Stato endpoint per endpoint|Conteggio|Massimo|1 se lo stato probe di un endpoint è "Abilitato", in caso contrario 0.|EndpointName|
|QpsByEndpoint|Sì|Query da endpoint restituite|Conteggio|Totale|Numero di volte in cui un endpoint di Gestione traffico è stato restituito nell'intervallo di tempo specificato|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AverageBandwidth|Sì|Larghezza di banda S2S gateway|Byte al secondo|Media|Larghezza di banda media da sito a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SBandwidth|Sì|Larghezza di banda per connessione da punto a sito gateway|Byte al secondo|Media|Larghezza di banda media per connessione da punto a sito per un gateway in byte al secondo|Nessuna dimensione|
|P2SConnectionCount|Sì|Numero di connessioni da punto a sito|Conteggio|Massimo|Numero di connessioni da punto a sito di un gateway|Protocollo|
|TunnelAverageBandwidth|Sì|Larghezza di banda tunnel|Byte al secondo|Media|Larghezza di banda media di un tunnel in byte al secondo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Sì|Byte in uscita tunnel|Byte|Totale|Byte in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Sì|Eliminazione pacchetti per mancata corrispondenza selettore traffico in uscita tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in uscita per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Sì|Pacchetti in uscita tunnel|Conteggio|Totale|Conteggio dei pacchetti in uscita di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Sì|Byte in ingresso tunnel|Byte|Totale|Byte in ingresso di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Sì|Eliminazione pacchetti per mancata corrispondenza selettore traffico in ingresso tunnel|Conteggio|Totale|Conteggio delle eliminazioni di pacchetti in ingresso per mancata corrispondenza del selettore traffico di un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Sì|Pacchetti in ingresso tunnel|Conteggio|Totale|Conteggio dei pacchetti in ingresso di un tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Sì|Round trip time for Pings to a VM (Tempo di round trip per ping a una macchina virtuale)|Millisecondi|Media|Tempo di round trip per i ping inviati a una macchina virtuale di destinazione|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sì|Failed Pings to a VM (Ping non riusciti a una macchina virtuale)|Percentuale|Media|Percentuale del numero di ping non riusciti in totale ping inviati di una macchina virtuale di destinazione|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|incoming|Sì|Messaggi in ingresso|Conteggio|Totale|Numero di tutte le chiamate all'API di invio riuscite. |Nessuna dimensione|
|incoming.all.failedrequests|Sì|Tutte le richieste in ingresso non riuscite|Conteggio|Totale|Totale richieste in ingresso non riuscite per un hub di notifica|Nessuna dimensione|
|incoming.all.requests|Sì|Tutte le richieste in ingresso|Conteggio|Totale|Totale richieste in ingresso per un hub di notifica|Nessuna dimensione|
|incoming.scheduled|Sì|Notifiche push pianificate inviate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
|incoming.scheduled.cancel|Sì|Notifiche push pianificate annullate|Conteggio|Totale|Notifiche push pianificate annullate|Nessuna dimensione|
|installation.all|Sì|Operazioni di gestione installazione|Conteggio|Totale|Operazioni di gestione installazione|Nessuna dimensione|
|installation.delete|Sì|Elimina operazioni di installazione|Conteggio|Totale|Elimina operazioni di installazione|Nessuna dimensione|
|installation.get|Sì|Ottieni operazioni di installazione|Conteggio|Totale|Ottieni operazioni di installazione|Nessuna dimensione|
|installation.patch|Sì|Operazioni di installazione patch|Conteggio|Totale|Operazioni di installazione patch|Nessuna dimensione|
|installation.upsert|Sì|Crea o aggiorna operazioni di installazione|Conteggio|Totale|Crea o aggiorna operazioni di installazione|Nessuna dimensione|
|notificationhub.pushes|Sì|Tutte le notifiche in uscita|Conteggio|Totale|Tutte le notifiche in uscita dell'hub di notifica|Nessuna dimensione|
|outgoing.allpns.badorexpiredchannel|Sì|Errori canale non valido o scaduto|Conteggio|Totale|Numero di push non riusciti perché il canale/token/ID di registrazione nella registrazione è scaduto o non è valido.|Nessuna dimensione|
|outgoing.allpns.channelerror|Sì|Errori canale|Conteggio|Totale|Numero di push non riusciti perché il canale non è valido, non è associato all'app corretta, è limitato o è scaduto.|Nessuna dimensione|
|outgoing.allpns.invalidpayload|Sì|Errori payload|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS ha restituito un errore di payload non valido.|Nessuna dimensione|
|outgoing.allpns.pnserror|Sì|Errori sistema di notifica esterno|Conteggio|Totale|Numero di push non riusciti perché si è verificato un problema di comunicazione con il sistema PNS (problemi di autenticazione esclusi).|Nessuna dimensione|
|outgoing.allpns.success|Sì|Notifiche riuscite|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.apns.badchannel|Sì|Errore canale APNS non valido|Conteggio|Totale|Numero di push non riusciti perché il token non è valido (codice di stato del servizio APN: 8).|Nessuna dimensione|
|outgoing.apns.expiredchannel|Sì|Errore canale APNS scaduto|Conteggio|Totale|Numero di token invalidati dal canale di feedback del servizio APN.|Nessuna dimensione|
|outgoing.apns.invalidcredentials|Sì|Errori di autorizzazione del servizio APN|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.apns.invalidnotificationsize|Sì|Errore dimensioni notifica APNS non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (codice di stato del servizio APN: 7).|Nessuna dimensione|
|outgoing.apns.pnserror|Sì|Errori APNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con il servizio APN.|Nessuna dimensione|
|outgoing.apns.success|Sì|Notifiche APNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.gcm.authenticationerror|Sì|Errori di autenticazione GCM|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate, perché le credenziali sono bloccate oppure perché l'ID del mittente non è configurato correttamente nell'app (risultato GCM: MismatchedSenderId).|Nessuna dimensione|
|outgoing.gcm.badchannel|Sì|Errore canale GCM non valido|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è stato riconosciuto (risultato GCM: Invalid Registration).|Nessuna dimensione|
|outgoing.gcm.expiredchannel|Sì|Errore canale GCM scaduto|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione è scaduto (risultato GCM: NotRegistered).|Nessuna dimensione|
|outgoing.gcm.invalidcredentials|Sì|Errori di autorizzazione GCM (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.gcm.invalidnotificationformat|Sì|Formato notifiche GCM non valido|Conteggio|Totale|Numero di push non riusciti perché il formato del payload non è corretto (risultato GCM: InvalidDataKey o InvalidTtl).|Nessuna dimensione|
|outgoing.gcm.invalidnotificationsize|Sì|Errore dimensioni notifica GCM non valide|Conteggio|Totale|Numero di push non riusciti perché il payload è troppo grande (risultato GCM: MessageTooBig).|Nessuna dimensione|
|outgoing.gcm.pnserror|Sì|Errori GCM|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con GCM.|Nessuna dimensione|
|outgoing.gcm.success|Sì|Notifiche GCM completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.gcm.throttled|Sì|Notifiche GCM limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è stata limitata da GCM (codice di stato GCM: 501-599 o risultato: Unavailable).|Nessuna dimensione|
|outgoing.gcm.wrongchannel|Sì|Errore canale GCM errato|Conteggio|Totale|Numero di push non riusciti perché l'ID di registrazione nella registrazione non è associato all'app corrente (risultato GCM: InvalidPackageName).|Nessuna dimensione|
|outgoing.mpns.authenticationerror|Sì|Errori di autenticazione MPNS|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.mpns.badchannel|Sì|Errori canale MPNS errato|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato MPNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.channeldisconnected|Sì|Canale MPNS disconnesso|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione è disconnesso (stato MPNS: 412 - Non trovato).|Nessuna dimensione|
|outgoing.mpns.dropped|Sì|Notifiche MPNS eliminate|Conteggio|Totale|Numero di push che sono stati rimossi dal servizio MPNS (intestazione della risposta MPNS: X-NotificationStatus: QueueFull o Suppressed).|Nessuna dimensione|
|outgoing.mpns.invalidcredentials|Sì|Credenziali MPNS non valide|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate.|Nessuna dimensione|
|outgoing.mpns.invalidnotificationformat|Sì|Formato notifiche MPNS non valido|Conteggio|Totale|Numero di push non riusciti perché il payload della notifica è troppo grande.|Nessuna dimensione|
|outgoing.mpns.pnserror|Sì|Errori MPNS|Conteggio|Totale|Numero di push non riusciti a causa di errori di comunicazione con MPNS.|Nessuna dimensione|
|outgoing.mpns.success|Sì|Notifiche MPNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.mpns.throttled|Sì|Notifiche MPNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio MPNS (WNS/MPNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.wns.authenticationerror|Sì|Errori di autenticazione WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con Windows Live, di credenziali non valide o di token non corretto.|Nessuna dimensione|
|outgoing.wns.badchannel|Sì|Errore canale WNS non valido|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale nella registrazione non è stato riconosciuto (stato WNS: 404 - Non trovato).|Nessuna dimensione|
|outgoing.wns.channeldisconnected|Sì|Canale WNS disconnesso|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.channelthrottled|Sì|Canale WNS limitato|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (intestazione della risposta WNS: X-WNS-NotificationStatus: channelThrottled).|Nessuna dimensione|
|outgoing.wns.dropped|Sì|Notifiche WNS eliminate|Conteggio|Totale|La notifica è stata rimossa perché l'URI del canale nella registrazione è limitato (X-WNS-NotificationStatus: dropped ma non X-WNS-DeviceConnectionStatus: disconnected).|Nessuna dimensione|
|outgoing.wns.expiredchannel|Sì|Errore canale WNS scaduto|Conteggio|Totale|Numero di push non riusciti perché l'URI del canale è scaduto (stato WNS: 410 - Non disponibile).|Nessuna dimensione|
|outgoing.wns.invalidcredentials|Sì|Errori di autorizzazione WNS (credenziali non valide)|Conteggio|Totale|Numero di push non riusciti perché il sistema PNS non accetta le credenziali specificate o perché le credenziali sono bloccate. Windows Live non riconosce le credenziali.|Nessuna dimensione|
|outgoing.wns.invalidnotificationformat|Sì|Formato notifica WNS non valido|Conteggio|Totale|Il formato della notifica non è valido (stato WNS: 400). Si noti che WNS non rifiuta tutti i payload non validi.|Nessuna dimensione|
|outgoing.wns.invalidnotificationsize|Sì|Errore dimensioni notifica WNS non valide|Conteggio|Totale|Il payload della notifica è troppo grande (stato WNS: 413).|Nessuna dimensione|
|outgoing.wns.invalidtoken|Sì|Errori di autorizzazione WNS (token non valido)|Conteggio|Totale|Il token fornito a WNS non è valido (stato WNS: 401 - Non autorizzato).|Nessuna dimensione|
|outgoing.wns.pnserror|Sì|Errori WNS|Conteggio|Totale|La notifica non è stata recapitata a causa di errori di comunicazione con WNS.|Nessuna dimensione|
|outgoing.wns.success|Sì|Notifiche WNS completate|Conteggio|Totale|Numero di tutte le notifiche riuscite.|Nessuna dimensione|
|outgoing.wns.throttled|Sì|Notifiche WNS limitate|Conteggio|Totale|Numero di push non riusciti perché l'app è limitata dal servizio WNS (stato WNS: 406 - Non accettabile).|Nessuna dimensione|
|outgoing.wns.tokenproviderunreachable|Sì|Errori di autorizzazione WNS (non disponibile)|Conteggio|Totale|Windows Live non è raggiungibile.|Nessuna dimensione|
|outgoing.wns.wrongtoken|Sì|Errori di autorizzazione WNS (token errato)|Conteggio|Totale|Il token fornito a WNS è valido ma per un'altra applicazione (stato WNS: 403 - Non consentito). Questo scenario può verificarsi se l'URI del canale nella registrazione è associato a un'altra app. Verificare che l'app client sia associata all'app le cui credenziali si trovano nell'hub di notifica.|Nessuna dimensione|
|registration.all|Sì|Operazioni di registrazione|Conteggio|Totale|Numero di tutte le operazioni di registrazione riuscite (creazioni, aggiornamenti, query ed eliminazioni). |Nessuna dimensione|
|registration.create|Sì|Operazioni di creazione registrazione|Conteggio|Totale|Numero di tutte le creazioni di registrazioni riuscite.|Nessuna dimensione|
|registration.delete|Sì|Operazioni di eliminazione registrazione|Conteggio|Totale|Numero di tutte le eliminazioni di registrazioni riuscite.|Nessuna dimensione|
|registration.get|Sì|Operazioni di lettura registrazione|Conteggio|Totale|Numero di tutte le query su registrazioni riuscite.|Nessuna dimensione|
|registration.update|Sì|Operazioni di aggiornamento registrazione|Conteggio|Totale|Numero di tutti gli aggiornamenti di registrazioni riusciti.|Nessuna dimensione|
|scheduled.pending|Sì|Notifiche pianificate in sospeso|Conteggio|Totale|Notifiche pianificate in sospeso|Nessuna dimensione|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Average_% Available Memory|Sì|% Available Memory|Conteggio|Media|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Sì|% Available Swap Space|Conteggio|Media|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Sì|% byte vincolati in uso|Conteggio|Media|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Sì|% DPC Time|Conteggio|Media|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|Sì|% Free Inodes|Conteggio|Media|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Sì|% Free Space|Conteggio|Media|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Sì|% Idle Time|Conteggio|Media|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Sì|% Interrupt Time|Conteggio|Media|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Sì|% IO Wait Time|Conteggio|Media|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Sì|% Nice Time|Conteggio|Media|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Sì|% Privileged Time|Conteggio|Media|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Sì|% di tempo processore|Conteggio|Media|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Sì|% Used Inodes|Conteggio|Media|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Sì|% Used Memory|Conteggio|Media|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Sì|% Used Space|Conteggio|Media|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Sì|% Used Swap Space|Conteggio|Media|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Sì|% User Time|Conteggio|Media|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Sì|MByte disponibili|Conteggio|Media|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Sì|Available MBytes Memory|Conteggio|Media|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Sì|Available MBytes Swap|Conteggio|Media|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Sì|Avg. Disk sec/Read|Conteggio|Media|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Sì|Avg. Disk sec/Transfer|Conteggio|Media|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Sì|Avg. Disk sec/Write|Conteggio|Media|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Sì|Byte ricevuti/sec|Conteggio|Media|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Sì|Byte inviati/sec|Conteggio|Media|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Sì|Totale byte/sec|Conteggio|Media|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Sì|Lunghezza corrente coda del disco|Conteggio|Media|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Sì|Byte letti da disco/sec|Conteggio|Media|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Sì|Letture disco/sec|Conteggio|Media|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Sì|Disk Transfers/sec|Conteggio|Media|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Sì|Byte scritti su disco/sec|Conteggio|Media|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Sì|Scritture disco/sec|Conteggio|Media|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Sì|Free Megabytes|Conteggio|Media|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Sì|Free Physical Memory|Conteggio|Media|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Sì|Free Space in Paging Files|Conteggio|Media|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Sì|Free Virtual Memory|Conteggio|Media|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Sì|Logical Disk Bytes/sec|Conteggio|Media|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Sì|Page Reads/sec|Conteggio|Media|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Sì|Page Writes/sec|Conteggio|Media|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Sì|Pages/sec|Conteggio|Media|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Sì|Pct Privileged Time|Conteggio|Media|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Sì|Pct User Time|Conteggio|Media|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Sì|Physical Disk Bytes/sec|Conteggio|Media|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Sì|Processi|Conteggio|Media|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Sì|Lunghezza coda processore|Conteggio|Media|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Sì|Size Stored In Paging Files|Conteggio|Media|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Sì|Total Bytes|Conteggio|Media|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Sì|Total Bytes Received|Conteggio|Media|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Sì|Total Bytes Transmitted|Conteggio|Media|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Sì|Total Collisions|Conteggio|Media|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Sì|Total Packets Received|Conteggio|Media|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Sì|Total Packets Transmitted|Conteggio|Media|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Sì|Total Rx Errors|Conteggio|Media|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Sì|Total Tx Errors|Conteggio|Media|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Sì|Uptime|Conteggio|Media|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Sì|Used MBytes Swap Space|Conteggio|Media|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Sì|Used Memory kBytes|Conteggio|Media|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Sì|Used Memory MBytes|Conteggio|Media|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Sì|Utenti|Conteggio|Media|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Sì|Virtual Shared Memory|Conteggio|Media|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Evento|Sì|Event|Conteggio|Media|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Heartbeat|Sì|Heartbeat|Conteggio|Totale|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aggiornamento|Sì|Aggiornamento|Conteggio|Media|Aggiornamento|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Sì|Frequenza traffico in uscita|BitsPerSecond|Media|Velocità di traffico in uscita in bit al secondo|ConnectionId|
|IngressTrafficRate|Sì|Velocità di traffico in ingresso|BitsPerSecond|Media|Velocità di traffico in ingresso in bit al secondo|ConnectionId|
|SessionAvailabilityV4|Sì|Disponibilità sessione V4|Percentuale|Media|Disponibilità della sessione V4|ConnectionId|
|SessionAvailabilityV6|Sì|Disponibilità della sessione V6|Percentuale|Media|Disponibilità della sessione V6|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|PrefixLatency|Sì|Latenza prefisso|Millisecondi|Media|Latenza mediana del prefisso|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|memory_metric|Sì|Memoria|Byte|Media|Memoria. Intervallo di 0-3 GB per A1, 0-5 GB per A2, 0-10 GB per A3, 0-25 GB per A4, 0-50 GB per A5 e 0-100 GB per A6|Nessuna dimensione|
|memory_thrashing_metric|Sì|Thrashing di memoria (set di dati)|Percentuale|Media|Thrashing di memoria medio.|Nessuna dimensione|
|qpu_high_utilization_metric|Sì|QPU High Utilization (Utilizzo elevato QPU)|Conteggio|Totale|Utilizzo elevato di QPU nell'ultimo minuto, 1 per utilizzo elevato, altrimenti 0|Nessuna dimensione|
|QueryDuration|Sì|Durata query (set di dati)|Millisecondi|Media|Durata delle query DAX nell'ultimo intervallo|Nessuna dimensione|
|QueryPoolJobQueueLength|Sì|Query Pool Job Queue Length (Datasets) (Lunghezza pool di processi nel pool di query (set di dati))|Conteggio|Media|Numero dei processi nella coda del pool dei thread di query.|Nessuna dimensione|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.Relay.|EntityName|
|ActiveListeners|No|ActiveListeners|Conteggio|Totale|Totale listener attivi per Microsoft.Relay.|EntityName|
|BytesTransferred|Sì|BytesTransferred|Conteggio|Totale|Totale byte trasferiti per Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Conteggio|Totale|Errore del client su connessioni listener per Microsoft.Relay.|EntityName, |
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Conteggio|Totale|Errore del server su connessioni listener per Microsoft.Relay.|EntityName, |
|ListenerConnections-Success|No|ListenerConnections-Success|Conteggio|Totale|Connessioni listener riuscite per Microsoft.Relay.|EntityName, |
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Conteggio|Totale|Totale connessioni listener per Microsoft.Relay.|EntityName|
|ListenerDisconnects|No|ListenerDisconnects|Conteggio|Totale|Totale disconnessioni listener per Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Conteggio|Totale|Errore del client su connessioni mittente per Microsoft.Relay.|EntityName, |
|SenderConnections-ServerError|No|SenderConnections-ServerError|Conteggio|Totale|Errore del server su connessioni mittente per Microsoft.Relay.|EntityName, |
|Connessioni mittente - Operazione riuscita|No|Connessioni mittente - Operazione riuscita|Conteggio|Totale|Connessioni mittente riuscite per Microsoft.Relay.|EntityName, |
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Conteggio|Totale|Totale richieste connessioni mittente per Microsoft.Relay.|EntityName|
|Disconnessioni mittente|No|Disconnessioni mittente|Conteggio|Totale|Totale disconnessioni mittente per Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|SearchLatency|Sì|Latenza ricerca|Secondi|Media|La latenza di ricerca media per il servizio di ricerca|Nessuna dimensione|
|SearchQueriesPerSecond|Sì|Query di ricerca al secondo|Conteggio al secondo|Media|Le query di ricerca al secondo per il servizio di ricerca|Nessuna dimensione|
|ThrottledSearchQueriesPercentage|Sì|Percentuale query di ricerca limitate|Percentuale|Media|La percentuale di query di ricerca che sono state limitate per il servizio di ricerca|Nessuna dimensione|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Conteggio|Totale|Totale connessioni attive per Microsoft.ServiceBus.|Nessuna dimensione|
|ActiveMessages|No|Numero di messaggi attivi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi attivi contenuti in una coda o in un argomento.|EntityName|
|ConnectionsClosed|No|Connessioni chiuse.|Conteggio|Media|Connessioni chiuse per Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|No|Connessioni aperte.|Conteggio|Media|Connessioni aperte per Microsoft.ServiceBus.|EntityName|
|CPUXNS|No|CPU (deprecato)|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio. Questa metrica è depricated. Usare invece la metrica CPU (NamespaceCpuUsage).|Nessuna dimensione|
|DeadletteredMessages|No|Numero di messaggi non recapitabili in una coda o in un argomento.|Conteggio|Media|Numero di messaggi non recapitabili in una coda o in un argomento.|EntityName|
|IncomingMessages|Sì|Messaggi in ingresso|Conteggio|Totale|Messaggi in ingresso per Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Sì|Richieste in ingresso|Conteggio|Totale|Richieste in ingresso per Microsoft.ServiceBus.|EntityName|
|Messaggi|No|Numero di messaggi contenuti in una coda o in un argomento.|Conteggio|Media|Numero di messaggi contenuti in una coda o in un argomento.|EntityName|
|NamespaceCpuUsage|No|CPU|Percentuale|Massimo|Metrica utilizzo CPU spazio dei nomi premium del bus di servizio.|Nessuna dimensione|
|NamespaceMemoryUsage|No|Utilizzo della memoria|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio.|Nessuna dimensione|
|OutgoingMessages|Sì|Messaggi in uscita|Conteggio|Totale|Messaggi in uscita per Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|No|Numero di messaggi pianificati in una coda o in un argomento.|Conteggio|Media|Numero di messaggi pianificati in una coda o in un argomento.|EntityName|
|ServerErrors|No|Errori server.|Conteggio|Totale|Errori del server per Microsoft.ServiceBus.|EntityName, |
|Dimensione|No|Dimensione|Byte|Media|Dimensioni di una coda o di un argomento in byte.|EntityName|
|SuccessfulRequests|No|Richieste riuscite|Conteggio|Totale|Richieste riuscite totali per uno spazio dei nomi|EntityName, |
|ThrottledRequests|No|Richieste limitate.|Conteggio|Totale|Richieste limitate per Microsoft.ServiceBus.|EntityName, |
|UserErrors|No|Errori utente.|Conteggio|Totale|Errori utente per Microsoft.ServiceBus.|EntityName, |
|WSXNS|No|Utilizzo memoria (deprecato)|Percentuale|Massimo|Metrica utilizzo memoria spazio dei nomi premium del bus di servizio. Questa metrica è deprecata. Usare invece la metrica utilizzo memoria (NamespaceMemoryUsage).|Nessuna dimensione|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Conteggio|Media|Utilizzo effettivo della CPU in Milli Core|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Byte|Media|Utilizzo effettivo della memoria in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Conteggio|Media|CPU allocata a questo contenitore in Milli Core|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Byte|Media|Memoria allocata a questo contenitore in MB|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Conteggio|Media|Stato dell'applicazione Service Fabric mesh|ApplicationName, stato|
|ContainerStatus|No|ContainerStatus|Conteggio|Media|Stato del contenitore nell'applicazione Service Fabric mesh|ApplicationName, ServiceName, codepackagename, ServiceReplicaName, stato|
|CpuUtilization|No|CpuUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Percentuale|Media|Utilizzo della CPU per questo contenitore come percentuale di AllocatedCpu|ApplicationName, ServiceName, codepackagename, ServiceReplicaName|
|RestartCount|No|RestartCount|Conteggio|Media|Riavviare il conteggio di un contenitore nell'applicazione Service Fabric mesh|ApplicationName, status, ServiceName, ServiceReplicaName, codepackagename|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Conteggio|Media|Stato di integrità di una replica del servizio nell'applicazione Service Fabric mesh|ApplicationName, status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Conteggio|Media|Stato di integrità di un servizio nell'applicazione Service Fabric mesh|ApplicationName, stato, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ConnectionCount|Sì|Numero di connessioni|Conteggio|Massimo|Quantità di connessioni utente.|Endpoint|
|InboundTraffic|Sì|Traffico in ingresso|Byte|Totale|Traffico in ingresso del servizio|Nessuna dimensione|
|MessageCount|Sì|Numero messaggi|Conteggio|Totale|Quantità totale di messaggi.|Nessuna dimensione|
|OutboundTraffic|Sì|Traffico in uscita|Byte|Totale|Traffico in uscita del servizio|Nessuna dimensione|
|SystemErrors|Sì|Errori di sistema|Percentuale|Massimo|Percentuale di errori di sistema|Nessuna dimensione|
|UserErrors|Sì|Errori utente|Percentuale|Massimo|Percentuale di errori utente|Nessuna dimensione|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Sì|Percentuale CPU Media|Percentuale|Media|Percentuale CPU Media|Nessuna dimensione|
|io_bytes_read|Sì|Byte di I/O letti|Byte|Media|Byte di I/O letti|Nessuna dimensione|
|io_bytes_written|Sì|Byte di I/O scritti|Byte|Media|Byte di I/O scritti|Nessuna dimensione|
|io_requests|Sì|Numero di richieste I/O|Conteggio|Media|Numero di richieste I/O|Nessuna dimensione|
|reserved_storage_mb|Sì|Spazio di archiviazione riservato|Conteggio|Media|Spazio di archiviazione riservato|Nessuna dimensione|
|storage_space_used_mb|Sì|Spazio di archiviazione usato|Conteggio|Media|Spazio di archiviazione usato|Nessuna dimensione|
|virtual_core_count|Sì|Numero di core virtuali|Conteggio|Media|Numero di core virtuali|Nessuna dimensione|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|active_queries|Sì|Query attive|Conteggio|Totale|Query attive in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|Nessuna dimensione|
|allocated_data_storage|Sì|Spazio dati allocato|Byte|Media|Archiviazione dati allocata. Non applicabile ai data warehouse.|Nessuna dimensione|
|app_cpu_billed|Sì|CPU dell'app fatturata|Conteggio|Totale|CPU app fatturata. Si applica ai database senza server.|Nessuna dimensione|
|app_cpu_percent|Sì|App CPU percentage (Percentuale CPU app)|Percentuale|Media|Percentuale CPU app. Si applica ai database senza server.|Nessuna dimensione|
|app_memory_percent|Sì|App memory percentage (Percentuale memoria app)|Percentuale|Media|Percentuale di memoria dell'app. Si applica ai database senza server.|Nessuna dimensione|
|base_blob_size_bytes|Sì|Dimensioni di archiviazione BLOB di base|Byte|Massimo|Dimensioni di archiviazione BLOB di base. Si applica ai database con iperscalabilità.|Nessuna dimensione|
|blocked_by_firewall|Sì|Blocco da parte del firewall|Conteggio|Totale|Blocco da parte del firewall|Nessuna dimensione|
|cache_hit_percent|Sì|Percentuale dei riscontri nella cache|Percentuale|Massimo|Percentuale riscontri cache. Si applica solo ai data warehouse.|Nessuna dimensione|
|cache_used_percent|Sì|Percentuale della cache utilizzata|Percentuale|Massimo|Percentuale di utilizzo della cache. Si applica solo ai data warehouse.|Nessuna dimensione|
|connection_failed|Sì|Connessioni non riuscite|Conteggio|Totale|Connessioni non riuscite|Nessuna dimensione|
|connection_successful|Sì|Connessioni riuscite|Conteggio|Totale|Connessioni riuscite|Nessuna dimensione|
|cpu_limit|Sì|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai database basati su vCore.|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|cpu_used|Sì|CPU used (CPU usata)|Conteggio|Media|CPU utilizzata. Si applica ai database basati su vCore.|Nessuna dimensione|
|deadlock|Sì|Deadlock|Conteggio|Totale|Deadlock. Non applicabile ai data warehouse.|Nessuna dimensione|
|diff_backup_size_bytes|Sì|Dimensioni di archiviazione del backup differenziale|Byte|Massimo|Dimensioni di archiviazione del backup differenziale cumulative. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuna dimensione|
|dtu_consumption_percent|Sì|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai database basati su DTU.|Nessuna dimensione|
|dtu_limit|Sì|Limite DTU|Conteggio|Media|Limite DTU. Si applica ai database basati su DTU.|Nessuna dimensione|
|dtu_used|Sì|Uso DTU|Conteggio|Media|DTU usato. Si applica ai database basati su DTU.|Nessuna dimensione|
|dw_backup_size_gb|Sì|Dimensioni dello spazio di archiviazione dei dati (GB)|Conteggio|Totale|Le dimensioni di archiviazione dei dati sono costituite dalle dimensioni dei dati e dal log delle transazioni. La metrica viene conteggiata in base alla parte "archiviazione" della fattura. Si applica solo ai data warehouse.|Nessuna dimensione|
|dw_geosnapshot_size_gb|Sì|Dimensioni di archiviazione per il ripristino di emergenza (GB)|Conteggio|Totale|Le dimensioni di archiviazione per il ripristino di emergenza vengono riflesse in "archiviazione ripristino di emergenza" nella fattura. Si applica solo ai data warehouse.|Nessuna dimensione|
|dw_snapshot_size_gb|Sì|Dimensioni di archiviazione snapshot (GB)|Conteggio|Totale|Dimensioni di archiviazione snapshot è la dimensione delle modifiche incrementali acquisite dagli snapshot per creare punti di ripristino automatici e definiti dall'utente. La metrica viene conteggiata in base alla parte "archiviazione" della fattura. Si applica solo ai data warehouse.|Nessuna dimensione|
|dwu_consumption_percent|Sì|Percentuale DWU|Percentuale|Massimo|Percentuale di DWU. Si applica solo ai data warehouse.|Nessuna dimensione|
|dwu_limit|Sì|Limite DWU|Conteggio|Massimo|Limite DWU. Si applica solo ai data warehouse.|Nessuna dimensione|
|dwu_used|Sì|Uso DWU|Conteggio|Massimo|DWU usato. Si applica solo ai data warehouse.|Nessuna dimensione|
|full_backup_size_bytes|Sì|Dimensioni di archiviazione del backup completo|Byte|Massimo|Dimensioni di archiviazione dei backup completi cumulativi. Si applica ai database basati su vCore. Non applicabile a database con iperscalabilità.|Nessuna dimensione|
|local_tempdb_usage_percent|Sì|Percentuale di tempdb locale|Percentuale|Media|Percentuale tempdb locale. Si applica solo ai data warehouse.|Nessuna dimensione|
|log_backup_size_bytes|Sì|Dimensioni di archiviazione dei backup del log|Byte|Massimo|Dimensioni di archiviazione dei backup del log cumulative. Si applica ai database basati su vCore e con iperscalabilità.|Nessuna dimensione|
|log_write_percent|Sì|Percentuale I/O registro|Percentuale|Media|Percentuale di i/o del log. Non applicabile ai data warehouse.|Nessuna dimensione|
|memory_usage_percent|Sì|Percentuale di memoria|Percentuale|Massimo|Percentuale di memoria. Si applica solo ai data warehouse.|Nessuna dimensione|
|physical_data_read_percent|Sì|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|queued_queries|Sì|Query in coda|Conteggio|Totale|Query in coda in tutti i gruppi del carico di lavoro. Si applica solo ai data warehouse.|Nessuna dimensione|
|sessions_percent|Sì|Percentuale sessioni|Percentuale|Media|Percentuale sessioni. Non applicabile ai data warehouse.|Nessuna dimensione|
|snapshot_backup_size_bytes|Sì|Dimensioni di archiviazione del backup di snapshot|Byte|Massimo|Dimensioni di archiviazione dei backup di snapshot cumulativi. Si applica ai database con iperscalabilità.|Nessuna dimensione|
|sqlserver_process_core_percent|Sì|Percentuale Core processo SQL Server|Percentuale|Massimo|Utilizzo della CPU come percentuale del processo del database SQL. Non applicabile ai data warehouse.|Nessuna dimensione|
|sqlserver_process_memory_percent|Sì|Percentuale memoria processo SQL Server|Percentuale|Massimo|Utilizzo della memoria come percentuale del processo del database SQL. Non applicabile ai data warehouse.|Nessuna dimensione|
|storage|Sì|Spazio dati usato|Byte|Massimo|Spazio dati utilizzato. Non applicabile ai data warehouse.|Nessuna dimensione|
|storage_percent|Sì|Data space used percent (Percentuale usata spazio dati)|Percentuale|Massimo|Percentuale di utilizzo dello spazio dei dati. Non applicabile a data warehouse o database con iperscalabilità.|Nessuna dimensione|
|tempdb_data_size|Sì|Tempdb Data File Size Kilobytes (Dimensioni file dati tempdb in kilobyte)|Conteggio|Massimo|Dimensioni del file di dati tempdb. Non applicabile ai data warehouse.|Nessuna dimensione|
|tempdb_log_size|Sì|Tempdb Log File Size Kilobytes (Dimensioni file di log tempdb in kilobyte)|Conteggio|Massimo|Kilobyte dimensioni file di log tempdb. Non applicabile ai data warehouse.|Nessuna dimensione|
|tempdb_log_used_percent|Sì|Tempdb Percent Log Used (Percentuale log utilizzata tempdb)|Percentuale|Massimo|Log percentuale tempdb utilizzato. Non applicabile ai data warehouse.|Nessuna dimensione|
|wlg_active_queries|Sì|Query attive del gruppo di carico di lavoro|Conteggio|Totale|Query attive all'interno del gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Sì|Timeout query del gruppo di carico di lavoro|Conteggio|Totale|Query che hanno raggiunto il timeout per il gruppo del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Sì|Allocazione del gruppo di carico di lavoro per percentuale di sistema|Percentuale|Massimo|Percentuale allocata di risorse rispetto all'intero sistema per gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Sì|Allocazione gruppo del carico di lavoro per percentuale risorse Cap|Percentuale|Massimo|Percentuale allocata di risorse rispetto alle risorse CAP specificate per gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Sì|Percentuale limite di risorse effettiva|Percentuale|Massimo|Limite rigido della percentuale di risorse consentite per il gruppo del carico di lavoro, prendendo in considerazione la percentuale di risorse minime effettiva allocata per altri gruppi del carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Sì|Percentuale risorse minima effettiva|Percentuale|Massimo|Percentuale minima di risorse riservate e isolate per il gruppo del carico di lavoro, prendendo in considerazione il valore minimo del livello di servizio. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Sì|Query accodate del gruppo di carico di lavoro|Conteggio|Totale|Query in coda all'interno del gruppo di carico di lavoro. Si applica solo ai data warehouse.|WorkloadGroupName, IsUserDefined|
|workers_percent|Sì|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro. Non applicabile ai data warehouse.|Nessuna dimensione|
|xtp_storage_percent|Sì|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP In-Memory. Non applicabile ai data warehouse.|Nessuna dimensione|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|allocated_data_storage|Sì|Spazio dati allocato|Byte|Media|Spazio dati allocato|Nessuna dimensione|
|allocated_data_storage_percent|Sì|Data space allocated percent (Percentuale spazio dati allocato)|Percentuale|Massimo|Data space allocated percent (Percentuale spazio dati allocato)|Nessuna dimensione|
|cpu_limit|Sì|Limite CPU|Conteggio|Media|Limite CPU. Si applica ai pool elastici basati su vCore.|Nessuna dimensione|
|cpu_percent|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Nessuna dimensione|
|cpu_used|Sì|CPU used (CPU usata)|Conteggio|Media|CPU utilizzata. Si applica ai pool elastici basati su vCore.|Nessuna dimensione|
|database_allocated_data_storage|No|Spazio dati allocato|Byte|Media|Spazio dati allocato|DatabaseResourceId|
|database_cpu_limit|No|Limite CPU|Conteggio|Media|Limite CPU|DatabaseResourceId|
|database_cpu_percent|No|Percentuale CPU|Percentuale|Media|Percentuale CPU|DatabaseResourceId|
|database_cpu_used|No|CPU used (CPU usata)|Conteggio|Media|CPU used (CPU usata)|DatabaseResourceId|
|database_dtu_consumption_percent|No|Percentuale di DTU|Percentuale|Media|Percentuale di DTU|DatabaseResourceId|
|database_eDTU_used|No|Uso eDTU|Conteggio|Media|Uso eDTU|DatabaseResourceId|
|database_log_write_percent|No|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|DatabaseResourceId|
|database_physical_data_read_percent|No|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|DatabaseResourceId|
|database_sessions_percent|No|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|DatabaseResourceId|
|database_storage_used|No|Spazio dati usato|Byte|Media|Spazio dati usato|DatabaseResourceId|
|database_workers_percent|No|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|DatabaseResourceId|
|dtu_consumption_percent|Sì|Percentuale di DTU|Percentuale|Media|Percentuale di DTU. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|eDTU_limit|Sì|Limite eDTU|Conteggio|Media|limite eDTU. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|eDTU_used|Sì|Uso eDTU|Conteggio|Media|eDTU usato. Si applica ai pool elastici basati su DTU.|Nessuna dimensione|
|log_write_percent|Sì|Percentuale I/O registro|Percentuale|Media|Percentuale I/O registro|Nessuna dimensione|
|physical_data_read_percent|Sì|Percentuale di I/O di dati|Percentuale|Media|Percentuale di I/O di dati|Nessuna dimensione|
|sessions_percent|Sì|Percentuale sessioni|Percentuale|Media|Percentuale sessioni|Nessuna dimensione|
|sqlserver_process_core_percent|Sì|Percentuale Core processo SQL Server|Percentuale|Massimo|Utilizzo della CPU come percentuale del processo del database SQL. Si applica ai pool elastici.|Nessuna dimensione|
|sqlserver_process_memory_percent|Sì|Percentuale memoria processo SQL Server|Percentuale|Massimo|Utilizzo della memoria come percentuale del processo del database SQL. Si applica ai pool elastici.|Nessuna dimensione|
|storage_limit|Sì|Dimensioni massime dati|Byte|Media|Dimensioni massime dei dati|Nessuna dimensione|
|storage_percent|Sì|Data space used percent (Percentuale usata spazio dati)|Percentuale|Media|Data space used percent (Percentuale usata spazio dati)|Nessuna dimensione|
|storage_used|Sì|Spazio dati usato|Byte|Media|Spazio dati usato|Nessuna dimensione|
|tempdb_data_size|Sì|Tempdb Data File Size Kilobytes (Dimensioni file dati tempdb in kilobyte)|Conteggio|Massimo|Tempdb Data File Size Kilobytes (Dimensioni file dati tempdb in kilobyte)|Nessuna dimensione|
|tempdb_log_size|Sì|Tempdb Log File Size Kilobytes (Dimensioni file di log tempdb in kilobyte)|Conteggio|Massimo|Tempdb Log File Size Kilobytes (Dimensioni file di log tempdb in kilobyte)|Nessuna dimensione|
|tempdb_log_used_percent|Sì|Tempdb Percent Log Used (Percentuale log utilizzata tempdb)|Percentuale|Massimo|Tempdb Percent Log Used (Percentuale log utilizzata tempdb)|Nessuna dimensione|
|workers_percent|Sì|Percentuale ruoli di lavoro|Percentuale|Media|Percentuale ruoli di lavoro|Nessuna dimensione|
|xtp_storage_percent|Sì|Percentuale di archiviazione OLTP in memoria|Percentuale|Media|Percentuale di archiviazione OLTP in memoria|Nessuna dimensione|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita. Questo numero include l'uscita per il client esterno dall'archiviazione di Azure e l'uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Capacità usata|Byte|Media|Quantità di risorse di archiviazione usata dall'account di archiviazione. Per gli account di archiviazione Standard, corrisponde alla somma della capacità usata da BLOB, tabelle, file e code. Per gli account di archiviazione Premium e gli account di archiviazione BLOB, è uguale a BlobCapacity o filecapacity.|Nessuna dimensione|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|BlobCapacity|No|Capacità BLOB|Byte|Media|Quantità di memoria usata dal servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|BlobCount|No|Numero di BLOB|Conteggio|Media|Numero di oggetti BLOB archiviati nell'account di archiviazione.|BlobType, livello|
|BlobProvisionedSize|No|Dimensioni del provisioning del BLOB|Byte|Media|Quantità di archiviazione di cui è stato effettuato il provisioning nel servizio BLOB dell'account di archiviazione, in byte.|BlobType, livello|
|ContainerCount|Sì|Numero di contenitori BLOB|Conteggio|Media|Numero di contenitori nell'account di archiviazione.|Nessuna dimensione|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita. Questo numero include l'uscita per il client esterno dall'archiviazione di Azure e l'uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|IndexCapacity|No|Capacità indice|Byte|Media|Quantità di spazio di archiviazione utilizzata da Azure Data Lake Storage Gen2 Indice gerarchico.|Nessuna dimensione|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|Geotype, ApiName, autenticazione, FileShare|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita. Questo numero include l'uscita per il client esterno dall'archiviazione di Azure e l'uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|Geotype, ApiName, autenticazione, FileShare|
|FileCapacity|No|Capacità file|Byte|Media|Quantità di archiviazione file usata dall'account di archiviazione.|FileShare|
|FileCount|No|Numero di file|Conteggio|Media|Numero di file nell'account di archiviazione.|FileShare|
|FileShareCapacityQuota|No|Quota di capacità della condivisione file|Byte|Media|Limite massimo per la quantità di spazio di archiviazione che può essere utilizzato dal servizio File di Azure in byte.|FileShare|
|FileShareCount|No|Numero di condivisione file|Conteggio|Media|Numero di condivisioni file nell'account di archiviazione.|Nessuna dimensione|
|FileShareProvisionedIOPS|No|IOPS con provisioning di condivisione file|Byte|Media|Il numero di operazioni di i/o al secondo di cui è stato effettuato il provisioning per la condivisione file Premium nell'account di archiviazione file Premium. Questo numero viene calcolato in base alla dimensione di provisioning (quota) della capacità di condivisione.|FileShare|
|FileShareSnapshotCount|No|Numero di snapshot in condivisione file|Conteggio|Media|Il numero di snapshot presenti nella condivisione nel servizio file dell'account di archiviazione.|FileShare|
|FileShareSnapshotSize|No|Dimensioni snapshot in condivisione file|Byte|Media|Quantità di spazio di archiviazione usata dagli snapshot nel servizio file dell'account di archiviazione in byte.|FileShare|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|Geotype, ApiName, autenticazione, FileShare|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|Geotype, ApiName, autenticazione, FileShare|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|Geotype, ApiName, autenticazione, FileShare|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, Geotype, ApiName, autenticazione, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita. Questo numero include l'uscita per il client esterno dall'archiviazione di Azure e l'uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Sì|Capacità coda|Byte|Media|Quantità di archiviazione code usata dall'account di archiviazione.|Nessuna dimensione|
|QueueCount|Sì|Numero di coda|Conteggio|Media|Numero di code nell'account di archiviazione.|Nessuna dimensione|
|QueueMessageCount|Sì|Numero di messaggi in coda|Conteggio|Media|Numero di messaggi non scaduti nella coda nell'account di archiviazione.|Nessuna dimensione|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Disponibilità|Sì|Disponibilità|Percentuale|Media|Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore TotalBillableRequests e dividendolo per il numero di richieste applicabili, incluse quelle che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata.|GeoType, ApiName, Authentication|
|Egress|Sì|Egress|Byte|Totale|Quantità di dati in uscita. Questo numero include l'uscita per il client esterno dall'archiviazione di Azure e l'uscita in Azure. Questo numero non rispecchia quindi dati in uscita fatturabili.|GeoType, ApiName, Authentication|
|Dati in ingresso|Sì|Dati in ingresso|Byte|Totale|Quantità di dati in ingresso, in byte. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sì|Latenza end-to-end per richieste con esito positivo|Millisecondi|Media|Latenza end-to-end media di richieste con esito positivo eseguite in un servizio di archiviazione o nell'operazione API specificata, in millisecondi. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sì|Latenza server per richieste con esito positivo|Millisecondi|Media|Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Sì|Capacità tabella|Byte|Media|Quantità di archiviazione tabelle usata dall'account di archiviazione.|Nessuna dimensione|
|TableCount|Sì|Numero di tabella|Conteggio|Media|Numero di tabelle nell'account di archiviazione.|Nessuna dimensione|
|TableEntityCount|Sì|Numero di entità di tabella|Conteggio|Media|Numero di entità tabella nell'account di archiviazione.|Nessuna dimensione|
|Transazioni|Sì|Transazioni|Conteggio|Totale|Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. Usare la dimensione ResponseType per il numero di tipi di risposta diversi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Sì|Sync Session Result (Risultato sessione di sincronizzazione)|Conteggio|Media|Metrica che registra il valore 1 ogni volta che l'endpoint del server completa correttamente una sessione di sincronizzazione con l'endpoint cloud|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Sì|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Sì|Cloud tiering recall size by application (Dimensioni richiamo cloud a livelli per applicazione)|Byte|Totale|Dimensioni dei dati richiamati dall'applicazione|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Sì|Cloud tiering recall size (Dimensioni richiamo cloud a livelli)|Byte|Totale|Dimensione dei dati richiamati|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Sì|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Sì|Cloud tiering recall throughput (Velocità effettiva richiamo cloud a livelli)|Byte al secondo|Media|Dimensioni della velocità effettiva di richiamo dei dati|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Sì|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Sì|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Sì|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Sì|Stato online del server|Conteggio|Massimo|Metrica che registra il valore 1 ogni volta che il server resigtered registra correttamente un heartbeat con l'endpoint cloud|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Sì|Richiamo cloud a livelli|Byte|Totale|Dimensioni totali dei dati richiamati dal server|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Sì|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Sì|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Sì|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Sì|Byte sincronizzati|Byte|Totale|Dimensioni totali del file trasferiti per le sessioni di sincronizzazione|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Sì|File sincronizzati|Conteggio|Totale|Conteggio dei file sincronizzati|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Sì|File non sincronizzati|Conteggio|Totale|Il numero di file non è stato sincronizzato|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Sì|Richieste di funzioni non riuscite|Conteggio|Totale|Richieste di funzioni non riuscite|LogicalName, PartitionId|
|AMLCalloutInputEvents|Sì|Eventi di funzioni|Conteggio|Totale|Eventi di funzioni|LogicalName, PartitionId|
|AMLCalloutRequests|Sì|Richieste di funzioni|Conteggio|Totale|Richieste di funzioni|LogicalName, PartitionId|
|ConversionErrors|Sì|Errori di conversione dati|Conteggio|Totale|Errori di conversione dati|LogicalName, PartitionId|
|DeserializationError|Sì|Errori di deserializzazione dell'input|Conteggio|Totale|Errori di deserializzazione dell'input|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Sì|Eventi non in ordine|Conteggio|Totale|Eventi non in ordine|LogicalName, PartitionId|
|EarlyInputEvents|Sì|Eventi di input anticipati|Conteggio|Totale|Eventi di input anticipati|LogicalName, PartitionId|
|Errors|Sì|Errori di runtime|Conteggio|Totale|Errori di runtime|LogicalName, PartitionId|
|InputEventBytes|Sì|Byte evento di input|Byte|Totale|Byte evento di input|LogicalName, PartitionId|
|InputEvents|Sì|Eventi di input|Conteggio|Totale|Eventi di input|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Sì|Eventi di input con backlog|Conteggio|Massimo|Eventi di input con backlog|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Sì|Origini di input ricevute|Conteggio|Totale|Origini di input ricevute|LogicalName, PartitionId|
|LateInputEvents|Sì|Ultimi eventi di input|Conteggio|Totale|Ultimi eventi di input|LogicalName, PartitionId|
|OutputEvents|Sì|Eventi di output|Conteggio|Totale|Eventi di output|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Sì|Ritardo limite|Secondi|Massimo|Ritardo limite|LogicalName, PartitionId|
|ResourceUtilization|Sì|% utilizzo unità di streaming|Percentuale|Massimo|% utilizzo unità di streaming|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|No|Esecuzioni attività terminate|Conteggio|Totale|Numero di attività di orchestrazione con esito positivo, negativo o annullato|Risultato, FailureType, attività, ActivityType, pipeline|
|OrchestrationPipelineRunsEnded|No|Esecuzioni di pipeline terminate|Conteggio|Totale|Numero di esecuzioni di pipeline di orchestrazione con esito positivo, negativo o annullato|Risultato, FailureType, pipeline|
|OrchestrationTriggersEnded|No|Trigger terminati|Conteggio|Totale|Numero di trigger di orchestrazione riusciti, non riusciti o annullati|Risultato, FailureType, trigger|
|SQLOnDemandLoginAttempts|No|Tentativi di accesso|Conteggio|Totale|Numero di tentativi di accesso riuscito o non riusciti|Risultato|
|SQLOnDemandQueriesEnded|No|Query terminate|Conteggio|Totale|Numero di query che hanno avuto esito positivo, negativo o sono state annullate|Risultato|
|SQLOnDemandQueryProcessedBytes|No|Dati elaborati|Byte|Totale|Quantità di dati elaborati dalle query|Nessuna dimensione|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. sinapsi/Workspaces/bigDataPools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CoresCapacity|No|Capacità core|Conteggio|Massimo|Capacità core|Nessuna dimensione|
|MemoryCapacityGB|No|Capacità memoria (GB)|Conteggio|Massimo|Capacità memoria (GB)|Nessuna dimensione|
|SparkJobsEnded|Sì|Applicazioni terminate|Conteggio|Totale|Numero di applicazioni terminate|Tipoprocesso, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. sinapsi/Workspaces/sqlpools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|No|Percentuale riscontri cache adattiva|Percentuale|Massimo|Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale di riscontri nella cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache.|Nessuna dimensione|
|AdaptiveCacheUsedPercent|No|Percentuale utilizzata della cache adattiva|Percentuale|Massimo|Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale utilizzata della cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache.|Nessuna dimensione|
|Connessioni|Sì|Connessioni|Conteggio|Totale|Numero totale di accessi al pool SQL|Risultato|
|ConnectionsBlockedByFirewall|No|Connessioni bloccate dal firewall|Conteggio|Totale|Numero di connessioni bloccate dalle regole del firewall. Rivedere i criteri di controllo di accesso per il pool SQL e monitorare queste connessioni se il conteggio è elevato|Nessuna dimensione|
|DWULimit|No|Limite DWU|Conteggio|Massimo|Obiettivo del livello di servizio del pool SQL|Nessuna dimensione|
|DWUUsed|No|Uso DWU|Conteggio|Massimo|Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato per limite DWU * DWU%|Nessuna dimensione|
|DWUUsedPercent|No|Percentuale di utilizzo di DWU|Percentuale|Massimo|Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato prendendo il massimo tra la percentuale di CPU e la percentuale di i/o dei dati|Nessuna dimensione|
|LocalTempDBUsedPercent|No|Percentuale di utilizzo del tempdb locale|Percentuale|Massimo|Utilizzo tempdb locale in tutti i nodi di calcolo: i valori vengono emessi ogni cinque minuti|Nessuna dimensione|
|MemoryUsedPercent|No|Percentuale di memoria utilizzata|Percentuale|Massimo|Utilizzo della memoria in tutti i nodi del pool SQL|Nessuna dimensione|
|wlg_effective_min_resource_percent|Sì|Percentuale risorse minima effettiva|Percentuale|Minimo|L'impostazione percentuale effettiva minima delle risorse può prendere in considerazione il livello di servizio e le impostazioni del gruppo di carico di lavoro. Il min_percentage_resource effettivo può essere modificato in un livello superiore rispetto ai livelli di servizio più bassi|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|No|Query attive del gruppo di carico di lavoro|Conteggio|Totale|Query attive all'interno del gruppo di carico di lavoro. Utilizzando questa metrica non filtrata e unsplit, vengono visualizzate tutte le query attive in esecuzione nel sistema|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Timeout query del gruppo di carico di lavoro|Conteggio|Totale|Query per il gruppo del carico di lavoro di cui si è verificato il timeout. I timeout delle query segnalati da questa metrica sono solo dopo l'avvio dell'esecuzione della query (non è incluso il tempo di attesa a causa del blocco o delle attese delle risorse)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|No|Allocazione del gruppo di carico di lavoro per percentuale risorse massima|Percentuale|Massimo|Visualizza la percentuale di allocazione delle risorse rispetto alla percentuale effettiva delle risorse CAP per gruppo di carico di lavoro. Questa metrica fornisce l'utilizzo effettivo del gruppo di carico di lavoro|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Allocazione del gruppo di carico di lavoro per percentuale di sistema|Percentuale|Massimo|Percentuale di allocazione delle risorse rispetto all'intero sistema|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Sì|Percentuale limite di risorse effettiva|Percentuale|Massimo|Percentuale effettiva della risorsa Cap per il gruppo del carico di lavoro. Se sono presenti altri gruppi di carico di lavoro con min_percentage_resource > 0, il effective_cap_percentage_resource viene ridotto proporzionalmente|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Query accodate del gruppo di carico di lavoro|Conteggio|Totale|Conteggio cumulativo delle richieste accodate dopo il raggiungimento del limite massimo di concorrenza|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sì|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti da tutte le origini eventi|Nessuna dimensione|
|IngressReceivedInvalidMessages|Sì|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi non validi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedMessages|Sì|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero dei messaggi letti da tutte le origini eventi di hub eventi o hub IoT|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Sì|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Sì|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressStoredBytes|Sì|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Sì|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|WarmStorageMaxProperties|Sì|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuna dimensione|
|WarmStorageUsedProperties|Sì|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuna dimensione|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sì|Ingress Received Bytes (Byte ricevuti in ingresso)|Byte|Totale|Numero di byte letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedInvalidMessages|Sì|Ingress Received Invalid Messages (Messaggi non validi ricevuti in ingresso)|Conteggio|Totale|Numero di messaggi non validi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedMessages|Sì|Ingress Received Messages (Messaggi ricevuti in ingresso)|Conteggio|Totale|Numero messaggi letti dall'origine eventi|Nessuna dimensione|
|IngressReceivedMessagesCountLag|Sì|Ingress Received Messages Count Lag (Ritardo numero di messaggi ricevuti in ingresso)|Conteggio|Media|Differenza tra il numero di sequenza dell'ultimo messaggio accodato nella partizione di origine eventi e il numero di sequenza dei messaggi elaborati in ingresso|Nessuna dimensione|
|IngressReceivedMessagesTimeLag|Sì|Ingress Received Messages Time Lag (Tempo di ritardo messaggi ricevuti in ingresso)|Secondi|Massimo|Differenza tra l'ora in cui il messaggio viene accodato nell'origine eventi e l'ora di elaborazione in ingresso.|Nessuna dimensione|
|IngressStoredBytes|Sì|Ingress Stored Bytes (Byte archiviati in ingresso)|Byte|Totale|Dimensioni totali degli eventi elaborati correttamente e disponibili per le query|Nessuna dimensione|
|IngressStoredEvents|Sì|Ingress Stored Events (Eventi archiviati in ingresso)|Conteggio|Totale|Numero degli eventi flat elaborati correttamente e disponibili per le query|Nessuna dimensione|
|WarmStorageMaxProperties|Sì|Proprietà Max archiviazione a caldo|Conteggio|Massimo|Numero massimo di proprietà usate consentite dall'ambiente per lo SKU S1/S2 e numero massimo di proprietà consentite da warm Store per lo SKU PAYG|Nessuna dimensione|
|WarmStorageUsedProperties|Sì|Proprietà utilizzo memoria calda |Conteggio|Massimo|Numero di proprietà usate dall'ambiente per lo SKU S1/S2 e il numero di proprietà usate da warm Store per lo SKU PAYG|Nessuna dimensione|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|Byte letti da disco|Sì|Byte letti da disco|Byte|Totale|Velocità effettiva totale del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuna dimensione|
|Operazioni lettura disco/sec|Sì|Operazioni lettura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|Byte scritti su disco|Sì|Byte scritti su disco|Byte|Totale|Velocità effettiva totale del disco dovuta alle operazioni di scrittura nel periodo di campionamento.|Nessuna dimensione|
|Operazioni scrittura disco/sec|Sì|Operazioni scrittura disco/sec|Conteggio al secondo|Media|Numero medio di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskReadBytesPerSecond|Sì|Disk Read Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di lettura nel periodo di campionamento.|Nessuna dimensione|
|DiskReadLatency|Sì|Latenza di lettura da disco|Millisecondi|Media|Latenza di lettura totale. Somma delle latenze di lettura del dispositivo e del kernel.|Nessuna dimensione|
|DiskReadOperations|Sì|Disk Read Operations (Operazioni di lettura da disco)|Conteggio|Totale|Numero di operazioni di i/o lette nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|DiskWriteBytesPerSecond|Sì|Disk Write Bytes/Sec|Byte al secondo|Media|Velocità effettiva media del disco dovuta a operazioni di scrittura nel periodo di campionamento.|Nessuna dimensione|
|DiskWriteLatency|Sì|Latenza di scrittura su disco|Millisecondi|Media|Latenza di scrittura totale. Somma delle latenze di scrittura del dispositivo e del kernel.|Nessuna dimensione|
|DiskWriteOperations|Sì|Disk Write Operations (Operazioni di scrittura su disco)|Conteggio|Totale|Numero di operazioni di scrittura i/o nel periodo di esempio precedente. Si noti che queste operazioni possono essere di dimensioni variabili.|Nessuna dimensione|
|MemoryActive|Sì|Memory Active (Memoria attiva)|Byte|Media|Quantità di memoria usata dalla macchina virtuale in un intervallo di tempo ridotto. Questo è il numero "vero" della quantità di memoria attualmente necessaria per la macchina virtuale. È possibile che venga scambiata una memoria aggiuntiva inutilizzata o che non abbia alcun effetto sulle prestazioni del Guest.|Nessuna dimensione|
|MemoryGranted|Sì|Memory Granted (Memoria concessa)|Byte|Media|Quantità di memoria concessa alla macchina virtuale dall'host. La memoria non viene concessa all'host fino a quando non viene toccata una sola volta e la memoria concessa potrebbe essere invertita o rimossa se il VMkernel richiede la memoria.|Nessuna dimensione|
|MemoryUsed|Sì|Memory Used (Memoria usata)|Byte|Media|Quantità di memoria del computer utilizzata dalla VM.|Nessuna dimensione|
|Rete in ingresso|Sì|Rete in ingresso|Byte|Totale|Velocità effettiva totale della rete per il traffico ricevuto.|Nessuna dimensione|
|Rete in uscita|Sì|Rete in uscita|Byte|Totale|Velocità effettiva totale della rete per il traffico trasmesso.|Nessuna dimensione|
|NetworkInBytesPerSecond|Sì|Network In Bytes/Sec (Rete in ingresso in byte/sec)|Byte al secondo|Media|Velocità effettiva media della rete per il traffico ricevuto.|Nessuna dimensione|
|NetworkOutBytesPerSecond|Sì|Network Out Bytes/Sec (Rete in uscita in byte/sec)|Byte al secondo|Media|Velocità effettiva media della rete per il traffico trasmesso.|Nessuna dimensione|
|Percentuale CPU|Sì|CPU percentuale|Percentuale|Media|Utilizzo della CPU. Questo valore viene segnalato con 100% che rappresenta tutti i core del processore nel sistema. Ad esempio, una macchina virtuale a 2 vie che usa il 50% di un sistema a quattro core USA completamente due core.|Nessuna dimensione|
|PercentageCpuReady|Sì|Percentage CPU Ready (Percentuale di disponibilità CPU)|Millisecondi|Totale|Il tempo pronto è il tempo di attesa per la disponibilità di CPU nell'intervallo di aggiornamento precedente.|Nessuna dimensione|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|ActiveRequests|Sì|Richieste attive|Conteggio|Totale|Richieste attive|Istanza|
|AverageResponseTime|Sì|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|BytesReceived|Sì|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Sì|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|CpuPercentage|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|DiskQueueLength|Sì|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|Http101|Sì|Http 101|Conteggio|Totale|Http 101|Istanza|
|Http2xx|Sì|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Sì|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Sì|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Sì|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Sì|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Sì|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Sì|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Sì|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|HttpQueueLength|Sì|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|LargeAppServicePlanInstances|Sì|Ruoli di lavoro piano di servizio app Large|Conteggio|Media|Ruoli di lavoro piano di servizio app Large|Nessuna dimensione|
|MediumAppServicePlanInstances|Sì|Ruoli di lavoro piano di servizio app Medium|Conteggio|Media|Ruoli di lavoro piano di servizio app Medium|Nessuna dimensione|
|MemoryPercentage|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|Requests|Sì|Requests|Conteggio|Totale|Requests|Istanza|
|SmallAppServicePlanInstances|Sì|Ruoli di lavoro piano di servizio app Small|Conteggio|Media|Ruoli di lavoro piano di servizio app Small|Nessuna dimensione|
|TotalFrontEnds|Sì|Front end totali|Conteggio|Media|Front end totali|Nessuna dimensione|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|CpuPercentage|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|MemoryPercentage|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|WorkersAvailable|Sì|Ruoli di lavoro disponibili|Conteggio|Media|Ruoli di lavoro disponibili|Nessuna dimensione|
|WorkersTotal|Sì|Ruoli di lavoro totali|Conteggio|Media|Ruoli di lavoro totali|Nessuna dimensione|
|WorkersUsed|Sì|Ruoli di lavoro usati|Conteggio|Media|Ruoli di lavoro usati|Nessuna dimensione|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|BytesReceived|Sì|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Sì|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|CpuPercentage|Sì|Percentuale CPU|Percentuale|Media|Percentuale CPU|Istanza|
|DiskQueueLength|Sì|Lunghezza coda disco|Conteggio|Media|Lunghezza coda disco|Istanza|
|HttpQueueLength|Sì|Lunghezza coda HTTP|Conteggio|Media|Lunghezza coda HTTP|Istanza|
|MemoryPercentage|Sì|Percentuale memoria|Percentuale|Media|Percentuale memoria|Istanza|
|SocketInboundAll|Sì|SocketInboundAll|Conteggio|Media|SocketInboundAll|Istanza|
|SocketLoopback|Sì|SocketLoopback|Conteggio|Media|SocketLoopback|Istanza|
|SocketOutboundAll|Sì|SocketOutboundAll|Conteggio|Media|SocketOutboundAll|Istanza|
|SocketOutboundEstablished|Sì|SocketOutboundEstablished|Conteggio|Media|SocketOutboundEstablished|Istanza|
|SocketOutboundTimeWait|Sì|SocketOutboundTimeWait|Conteggio|Media|SocketOutboundTimeWait|Istanza|
|TcpCloseWait|Sì|TCP Close Wait|Conteggio|Media|TCP Close Wait|Istanza|
|TcpClosing|Sì|TCP Closing|Conteggio|Media|TCP Closing|Istanza|
|TcpEstablished|Sì|TCP Established|Conteggio|Media|TCP Established|Istanza|
|TcpFinWait1|Sì|TCP Fin Wait 1|Conteggio|Media|TCP Fin Wait 1|Istanza|
|TcpFinWait2|Sì|TCP Fin Wait 2|Conteggio|Media|TCP Fin Wait 2|Istanza|
|TcpLastAck|Sì|TCP Last Ack|Conteggio|Media|TCP Last Ack|Istanza|
|TcpSynReceived|Sì|TCP Syn Received|Conteggio|Media|TCP Syn Received|Istanza|
|TcpSynSent|Sì|TCP Syn Sent|Conteggio|Media|TCP Syn Sent|Istanza|
|TcpTimeWait|Sì|TCP Time Wait|Conteggio|Media|TCP Time Wait|Istanza|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (escluse le funzioni) 

> [!NOTE]
> L' **utilizzo del file System** è una nuova metrica distribuita a livello globale, non è previsto alcun dato, a meno che non sia stata inserita nell'elenco elementi consentiti per l'anteprima privata.

> [!IMPORTANT]
> Il **tempo di risposta medio** sarà deprecato per evitare confusione con le aggregazioni di metriche. Usare il **tempo di risposta** come sostituzione.

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AppConnections|Sì|Connessioni|Conteggio|Media|Connessioni|Istanza|
|AverageMemoryWorkingSet|Sì|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Sì|Tempo medio di risposta **(obsoleto)**|Secondi|Media|Tempo medio di risposta|Istanza|
|BytesReceived|Sì|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Sì|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|CpuTime|Sì|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|CurrentAssemblies|Sì|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|FileSystemUsage|Sì|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuna dimensione|
|Gen0Collections|Sì|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Sì|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Sì|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|Selettori|Sì|Numero di handle|Conteggio|Media|Numero di handle|Istanza|
|HealthCheckStatus|Sì|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|Http101|Sì|Http 101|Conteggio|Totale|Http 101|Istanza|
|Http2xx|Sì|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Sì|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Sì|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Sì|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Sì|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Sì|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Sì|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|HttpResponseTime|Sì|Tempo di risposta|Secondi|Media|Tempo di risposta|Istanza|
|IoOtherBytesPerSecond|Sì|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoOtherOperationsPerSecond|Sì|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|IoReadBytesPerSecond|Sì|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoReadOperationsPerSecond|Sì|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteBytesPerSecond|Sì|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoWriteOperationsPerSecond|Sì|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|MemoryWorkingSet|Sì|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|PrivateBytes|Sì|Private Bytes|Byte|Media|Private Bytes|Istanza|
|Requests|Sì|Requests|Conteggio|Totale|Requests|Istanza|
|RequestsInApplicationQueue|Sì|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|Threads|Sì|Thread Count|Conteggio|Media|Thread Count|Istanza|
|TotalAppDomains|Sì|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Sì|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funzioni)

> [!NOTE]
> L' **utilizzo del file System** è una nuova metrica distribuita a livello globale, non è previsto alcun dato, a meno che non sia stata inserita nell'elenco elementi consentiti per l'anteprima privata.

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Sì|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|BytesReceived|Sì|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Sì|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|CurrentAssemblies|Sì|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|FileSystemUsage|Sì|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuna dimensione|
|FunctionExecutionCount|Sì|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|FunctionExecutionUnits|Sì|Unità di esecuzione della funzione|Conteggio|Totale|[Unità di esecuzione della funzione](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Istanza|
|Gen0Collections|Sì|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Sì|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Sì|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|HealthCheckStatus|Sì|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|Http5xx|Sì|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|IoOtherBytesPerSecond|Sì|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoOtherOperationsPerSecond|Sì|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|IoReadBytesPerSecond|Sì|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoReadOperationsPerSecond|Sì|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteBytesPerSecond|Sì|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoWriteOperationsPerSecond|Sì|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|MemoryWorkingSet|Sì|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|PrivateBytes|Sì|Private Bytes|Byte|Media|Private Bytes|Istanza|
|RequestsInApplicationQueue|Sì|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|TotalAppDomains|Sì|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Sì|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metrica|Esportabile tramite Impostazioni di diagnostica?|Nome visualizzato per la metrica|Unità|Tipo di aggregazione|Description|Dimensioni|
|---|---|---|---|---|---|---|
|AppConnections|Sì|Connessioni|Conteggio|Media|Connessioni|Istanza|
|AverageMemoryWorkingSet|Sì|Working set della memoria medio|Byte|Media|Working set della memoria medio|Istanza|
|AverageResponseTime|Sì|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|Istanza|
|BytesReceived|Sì|Dati in entrata|Byte|Totale|Dati in entrata|Istanza|
|BytesSent|Sì|Dati in uscita|Byte|Totale|Dati in uscita|Istanza|
|CpuTime|Sì|Tempo CPU|Secondi|Totale|Tempo CPU|Istanza|
|CurrentAssemblies|Sì|Assembly attuali|Conteggio|Media|Assembly attuali|Istanza|
|FileSystemUsage|Sì|Utilizzo del file System|Byte|Media|Utilizzo del file System|Nessuna dimensione|
|FunctionExecutionCount|Sì|Conteggio delle esecuzioni della funzione|Conteggio|Totale|Conteggio delle esecuzioni della funzione|Istanza|
|FunctionExecutionUnits|Sì|Unità di esecuzione della funzione|Conteggio|Totale|Unità di esecuzione della funzione|Istanza|
|Gen0Collections|Sì|Garbage Collection di generazione 0|Conteggio|Totale|Garbage Collection di generazione 0|Istanza|
|Gen1Collections|Sì|Garbage Collection di generazione 1|Conteggio|Totale|Garbage Collection di generazione 1|Istanza|
|Gen2Collections|Sì|Garbage Collection di generazione 2|Conteggio|Totale|Garbage Collection di generazione 2|Istanza|
|Selettori|Sì|Numero di handle|Conteggio|Media|Numero di handle|Istanza|
|HealthCheckStatus|Sì|Stato del controllo integrità|Conteggio|Media|Stato del controllo integrità|Istanza|
|Http101|Sì|Http 101|Conteggio|Totale|Http 101|Istanza|
|Http2xx|Sì|Http 2xx|Conteggio|Totale|Http 2xx|Istanza|
|Http3xx|Sì|Http 3xx|Conteggio|Totale|Http 3xx|Istanza|
|Http401|Sì|Http 401|Conteggio|Totale|Http 401|Istanza|
|Http403|Sì|Http 403|Conteggio|Totale|Http 403|Istanza|
|Http404|Sì|Http 404|Conteggio|Totale|Http 404|Istanza|
|Http406|Sì|Http 406|Conteggio|Totale|Http 406|Istanza|
|Http4xx|Sì|Http 4xx|Conteggio|Totale|Http 4xx|Istanza|
|Http5xx|Sì|Errori server HTTP|Conteggio|Totale|Errori server HTTP|Istanza|
|HttpResponseTime|Sì|Tempo di risposta|Secondi|Media|Tempo di risposta|Istanza|
|IoOtherBytesPerSecond|Sì|I/O - Altri byte al secondo|Byte al secondo|Totale|I/O - Altri byte al secondo|Istanza|
|IoOtherOperationsPerSecond|Sì|I/O - Altre operazioni al secondo|Byte al secondo|Totale|I/O - Altre operazioni al secondo|Istanza|
|IoReadBytesPerSecond|Sì|I/O - Byte in lettura al secondo|Byte al secondo|Totale|I/O - Byte in lettura al secondo|Istanza|
|IoReadOperationsPerSecond|Sì|I/O - Operazioni di lettura al secondo|Byte al secondo|Totale|I/O - Operazioni di lettura al secondo|Istanza|
|IoWriteBytesPerSecond|Sì|I/O - Byte in scrittura al secondo|Byte al secondo|Totale|I/O - Byte in scrittura al secondo|Istanza|
|IoWriteOperationsPerSecond|Sì|I/O - Operazioni di scrittura al secondo|Byte al secondo|Totale|I/O - Operazioni di scrittura al secondo|Istanza|
|MemoryWorkingSet|Sì|Working set della memoria|Byte|Media|Working set della memoria|Istanza|
|PrivateBytes|Sì|Private Bytes|Byte|Media|Private Bytes|Istanza|
|Requests|Sì|Requests|Conteggio|Totale|Requests|Istanza|
|RequestsInApplicationQueue|Sì|Richieste nella coda dell'applicazione|Conteggio|Media|Richieste nella coda dell'applicazione|Istanza|
|Threads|Sì|Thread Count|Conteggio|Media|Thread Count|Istanza|
|TotalAppDomains|Sì|Totale di domini app|Conteggio|Media|Totale di domini app|Istanza|
|TotalAppDomainsUnloaded|Sì|Totale di domini app scaricati|Conteggio|Media|Totale di domini app scaricati|Istanza|


## <a name="next-steps"></a>Passaggi successivi
* [Metriche in Monitoraggio di Azure](data-platform.md)
* [Creare avvisi relativi alle metriche](alerts-overview.md)
* [Esportazione delle metriche nell'archiviazione, nell'hub eventi o in Log Analytics](platform-logs-overview.md)
