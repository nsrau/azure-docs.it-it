---
title: Comportamento di esportazione delle metriche con valori NULL e zero
description: Discussione sui valori NULL e zero quando si esportano le metriche e un puntatore a un elenco di metriche non esportabili.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: ca6acb97e52123a0663d988b3f217d305bce2c4b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131685"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metriche di piattaforma di Monitoraggio di Azure esportabili tramite Impostazioni di diagnostica

Monitoraggio di Azure offre [metriche di piattaforma](data-platform-metrics.md) per impostazione predefinita senza configurazione. Sono disponibili diversi modi per interagire con le metriche di piattaforma, tra cui la creazione di grafici nel portale, l'accesso tramite l'API REST o l'esecuzione di query tramite PowerShell o l'interfaccia della riga di comando. Per un elenco completo delle metriche di piattaforma attualmente disponibili con la pipeline delle metriche consolidate di Monitoraggio di Azure, vedere la pagina relativa alle [metriche supportate](metrics-supported.md). Per richiedere queste metriche e accedervi, usare la [versione API 2018-01-01](/rest/api/monitor/metricdefinitions). Altre metriche potrebbero essere disponibili nel portale o tramite le API legacy.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Metriche non esportabili tramite le impostazioni di diagnostica

Il contenuto che si trovava in questa posizione è stato spostato nell' [elenco supportato di metriche di monitoraggio di Azure](metrics-supported.md#exporting-platform-metrics-to-other-locations).

Esistono alcune limitazioni durante l'esportazione delle metriche tramite le impostazioni di diagnostica. Tutte le metriche sono esportabili tramite l'API REST. 

## <a name="exported-zero-vs-null-values"></a>Valori zero e NULL esportati 

Le metriche hanno un comportamento diverso quando si gestiscono valori 0 o NULL.  Alcune metriche segnalano 0 quando non vengono ottenuti dati, ad esempio le metriche sugli errori http. Se non si ottengono dati, altre metriche archiviano valori NULL in quanto può indicare che la risorsa è offline. È possibile visualizzare la differenza quando si crea un grafico di queste metriche con valori NULL visualizzati come [linee tratteggiate](metrics-troubleshoot.md#chart-shows-dashed-line). 

Quando le metriche della piattaforma possono essere esportate tramite le impostazioni di diagnostica, corrispondono al comportamento della metrica. Ovvero esportano i valori NULL quando la risorsa non invia dati.  Esportano ' 0' solo quando sono stati effettivamente emessi dalla risorsa sottostante. 

Se si elimina un gruppo di risorse o una risorsa specifica, i dati delle metriche provenienti dalle risorse interessate non vengono più inviati alle destinazioni di esportazione delle impostazioni di diagnostica. Ovvero non viene più visualizzato negli hub eventi, negli account di archiviazione di Azure e nelle aree di lavoro Log Analytics.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Metriche usate per esportare zero per NULL

Prima del 1 ° giugno 2020, le metriche seguenti **usavano per** emettere ' 0' in assenza di dati. Questi zeri potrebbero quindi essere esportati in sistemi downstream come Log Analytics e archiviazione di Azure.  Questo comportamento ha causato una certa confusione tra' 0' reale (emesso dalla risorsa) è 0' interpretato (NULL), quindi il comportamento è stato modificato in modo da corrispondere a quello della metrica sottostante come indicato nella sezione precedente. 

La modifica si è verificata in tutti i cloud pubblici e privati.

La modifica non ha influito sul comportamento di una delle seguenti esperienze: 
   - Log delle risorse della piattaforma esportati tramite impostazioni di diagnostica
   - Creazione di grafici delle metriche in Esplora metriche
   - Avvisi sulle metriche di piattaforma
 
Di seguito è riportato un elenco delle metriche il cui comportamento è stato modificato. 

| ResourceType                    | Metrica               |  Nome visualizzato metrica  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Unauthorized Gateway Requests (Richieste del gateway non autorizzate) (deprecato)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Total Gateway Requests (Totale richieste gateway) (deprecato)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Successful Gateway Requests (Richieste gateway riuscite) (deprecato)  | 
| Microsoft.ApiManagement/service | Requests |  Requests  | 
| Microsoft.ApiManagement/service | OtherRequests |  Other Gateway Requests (Altre richieste del gateway) (deprecato)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Failed Gateway Requests (Richieste gateway non riuscite) (deprecato)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Failed EventHub Events (Eventi EventHub non riusciti)  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Total EventHub Events (Eventi EventHub totali)  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Size of EventHub Events (Dimensione degli eventi EventHub)  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Timed Out EventHub Events (Timeout eventi EventHub)  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Throttled EventHub Events (Eventi EventHub limitati)  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Successful EventHub Events (Eventi EventHub riusciti)  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Rejected EventHub Events (Eventi EventHub rifiutati)  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Dropped EventHub Events (Eventi EventHub rimossi)  | 
| Microsoft.ApiManagement/service | Duration |  Durata complessiva delle richieste del gateway  | 
| Microsoft.ApiManagement/service | BackendDuration |  Duration of Backend Requests (Durata delle richieste back-end)  | 
| Microsoft.DBforMariaDB/servers | storage_used |  Uso archiviazione  | 
| Microsoft.DBforMariaDB/servers | storage_percent |  Percentuale di archiviazione  | 
| Microsoft.DBforMariaDB/servers | storage_limit |  Limite archiviazione  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_usage |  Archiviazione dei log del server usata  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_percent |  Percentuale di archiviazione dei log del server  | 
| Microsoft.DBforMariaDB/servers | serverlog_storage_limit |  Limite di archiviazione dei log del server  | 
| Microsoft.DBforMariaDB/servers | seconds_behind_master |  Intervallo di replica in secondi  | 
| Microsoft.DBforMariaDB/servers | network_bytes_ingress |  Rete in ingresso  | 
| Microsoft.DBforMariaDB/servers | network_bytes_egress |  Rete in uscita  | 
| Microsoft.DBforMariaDB/servers | memory_percent |  Percentuale memoria  | 
| Microsoft.DBforMariaDB/servers | io_consumption_percent |  Percentuale IO  | 
| Microsoft.DBforMariaDB/servers | cpu_percent |  Percentuale CPU  | 
| Microsoft.DBforMariaDB/servers | connections_failed |  Connessioni non riuscite  | 
| Microsoft.DBforMariaDB/servers | backup_storage_used |  Backup Storage used (Archiviazione di backup usata)  | 
| Microsoft.DBforMariaDB/servers | active_connections |  Connessioni attive  | 
| Microsoft.DBforMySQL/servers | storage_used |  Uso archiviazione  | 
| Microsoft.DBforMySQL/servers | storage_percent |  Percentuale di archiviazione  | 
| Microsoft.DBforMySQL/servers | storage_limit |  Limite archiviazione  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_usage |  Archiviazione dei log del server usata  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_percent |  Percentuale di archiviazione dei log del server  | 
| Microsoft.DBforMySQL/servers | serverlog_storage_limit |  Limite di archiviazione dei log del server  | 
| Microsoft.DBforMySQL/servers | seconds_behind_master |  Intervallo di replica in secondi  | 
| Microsoft.DBforMySQL/servers | network_bytes_ingress |  Rete in ingresso  | 
| Microsoft.DBforMySQL/servers | network_bytes_egress |  Rete in uscita  | 
| Microsoft.DBforMySQL/servers | memory_percent |  Percentuale memoria  | 
| Microsoft.DBforMySQL/servers | io_consumption_percent |  Percentuale IO  | 
| Microsoft.DBforMySQL/servers | cpu_percent |  Percentuale CPU  | 
| Microsoft.DBforMySQL/servers | connections_failed |  Connessioni non riuscite  | 
| Microsoft.DBforMySQL/servers | backup_storage_used |  Backup Storage used (Archiviazione di backup usata)  | 
| Microsoft.DBforMySQL/servers | active_connections |  Connessioni attive  | 
| Microsoft.Devices/Account | digitaltwins.telemetry.nodes |  Digital Twins Node Telemetry Placeholder (Segnaposto nodi telemetria di Gemelli digitali)  | 
| Microsoft.Devices/IotHubs | twinQueries.success |  Query dei dispositivi gemelli completate  | 
| Microsoft.Devices/IotHubs | twinQueries.resultSize |  Dimensioni dei risultati delle query dei dispositivi gemelli  | 
| Microsoft.Devices/IotHubs | twinQueries.failure |  Query dei dispositivi gemelli non riuscite  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.success |  Query sui processi riuscite  | 
| Microsoft.Devices/IotHubs | jobs.queryJobs.failure |  Query sui processi non riuscite  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.success |  Chiamate per elencare i processi riuscite  | 
| Microsoft.Devices/IotHubs | jobs.listJobs.failure |  Chiamate per elencare i processi non riuscite  | 
| Microsoft.Devices/IotHubs | jobs.failed |  Processi non riusciti  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success |  Creazioni di processi di aggiornamento dei dispositivi gemelli completate  | 
| Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure |  Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success |  Creazioni di processi di chiamata al metodo completate  | 
| Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure |  Creazioni di processi di chiamata al metodo non riuscite  | 
| Microsoft.Devices/IotHubs | jobs.completed |  Processi completati  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.success |  Annullamenti di processi riusciti  | 
| Microsoft.Devices/IotHubs | jobs.cancelJob.failure |  Annullamenti di processi non riusciti  | 
| Microsoft.Devices/IotHubs | EventGridLatency |  Event Grid latency (preview) (Latenza Griglia di eventi (anteprima))  | 
| Microsoft.Devices/IotHubs | EventGridDeliveries |  Event Grid deliveries(preview) (Recapiti Griglia di eventi (anteprima))  | 
| Microsoft.Devices/IotHubs | devices.totalDevices |  Dispositivi totali (deprecati)  | 
| Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol |  Dispositivi connessi (deprecati)   | 
| Microsoft.Devices/IotHubs | deviceDataUsageV2 |  Uso totale dei dati del dispositivo (anteprima)  | 
| Microsoft.Devices/IotHubs | deviceDataUsage |  Total device data usage (Utilizzo totale dati dispositivo)  | 
| Microsoft.Devices/IotHubs | dailyMessageQuotaUsed |  Total number of messages used (Numero totale di messaggi usati)  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.success |  Aggiornamenti dei dispositivi gemelli completati dai dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.size |  Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.twin.update.failure |  Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.success |  Letture dei dispositivi gemelli completate dai dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.size |  Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.twin.read.failure |  Letture dei dispositivi gemelli non riuscite per i dispositivi  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success |  Messaggi di telemetria inviati  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle |  Number of throttling errors (Numero di errori di limitazione)  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol |  Tentativi di invio di messaggi di telemetria  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.success |  Routing: messaggi di telemetria recapitati  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned |  Routing: messaggi di telemetria orfani   | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid |  Routing: messaggi di telemetria incompatibili  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback |  Routing: messaggi recapitati al fallback  | 
| Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped |  Routing: messaggi di telemetria eliminati   | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage |  Routing: latenza dei messaggi per l'archiviazione  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics |  Routing: latenza dei messaggi per l'argomento del bus di servizio  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues |  Routing: latenza dei messaggi per la coda del bus di servizio  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs |  Routing: latenza dei messaggi per l'hub eventi  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events |  Routing: latenza dei messaggi per messaggi/eventi  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes |  Routing: dati recapitati all'archiviazione  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs |  Routing: BLOB recapitati all'archiviazione  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage |  Routing: messaggi recapitati all'archiviazione  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics |  Routing: messaggi recapitati all'argomento del bus di servizio  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues |  Routing: messaggi recapitati alla coda del bus di servizio  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs |  Routing: messaggi inviati all'hub eventi  | 
| Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events |  Routing: messaggi recapitati a messaggi/eventi  | 
| Microsoft.Devices/IotHubs | configurazioni |  Metriche di configurazione  | 
| Microsoft.Devices/IotHubs | C2DMessagesExpired |  C2D Messages Expired (preview) (Messaggi C2D scaduti (anteprima))  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.success |  Aggiornamenti dei dispositivi gemelli completati dal back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.size |  Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.update.failure |  Aggiornamenti dei dispositivi gemelli non riusciti per il back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.success |  Letture dei dispositivi gemelli completate dal back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.size |  Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end  | 
| Microsoft.Devices/IotHubs | c2d.twin.read.failure |  Letture dei dispositivi gemelli non riuscite per il back-end  | 
| Microsoft.Devices/IotHubs | c2d.methods.success |  Chiamate a metodi diretti riuscite  | 
| Microsoft.Devices/IotHubs | c2d.methods.responseSize |  Dimensioni delle risposte a chiamate a metodi diretti  | 
| Microsoft.Devices/IotHubs | c2d.methods.requestSize |  Dimensioni delle richieste di chiamate a metodi diretti  | 
| Microsoft.Devices/IotHubs | c2d.methods.failure |  Chiamate a metodi diretti non riuscite  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success |  C2D messages rejected (Messaggi C2D rifiutati)  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success |  C2D message deliveries completed (Recapiti messaggi C2D completati)  | 
| Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success |  C2D messages abandoned (Messaggi C2D abbandonati)  | 
| Microsoft.Devices/provisioningServices | RegistrationAttempts |  Tentativi di registrazione  | 
| Microsoft.Devices/provisioningServices | DeviceAssignments |  Dispositivi assegnati  | 
| Microsoft.Devices/provisioningServices | AttestationAttempts |  Tentativi di attestazione  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Total Request Units (Unità richiesta totali)  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Totale richieste  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Richieste Mongo  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo Request Charge (Addebito richiesta Mongo)  | 
| Microsoft.EventGrid/domains | PublishSuccessLatencyInMs |  Publish Success Latency (Latenza di pubblicazioni riuscite)  | 
| Microsoft.EventGrid/domains | PublishSuccessCount |  Eventi pubblicati  | 
| Microsoft.EventGrid/domains | PublishFailCount |  Publish Failed Events (Eventi con pubblicazione non riuscita)  | 
| Microsoft.EventGrid/domains | MatchedEventCount |  Eventi abbinati  | 
| Microsoft.EventGrid/domains | DroppedEventCount |  Eventi eliminati  | 
| Microsoft.EventGrid/domains | DeliverySuccessCount |  Eventi recapitati  | 
| Microsoft.EventGrid/domains | DeadLetteredCount |  Eventi di messaggi non recapitati  | 
| Microsoft.EventGrid/eventSubscriptions | MatchedEventCount |  Eventi abbinati  | 
| Microsoft.EventGrid/eventSubscriptions | DroppedEventCount |  Eventi eliminati  | 
| Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount |  Eventi recapitati  | 
| Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount |  Eventi di messaggi non recapitati  | 
| Microsoft.EventGrid/extensionTopics | UnmatchedEventCount |  Eventi senza corrispondenza  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Publish Success Latency (Latenza di pubblicazioni riuscite)  | 
| Microsoft.EventGrid/extensionTopics | PublishSuccessCount |  Eventi pubblicati  | 
| Microsoft.EventGrid/extensionTopics | PublishFailCount |  Publish Failed Events (Eventi con pubblicazione non riuscita)  | 
| Microsoft.EventGrid/topics | UnmatchedEventCount |  Eventi senza corrispondenza  | 
| Microsoft.EventGrid/topics | PublishSuccessLatencyInMs |  Publish Success Latency (Latenza di pubblicazioni riuscite)  | 
| Microsoft.EventGrid/topics | PublishSuccessCount |  Eventi pubblicati  | 
| Microsoft.EventGrid/topics | PublishFailCount |  Publish Failed Events (Eventi con pubblicazione non riuscita)  | 
| Microsoft.EventHub/clusters | OutgoingMessages |  Messaggi in uscita  | 
| Microsoft.EventHub/clusters | OutgoingBytes |  Byte in uscita.  | 
| Microsoft.EventHub/clusters | IncomingRequests |  Richieste in ingresso  | 
| Microsoft.EventHub/clusters | IncomingMessages |  Messaggi in ingresso  | 
| Microsoft.EventHub/clusters | IncomingBytes |  Byte in ingresso.  | 
| Microsoft.EventHub/namespaces | SVRBSY |  Errori server occupato (deprecato)  | 
| Microsoft.EventHub/namespaces | SUCCREQ |  Richieste completate correttamente (deprecato)  | 
| Microsoft.EventHub/namespaces | OUTMSGS |  Outgoing Messages (obsolete) (Deprecated) (Messaggi in uscita (obsoleto) (deprecato))  | 
| Microsoft.EventHub/namespaces | OutgoingMessages |  Messaggi in uscita  | 
| Microsoft.EventHub/namespaces | OutgoingBytes |  Byte in uscita.  | 
| Microsoft.EventHub/namespaces | MISCERR |  Altri errori (deprecato)  | 
| Microsoft.EventHub/namespaces | INTERR |  Errori interni del server (deprecato)  | 
| Microsoft.EventHub/namespaces | INREQS |  Richieste in ingresso (deprecato)  | 
| Microsoft.EventHub/namespaces | INMSGS |  Incoming Messages (obsolete) (Deprecated) (Messaggi in ingresso (obsoleto) (deprecato))  | 
| Microsoft.EventHub/namespaces | IncomingRequests |  Richieste in ingresso  | 
| Microsoft.EventHub/namespaces | IncomingMessages |  Messaggi in ingresso  | 
| Microsoft.EventHub/namespaces | IncomingBytes |  Byte in ingresso.  | 
| Microsoft.EventHub/namespaces | FAILREQ |  Richieste non riuscite (deprecato)  | 
| Microsoft.EventHub/namespaces | EHOUTMSGS |  Messaggi in uscita (deprecata)  | 
| Microsoft.EventHub/namespaces | EHOUTMBS |  Outgoing bytes (obsolete) (Deprecated) (Byte in uscita (obsoleto) (deprecato))  | 
| Microsoft.EventHub/namespaces | EHOUTBYTES |  Byte in uscita (deprecata)  | 
| Microsoft.EventHub/namespaces | EHINMSGS |  Messaggi in ingresso (deprecata)  | 
| Microsoft.EventHub/namespaces | EHINMBS |  Byte in ingresso (obsoleto) (deprecato)  | 
| Microsoft.EventHub/namespaces | EHINBYTES |  Byte in ingresso (deprecata)  | 
| Microsoft.EventHub/namespaces | EHAMSGS |  Archiviazione messaggi (deprecato)  | 
| Microsoft.EventHub/namespaces | EHAMBS |  Velocità effettiva archiviazione messaggi (deprecato)  | 
| Microsoft.EventHub/namespaces | EHABL |  Archiviazione backlog messaggi (deprecato)  | 
| Microsoft.HDInsight/clusters | NumActiveWorkers |  Number of Active Workers (Numero di ruoli di lavoro)  | 
| Microsoft.HDInsight/clusters | GatewayRequests |  Richieste del gateway  | 
| Microsoft.HDInsight/clusters | CategorizedGatewayRequests |  Richieste del gateway per categoria  | 
| Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated |  Azioni di ridimensionamento avviate  | 
| Microsoft.Insights/Components | traces/count |  Traces  | 
| Microsoft.Insights/Components | performanceCounters/requestsPerSecond |  Frequenza delle richieste HTTP  | 
| Microsoft.Insights/Components | performanceCounters/requestsInQueue |  Richieste HTTP nella coda dell'applicazione  | 
| Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond |  Frequenza di eccezioni  | 
| Microsoft.Insights/Components | pageViews/count |  Visualizzazioni pagina  | 
| Microsoft.Insights/Components | exceptions/count |  Eccezioni  | 
| Microsoft.Kusto/Clusters | StreamingIngestResults |  Streaming Ingest Result (Risultato inserimento streaming)  | 
| Microsoft.Kusto/Clusters | StreamingIngestDuration |  Streaming Ingest Duration (Durata inserimento streaming)  | 
| Microsoft.Kusto/Clusters | StreamingIngestDataRate |  Streaming Ingest Data Rate (Velocità dati inserimento streaming)  | 
| Microsoft.Kusto/Clusters | SteamingIngestRequestRate |  Streaming Ingest Request Rate (Frequenza richieste inserimento streaming)  | 
| Microsoft.Kusto/Clusters | QueryDuration |  Durata delle query  | 
| Microsoft.Kusto/Clusters | KeepAlive |  Keep alive  | 
| Microsoft.Kusto/Clusters | IngestionVolumeInMB |  Ingestion volume (in MB) (Volume inserimento (in MB))  | 
| Microsoft.Kusto/Clusters | IngestionUtilization |  Utilizzo inserimento  | 
| Microsoft.Kusto/Clusters | IngestionResult |  Risultati inserimento  | 
| Microsoft.Kusto/Clusters | IngestionLatencyInSeconds |  Latenza inserimento (in secondi)  | 
| Microsoft.Kusto/Clusters | ExportUtilization |  Export Utilization (Utilizzo esportazione)  | 
| Microsoft.Kusto/Clusters | EventsProcessedForEventHubs |  Events processed (for Event/IoT Hubs) (Eventi elaborati (per hub eventi/IoT))  | 
| Microsoft.Kusto/Clusters | CPU |  CPU  | 
| Microsoft.Kusto/Clusters | ContinuousExportResult |  Continuous Export Result (Risultato esportazione continua)  | 
| Microsoft.Kusto/Clusters | ContinuousExportPendingCount |  Continuous Export Pending Count (Esportazione continua - Conteggio in sospeso)  | 
| Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported |  Continuous export - num of exported records (Esportazione continua - Numero di record esportati)  | 
| Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes |  Continuous Export Max Lateness Minutes (Esportazione continua - Numero max minuti ritardo)  | 
| Microsoft.Kusto/Clusters | CacheUtilization |  Utilizzo cache  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Eventi limitati trigger  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded |  Trigger riusciti   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersStarted |  Trigger avviati   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped |  Trigger ignorati  | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFired |  Trigger rimossi   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersFailed |  Trigger non riusciti   | 
| Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted |  Trigger completati   | 
| Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents |  Eventi limitati esecuzione  | 
| Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Eventi di avvio esecuzione limitati  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded |  Esecuzioni riuscite  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsStarted |  Esecuzioni avviate  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsFailed |  Esecuzioni non riuscite  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCompleted |  Esecuzioni completate  | 
| Microsoft.Logic/integrationServiceEnvironments | RunsCancelled |  Esecuzioni annullate  | 
| Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage |  Percentuale di errori di esecuzione  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents |  Eventi limitati azione  | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded |  Azioni riuscite   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsStarted |  Azioni avviate   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped |  Azioni ignorate   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsFailed |  Azioni non riuscite   | 
| Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted |  Azioni completate   | 
| Microsoft.Logic/workflows | TriggerThrottledEvents |  Eventi limitati trigger  | 
| Microsoft.Logic/workflows | TriggersSucceeded |  Trigger riusciti   | 
| Microsoft.Logic/workflows | TriggersStarted |  Trigger avviati   | 
| Microsoft.Logic/workflows | TriggersSkipped |  Trigger ignorati  | 
| Microsoft.Logic/workflows | TriggersFired |  Trigger rimossi   | 
| Microsoft.Logic/workflows | TriggersFailed |  Trigger non riusciti   | 
| Microsoft.Logic/workflows | TriggersCompleted |  Trigger completati   | 
| Microsoft.Logic/workflows | TotalBillableExecutions |  Esecuzioni fatturabili totali  | 
| Microsoft.Logic/workflows | RunThrottledEvents |  Eventi limitati esecuzione  | 
| Microsoft.Logic/workflows | RunStartThrottledEvents |  Eventi di avvio esecuzione limitati  | 
| Microsoft.Logic/workflows | RunsSucceeded |  Esecuzioni riuscite  | 
| Microsoft.Logic/workflows | RunsStarted |  Esecuzioni avviate  | 
| Microsoft.Logic/workflows | RunsFailed |  Esecuzioni non riuscite  | 
| Microsoft.Logic/workflows | RunsCompleted |  Esecuzioni completate  | 
| Microsoft.Logic/workflows | RunsCancelled |  Esecuzioni annullate  | 
| Microsoft.Logic/workflows | RunFailurePercentage |  Percentuale di errori di esecuzione  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Uso fatturazione per esecuzioni con uso dell'archiviazione  | 
| Microsoft.Logic/workflows | BillingUsageStorageConsumption |  Uso fatturazione per esecuzioni con uso dell'archiviazione  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Uso fatturazione per esecuzioni del connettore standard  | 
| Microsoft.Logic/workflows | BillingUsageStandardConnector |  Uso fatturazione per esecuzioni del connettore standard  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Uso fatturazione per esecuzioni di operazioni native  | 
| Microsoft.Logic/workflows | BillingUsageNativeOperation |  Uso fatturazione per esecuzioni di operazioni native  | 
| Microsoft.Logic/workflows | BillableTriggerExecutions |  Esecuzioni di trigger fatturabili  | 
| Microsoft.Logic/workflows | BillableActionExecutions |  Esecuzioni di azioni fatturabili  | 
| Microsoft.Logic/workflows | ActionThrottledEvents |  Eventi limitati azione  | 
| Microsoft.Logic/workflows | ActionsSucceeded |  Azioni riuscite   | 
| Microsoft.Logic/workflows | ActionsStarted |  Azioni avviate   | 
| Microsoft.Logic/workflows | ActionsSkipped |  Azioni ignorate   | 
| Microsoft.Logic/workflows | ActionsFailed |  Azioni non riuscite   | 
| Microsoft.Logic/workflows | ActionsCompleted |  Azioni completate   | 
| Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount |  Conteggio delle richieste web application firewall  | 
| Microsoft.Network/frontdoors | TotalLatency |  Latenza totale  | 
| Microsoft.Network/frontdoors | ResponseSize |  Dimensioni della risposta  | 
| Microsoft.Network/frontdoors | RequestSize |  Dimensioni della richiesta  | 
| Microsoft.Network/frontdoors | RequestCount |  Conteggio richieste  | 
| Microsoft.Network/frontdoors | BillableResponseSize |  Billable Response Size (Dimensione risposta fatturabile)  | 
| Microsoft.Network/frontdoors | BackendRequestLatency |  Latenza della richiesta del back-end  | 
| Microsoft.Network/frontdoors | BackendRequestCount |  Conteggio delle richieste del back-end  | 
| Microsoft.Network/frontdoors | BackendHealthPercentage |  Percentuale di integrità del back-end  | 
| Microsoft.Network/trafficManagerProfiles | QpsByEndpoint |  Query da endpoint restituite  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending |  Notifiche pianificate in sospeso  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update |  Operazioni di aggiornamento registrazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get |  Operazioni di lettura registrazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete |  Operazioni di eliminazione registrazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create |  Operazioni di creazione registrazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all |  Operazioni di registrazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken |  Errori di autorizzazione WNS (token errato)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable |  Errori di autorizzazione WNS (non disponibile)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled |  Notifiche WNS limitate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success |  Notifiche WNS completate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror |  Errori WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken |  Errori di autorizzazione WNS (token non valido)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize |  Errore dimensioni notifica WNS non valide  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat |  Formato notifica WNS non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials |  Errori di autorizzazione WNS (credenziali non valide)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel |  Errore canale WNS scaduto  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped |  Notifiche WNS eliminate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled |  Canale WNS limitato  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected |  Canale WNS disconnesso  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel |  Errore canale WNS non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror |  Errori di autenticazione WNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled |  Notifiche MPNS limitate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success |  Notifiche MPNS completate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror |  Errori MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat |  Formato notifiche MPNS non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials |  Credenziali MPNS non valide  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped |  Notifiche MPNS eliminate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected |  Canale MPNS disconnesso  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel |  Errori canale MPNS errato  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror |  Errori di autenticazione MPNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel |  Errore canale GCM errato  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled |  Notifiche GCM limitate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success |  Notifiche GCM completate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror |  Errori GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize |  Errore dimensioni notifica GCM non valide  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat |  Formato notifiche GCM non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials |  Errori di autorizzazione GCM (credenziali non valide)  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel |  Errore canale GCM scaduto  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel |  Errore canale GCM non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror |  Errori di autenticazione GCM  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success |  Notifiche APNS completate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror |  Errori APNS  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize |  Errore dimensioni notifica APNS non valide  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials |  Errori di autorizzazione del servizio APN  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel |  Errore canale APNS scaduto  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel |  Errore canale APNS non valido  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success |  Notifiche riuscite  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror |  Errori sistema di notifica esterno  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload |  Errori payload  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror |  Errori canale  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes |  Tutte le notifiche in uscita  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert |  Crea o aggiorna operazioni di installazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch |  Operazioni di installazione patch  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get |  Ottieni operazioni di installazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete |  Elimina operazioni di installazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all |  Operazioni di gestione installazione  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel |  Notifiche push pianificate annullate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled |  Notifiche push pianificate inviate  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests |  Tutte le richieste in ingresso  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests |  Tutte le richieste in ingresso non riuscite  | 
| Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming |  Messaggi in ingresso  | 
| Microsoft.OperationalInsights/workspaces | Heartbeat |  Heartbeat  | 
| Microsoft.Relay/namespaces | BytesTransferred |  BytesTransferred  | 
| Microsoft.ServiceBus/namespaces | OutgoingMessages |  Messaggi in uscita  | 
| Microsoft.ServiceBus/namespaces | IncomingRequests |  Richieste in ingresso  | 
| Microsoft.ServiceBus/namespaces | IncomingMessages |  Messaggi in ingresso  | 
| Microsoft.SignalRService/SignalR | UserErrors |  Errori utente  | 
| Microsoft.SignalRService/SignalR | SystemErrors |  Errori di sistema  | 
| Microsoft.SignalRService/SignalR | OutboundTraffic |  Traffico in uscita  | 
| Microsoft.SignalRService/SignalR | MessageCount |  Numero messaggi  | 
| Microsoft.SignalRService/SignalR | InboundTraffic |  Traffico in ingresso  | 
| Microsoft.SignalRService/SignalR | ConnectionCount |  Numero di connessioni  | 
| Microsoft.Sql/managedInstances | avg_cpu_percent |  Percentuale CPU Media  | 
| Microsoft.Sql/servers | dtu_used |  Uso DTU  | 
| Microsoft.Sql/servers | dtu_consumption_percent |  Percentuale di DTU  | 
| Microsoft.Sql/servers/databases | xtp_storage_percent |  Percentuale di archiviazione OLTP in memoria  | 
| Microsoft.Sql/servers/databases | workers_percent |  Percentuale ruoli di lavoro  | 
| Microsoft.Sql/servers/databases | sessions_percent |  Percentuale sessioni  | 
| Microsoft.Sql/servers/databases | physical_data_read_percent |  Percentuale di I/O di dati  | 
| Microsoft.Sql/servers/databases | log_write_percent |  Percentuale I/O registro  | 
| Microsoft.Sql/servers/databases | dwu_used |  Uso DWU  | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent |  Percentuale DWU  | 
| Microsoft.Sql/servers/databases | dtu_used |  Uso DTU  | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent |  Percentuale di DTU  | 
| Microsoft.Sql/servers/databases | deadlock |  Deadlock  | 
| Microsoft.Sql/servers/databases | cpu_used |  CPU used (CPU usata)  | 
| Microsoft.Sql/servers/databases | cpu_percent |  Percentuale CPU  | 
| Microsoft.Sql/servers/databases | connection_successful |  Connessioni riuscite  | 
| Microsoft.Sql/servers/databases | connection_failed |  Connessioni non riuscite  | 
| Microsoft.Sql/servers/databases | cache_hit_percent |  Percentuale dei riscontri nella cache  | 
| Microsoft.Sql/servers/databases | blocked_by_firewall |  Blocco da parte del firewall  | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent |  Percentuale di archiviazione OLTP in memoria  | 
| Microsoft.Sql/servers/elasticPools | workers_percent |  Percentuale ruoli di lavoro  | 
| Microsoft.Sql/servers/elasticPools | sessions_percent |  Percentuale sessioni  | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent |  Percentuale di I/O di dati  | 
| Microsoft.Sql/servers/elasticPools | log_write_percent |  Percentuale I/O registro  | 
| Microsoft.Sql/servers/elasticPools | eDTU_used |  Uso eDTU  | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent |  Percentuale di DTU  | 
| Microsoft.Sql/servers/elasticPools | cpu_percent |  Percentuale CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Front end totali  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Ruoli di lavoro piano di servizio app Small  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Requests |  Requests  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Percentuale memoria  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Ruoli di lavoro piano di servizio app Medium  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Ruoli di lavoro piano di servizio app Large  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Lunghezza coda HTTP  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx |  Errori server HTTP  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Lunghezza coda disco  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage |  Percentuale CPU  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent |  Dati in uscita  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived |  Dati in entrata  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Tempo medio di risposta  | 
| Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests |  Richieste attive  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed |  Ruoli di lavoro usati  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal |  Ruoli di lavoro totali  | 
| Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable |  Ruoli di lavoro disponibili  | 
| Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage |  Percentuale memoria  | 
| Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage |  Percentuale CPU  | 
| Microsoft.Web/serverfarms | TcpTimeWait |  TCP Time Wait  | 
| Microsoft.Web/serverfarms | TcpSynSent |  TCP Syn Sent  | 
| Microsoft.Web/serverfarms | TcpSynReceived |  TCP Syn Received  | 
| Microsoft.Web/serverfarms | TcpLastAck |  TCP Last Ack  | 
| Microsoft.Web/serverfarms | TcpFinWait2 |  TCP Fin Wait 2  | 
| Microsoft.Web/serverfarms | TcpFinWait1 |  TCP Fin Wait 1  | 
| Microsoft.Web/serverfarms | TcpEstablished |  TCP Established  | 
| Microsoft.Web/serverfarms | TcpClosing |  TCP Closing  | 
| Microsoft.Web/serverfarms | TcpCloseWait |  TCP Close Wait  | 
| Microsoft.Web/serverfarms | MemoryPercentage |  Percentuale memoria  | 
| Microsoft.Web/serverfarms | HttpQueueLength |  Lunghezza coda HTTP  | 
| Microsoft.Web/serverfarms | DiskQueueLength |  Lunghezza coda disco  | 
| Microsoft.Web/serverfarms | CpuPercentage |  Percentuale CPU  | 
| Microsoft.Web/serverfarms | BytesSent |  Dati in uscita  | 
| Microsoft.Web/serverfarms | BytesReceived |  Dati in entrata  | 
| Microsoft.Web/sites | TotalAppDomainsUnloaded |  Totale di domini app scaricati  | 
| Microsoft.Web/sites | TotalAppDomains |  Totale di domini app  | 
| Microsoft.Web/sites | Threads |  Thread Count  | 
| Microsoft.Web/sites | RequestsInApplicationQueue |  Richieste nella coda dell'applicazione  | 
| Microsoft.Web/sites | Requests |  Requests  | 
| Microsoft.Web/sites | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites | MemoryWorkingSet |  Working set della memoria  | 
| Microsoft.Web/sites | IoWriteOperationsPerSecond |  I/O - Operazioni di scrittura al secondo  | 
| Microsoft.Web/sites | IoWriteBytesPerSecond |  I/O - Byte in scrittura al secondo  | 
| Microsoft.Web/sites | IoReadOperationsPerSecond |  I/O - Operazioni di lettura al secondo  | 
| Microsoft.Web/sites | IoReadBytesPerSecond |  I/O - Byte in lettura al secondo  | 
| Microsoft.Web/sites | IoOtherOperationsPerSecond |  I/O - Altre operazioni al secondo  | 
| Microsoft.Web/sites | IoOtherBytesPerSecond |  I/O - Altri byte al secondo  | 
| Microsoft.Web/sites | HttpResponseTime |  Tempo di risposta  | 
| Microsoft.Web/sites | Http5xx |  Errori server HTTP  | 
| Microsoft.Web/sites | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites | Http406 |  Http 406  | 
| Microsoft.Web/sites | Http404 |  Http 404  | 
| Microsoft.Web/sites | Http403 |  Http 403  | 
| Microsoft.Web/sites | Http401 |  Http 401  | 
| Microsoft.Web/sites | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites | Http101 |  Http 101  | 
| Microsoft.Web/sites | HealthCheckStatus |  Stato del controllo integrità  | 
| Microsoft.Web/sites | Selettori |  Numero di handle  | 
| Microsoft.Web/sites | Gen2Collections |  Garbage Collection di generazione 2  | 
| Microsoft.Web/sites | Gen1Collections |  Garbage Collection di generazione 1  | 
| Microsoft.Web/sites | Gen0Collections |  Garbage Collection di generazione 0  | 
| Microsoft.Web/sites | FunctionExecutionUnits |  Unità di esecuzione della funzione  | 
| Microsoft.Web/sites | FunctionExecutionCount |  Conteggio delle esecuzioni della funzione  | 
| Microsoft.Web/sites | CurrentAssemblies |  Assembly attuali  | 
| Microsoft.Web/sites | CpuTime |  Tempo CPU  | 
| Microsoft.Web/sites | BytesSent |  Dati in uscita  | 
| Microsoft.Web/sites | BytesReceived |  Dati in entrata  | 
| Microsoft.Web/sites | AverageResponseTime |  Tempo medio di risposta  | 
| Microsoft.Web/sites | AverageMemoryWorkingSet |  Working set della memoria medio  | 
| Microsoft.Web/sites | AppConnections |  Connessioni  | 
| Microsoft.Web/sites/slots | TotalAppDomainsUnloaded |  Totale di domini app scaricati  | 
| Microsoft.Web/sites/slots | TotalAppDomains |  Totale di domini app  | 
| Microsoft.Web/sites/slots | Threads |  Thread Count  | 
| Microsoft.Web/sites/slots | RequestsInApplicationQueue |  Richieste nella coda dell'applicazione  | 
| Microsoft.Web/sites/slots | Requests |  Requests  | 
| Microsoft.Web/sites/slots | PrivateBytes |  Private Bytes  | 
| Microsoft.Web/sites/slots | MemoryWorkingSet |  Working set della memoria  | 
| Microsoft.Web/sites/slots | IoWriteOperationsPerSecond |  I/O - Operazioni di scrittura al secondo  | 
| Microsoft.Web/sites/slots | IoWriteBytesPerSecond |  I/O - Byte in scrittura al secondo  | 
| Microsoft.Web/sites/slots | IoReadOperationsPerSecond |  I/O - Operazioni di lettura al secondo  | 
| Microsoft.Web/sites/slots | IoReadBytesPerSecond |  I/O - Byte in lettura al secondo  | 
| Microsoft.Web/sites/slots | IoOtherOperationsPerSecond |  I/O - Altre operazioni al secondo  | 
| Microsoft.Web/sites/slots | IoOtherBytesPerSecond |  I/O - Altri byte al secondo  | 
| Microsoft.Web/sites/slots | HttpResponseTime |  Tempo di risposta  | 
| Microsoft.Web/sites/slots | Http5xx |  Errori server HTTP  | 
| Microsoft.Web/sites/slots | Http4xx |  Http 4xx  | 
| Microsoft.Web/sites/slots | Http406 |  Http 406  | 
| Microsoft.Web/sites/slots | Http404 |  Http 404  | 
| Microsoft.Web/sites/slots | Http403 |  Http 403  | 
| Microsoft.Web/sites/slots | Http401 |  Http 401  | 
| Microsoft.Web/sites/slots | Http3xx |  Http 3xx  | 
| Microsoft.Web/sites/slots | Http2xx |  Http 2xx  | 
| Microsoft.Web/sites/slots | Http101 |  Http 101  | 
| Microsoft.Web/sites/slots | HealthCheckStatus |  Stato del controllo integrità  | 
| Microsoft.Web/sites/slots | Selettori |  Numero di handle  | 
| Microsoft.Web/sites/slots | Gen2Collections |  Garbage Collection di generazione 2  | 
| Microsoft.Web/sites/slots | Gen1Collections |  Garbage Collection di generazione 1  | 
| Microsoft.Web/sites/slots | Gen0Collections |  Garbage Collection di generazione 0  | 
| Microsoft.Web/sites/slots | FunctionExecutionUnits |  Unità di esecuzione della funzione  | 
| Microsoft.Web/sites/slots | FunctionExecutionCount |  Conteggio delle esecuzioni della funzione  | 
| Microsoft.Web/sites/slots | CurrentAssemblies |  Assembly attuali  | 
| Microsoft.Web/sites/slots | CpuTime |  Tempo CPU  | 
| Microsoft.Web/sites/slots | BytesSent |  Dati in uscita  | 
| Microsoft.Web/sites/slots | BytesReceived |  Dati in entrata  | 
| Microsoft.Web/sites/slots | AverageResponseTime |  Tempo medio di risposta  | 
| Microsoft.Web/sites/slots | AverageMemoryWorkingSet |  Working set della memoria medio  | 
| Microsoft.Web/sites/slots | AppConnections |  Connessioni  | 
| Microsoft.Sql/servers/databases | cpu_percent | Percentuale CPU | 
| Microsoft.Sql/servers/databases | physical_data_read_percent | Percentuale di I/O di dati | 
| Microsoft.Sql/servers/databases | log_write_percent | Percentuale I/O registro | 
| Microsoft.Sql/servers/databases | dtu_consumption_percent | Percentuale di DTU | 
| Microsoft.Sql/servers/databases | connection_successful | Connessioni riuscite | 
| Microsoft.Sql/servers/databases | connection_failed | Connessioni non riuscite | 
| Microsoft.Sql/servers/databases | blocked_by_firewall | Blocco da parte del firewall | 
| Microsoft.Sql/servers/databases | deadlock | Deadlock | 
| Microsoft.Sql/servers/databases | xtp_storage_percent | Percentuale di archiviazione OLTP in memoria | 
| Microsoft.Sql/servers/databases | workers_percent | Percentuale ruoli di lavoro | 
| Microsoft.Sql/servers/databases | sessions_percent | Percentuale sessioni | 
| Microsoft.Sql/servers/databases | dtu_used | Uso DTU | 
| Microsoft.Sql/servers/databases | cpu_used | CPU used (CPU usata) | 
| Microsoft.Sql/servers/databases | dwu_consumption_percent | Percentuale DWU | 
| Microsoft.Sql/servers/databases | dwu_used | Uso DWU | 
| Microsoft.Sql/servers/databases | cache_hit_percent | Percentuale dei riscontri nella cache | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_system_percent | Allocazione del gruppo di carico di lavoro per percentuale di sistema | 
| Microsoft.Sql/servers/databases | wlg_allocation_relative_to_wlg_effective_cap_percent | Allocazione del gruppo di carico di lavoro per percentuale risorse massima | 
| Microsoft.Sql/servers/databases | wlg_active_queries | Query attive del gruppo di carico di lavoro | 
| Microsoft.Sql/servers/databases | wlg_queued_queries | Query accodate del gruppo di carico di lavoro | 
| Microsoft.Sql/servers/databases | active_queries | Query attive | 
| Microsoft.Sql/servers/databases | queued_queries | Query in coda | 
| Microsoft.Sql/servers/databases | wlg_active_queries_timeouts | Timeout query del gruppo di carico di lavoro | 
| Microsoft.Sql/servers/databases | wlg_queued_queries_timeouts | Timeout delle query in coda del gruppo di carico di lavoro | 
| Microsoft.Sql/servers/databases | wlg_effective_min_resource_percent | Percentuale risorse minima effettiva | 
| Microsoft.Sql/servers/databases | wlg_effective_cap_resource_percent | Percentuale limite di risorse effettiva | 
| Microsoft.Sql/servers/elasticPools | cpu_percent | Percentuale CPU | 
| Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Percentuale di I/O di dati | 
| Microsoft.Sql/servers/elasticPools | log_write_percent | Percentuale I/O registro | 
| Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Percentuale di DTU | 
| Microsoft.Sql/servers/elasticPools | workers_percent | Percentuale ruoli di lavoro | 
| Microsoft.Sql/servers/elasticPools | sessions_percent | Percentuale sessioni | 
| Microsoft.Sql/servers/elasticPools | eDTU_used | Uso eDTU | 
| Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Percentuale di archiviazione OLTP in memoria | 
| Microsoft.Sql/servers | dtu_consumption_percent | Percentuale di DTU | 
| Microsoft.Sql/servers | dtu_used | Uso DTU | 
| Microsoft.Sql/managedInstances | avg_cpu_percent | Percentuale CPU Media | 

