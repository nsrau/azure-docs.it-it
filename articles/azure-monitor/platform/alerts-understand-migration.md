---
title: Informazioni sul funzionamento dello strumento di migrazione volontaria per gli avvisi di monitoraggio di Azure
description: Informazioni sul funzionamento dello strumento di migrazione degli avvisi e sulla risoluzione dei problemi.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: f981c14e26c51c427dab6b418cab8df46b1bb026
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302254"
---
# <a name="understand-how-the-migration-tool-works"></a>Informazioni sul funzionamento dello strumento di migrazione

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici in monitoraggio di Azure verranno ritirati entro il 31 agosto 2019 (originariamente il 30 giugno 2019). Uno strumento di migrazione è disponibile nel portale di Azure ai clienti che utilizzano le regole di avviso classiche e che desiderano attivare la migrazione autonomamente.

Questo articolo spiega come funziona lo strumento di migrazione volontaria. Vengono inoltre descritte le soluzioni per alcuni problemi comuni.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di ritiro per la migrazione degli avvisi classica è stata [estesa al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di annuncio iniziale del 30 giugno 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regole di avviso classiche che non verranno migrate

> [!IMPORTANT]
> Gli avvisi del log attività (inclusi gli avvisi di integrità del servizio) e gli avvisi del log non sono interessati dalla migrazione. La migrazione si applica solo alle regole di avviso classiche descritte di [seguito](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Sebbene lo strumento possa eseguire la migrazione di quasi tutte le [regole di avviso classiche](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), esistono alcune eccezioni. Non verrà eseguita la migrazione delle seguenti regole di avviso tramite lo strumento (o durante la migrazione automatica a partire dal 2019 settembre):

- Regole di avviso classiche sulle metriche Guest della macchina virtuale (sia Windows che Linux). Vedere le [linee guida per la ricreazione di tali regole di avviso in nuovi avvisi delle metriche](#guest-metrics-on-virtual-machines) più avanti in questo articolo.
- Regole di avviso classiche sulle metriche di archiviazione classiche. Vedere le [linee guida per il monitoraggio degli account di archiviazione classici](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regole di avviso classiche per alcune metriche dell'account di archiviazione. Vedere [i dettagli](#storage-account-metrics) più avanti in questo articolo.
- Regole di avviso classiche in alcune Cosmos DB metriche. Vedere [i dettagli](#cosmos-db-metrics) più avanti in questo articolo.
- Regole di avviso classiche per tutte le macchine virtuali classiche e le metriche dei servizi cloud (Microsoft. ClassicCompute/virtualMachines e Microsoft. ClassicCompute/domainNames/Slots/Roles). Vedere [i dettagli](#classic-compute-metrics) più avanti in questo articolo.

Se la sottoscrizione include regole classiche di questo tipo, è necessario eseguirne la migrazione manualmente. Dal momento che non è possibile fornire una migrazione automatica, eventuali avvisi di metrica esistenti e classici di questi tipi continueranno a funzionare fino al 2020 giugno. Questa estensione consente di passare ai nuovi avvisi. Non è tuttavia possibile creare nuovi avvisi classici dopo il 2019 agosto.

> [!NOTE]
> Oltre alle eccezioni elencate in precedenza, se le regole di avviso classiche non sono valide, ad esempio se si tratta di [metriche deprecate](#classic-alert-rules-on-deprecated-metrics) o di risorse eliminate, non verranno migrate durante la migrazione volontaria. Eventuali regole di avviso classiche non valide verranno eliminate al momento della migrazione automatica.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche Guest nelle macchine virtuali

Prima di poter creare nuovi avvisi sulle metriche Guest, è necessario inviare le metriche Guest all'archivio delle metriche personalizzate di monitoraggio di Azure. Seguire queste istruzioni per abilitare il sink di monitoraggio di Azure nelle impostazioni di diagnostica:

- [Abilitazione delle metriche Guest per macchine virtuali Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Abilitazione delle metriche Guest per macchine virtuali Linux](collect-custom-metrics-linux-telegraf.md)

Al termine di questa procedura, è possibile creare nuovi avvisi delle metriche per le metriche Guest. Dopo aver creato nuovi avvisi per le metriche, è possibile eliminare gli avvisi classici.

### <a name="storage-account-metrics"></a>Metriche relative all'account di archiviazione

È possibile eseguire la migrazione di tutti gli avvisi classici sugli account di archiviazione, eccetto gli avvisi relativi a queste metriche:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

È necessario eseguire la migrazione delle regole di avviso classiche sulla percentuale di metriche in base [al mapping tra la metrica di archiviazione precedente e quella nuova](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Le soglie dovranno essere modificate in modo appropriato perché la nuova metrica disponibile è assoluta.

Le regole di avviso classiche in AnonymousThrottlingError, SASThrottlingError e ThrottlingError devono essere suddivise in due nuovi avvisi perché non esiste alcuna metrica combinata che fornisce la stessa funzionalità. Le soglie dovranno essere adattate in modo appropriato.

### <a name="cosmos-db-metrics"></a>Metriche di Cosmos DB

È possibile eseguire la migrazione di tutti gli avvisi classici sulle metriche di Cosmos DB, eccetto gli avvisi relativi a queste metriche:

- Media richieste al secondo
- Livello di coerenza
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Internal Server Error
- Numero massimo di RUPM utilizzati al minuto
- Numero massimo di ur al secondo
- Numero di richieste non riuscite di Mongo
- Richieste di eliminazione Mongo non riuscite
- Richieste di inserimento non riuscite di Mongo
- Mongo altre richieste non riuscite
- Addebito per altre richieste di Mongo
- Frequenza di richieste altre
- Richieste di query Mongo non riuscite
- Richieste di aggiornamento Mongo non riuscite
- Latenza di lettura osservata
- Latenza di scrittura osservata
- Disponibilità del servizio
- Capacità di archiviazione
- Richieste limitate
- Totale richieste

Media di richieste al secondo, livello di coerenza, numero massimo di RUPM consumate al minuto, numero massimo di ur al secondo, latenza di lettura osservata, latenza di scrittura osservata, capacità di archiviazione attualmente non disponibile nel [nuovo sistema](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Gli avvisi sulle metriche delle richieste come HTTP 2xx, HTTP 3xx, http 400, HTTP 401, errore interno del server, disponibilità del servizio, richieste limitate e richieste totali non vengono migrati perché la modalità di conteggio delle richieste è diversa tra le metriche classiche e le nuove metriche. Gli avvisi su questi dovranno essere ricreati manualmente con le soglie regolate.

Gli avvisi per le metriche delle richieste non riuscite di Mongo devono essere suddivisi in più avvisi perché non esiste una metrica combinata che fornisce la stessa funzionalità. Le soglie dovranno essere adattate in modo appropriato.

### <a name="classic-compute-metrics"></a>Metriche di calcolo classiche

Tutti gli avvisi sulle metriche di calcolo classiche non verranno migrati usando lo strumento di migrazione perché le risorse di calcolo classiche non sono ancora supportate con i nuovi avvisi. Il supporto per i nuovi avvisi per questi tipi di risorse verrà aggiunto in futuro. Una volta che è disponibile, i clienti devono ricreare le nuove regole di avviso equivalenti in base alle regole di avviso classiche prima del 2020 giugno.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regole di avviso classiche su metriche deprecate

Si tratta di regole di avviso classiche sulle metriche che in precedenza erano supportate ma che erano deprecate. Una piccola percentuale di clienti potrebbe avere regole di avviso classiche non valide per tali metriche. Poiché queste regole di avviso non sono valide, non verranno migrate.

| Tipo di risorsa| Metriche deprecate |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft. SQL/Servers/databases | service_level_objective, storage_limit, storage_used, limitazione, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Modalità di creazione di nuovi gruppi di azione e regole di avviso equivalenti

Lo strumento di migrazione converte le regole di avviso classiche in nuovi gruppi di azione e regole di avviso equivalenti. Per la maggior parte delle regole di avviso classiche, le nuove regole di avviso equivalenti sono sulla stessa `windowSize` metrica `aggregationType`con le stesse proprietà, ad esempio e. Tuttavia, alcune regole di avviso classiche sono relative alle metriche con una metrica equivalente diversa nel nuovo sistema. I principi seguenti si applicano alla migrazione di avvisi classici, a meno che non sia specificato nella sezione seguente:

- **Frequenza**: Definisce la frequenza con cui una regola di avviso classica o nuova controlla la condizione. Le `frequency` regole di avviso classiche non possono essere configurate dall'utente ed è sempre 5 minuti per tutti i tipi di risorse tranne Application Insights componenti per i quali è stato di 1 min. La frequenza delle regole equivalenti viene anche impostata rispettivamente su 5 min e 1 min.
- **Tipo**di aggregazione: Definisce la modalità di aggregazione della metrica sulla finestra di interesse. `aggregationType` È anche lo stesso tra gli avvisi classici e i nuovi avvisi per la maggior parte delle metriche. In alcuni casi, poiché la metrica è diversa tra gli avvisi classici e i nuovi avvisi `aggregationType` , viene `primary Aggregation Type` usato il valore equivalente o quello definito per la metrica.
- **Unità**: Proprietà della metrica in cui viene creato l'avviso. Alcune metriche equivalenti hanno unità diverse. La soglia viene modificata in modo appropriato in base alle esigenze. Se, ad esempio, la metrica originale presenta secondi come unità, ma la nuova metrica equivalente ha milliSecondi come unità, la soglia originale viene moltiplicata per 1000 per garantire lo stesso comportamento.
- **Dimensioni finestra**: Definisce la finestra su cui vengono aggregati i dati delle metriche per il confronto con la soglia. Per i `windowSize` valori standard come 5 minuti, 15mins, 30mins, 1 ora, 3 ore, 6 ore, 12 ore, 1 giorno, non sono state apportate modifiche per la nuova regola di avviso equivalente. Per gli altri valori, `windowSize` viene scelto il più vicino da usare. Per la maggior parte dei clienti, questa modifica non ha alcun effetto. Per una piccola percentuale di clienti, potrebbe essere necessario modificare la soglia per ottenere lo stesso comportamento.

Nelle sezioni seguenti vengono illustrate in dettaglio le metriche con una metrica equivalente diversa nel nuovo sistema. Tutte le metriche rimanenti per le regole di avviso classiche e nuove non sono elencate. È possibile trovare un elenco delle metriche supportate nel nuovo sistema [qui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Per i servizi dell'account di archiviazione come BLOB, tabelle, file e code, le metriche seguenti sono mappate alle metriche equivalenti, come illustrato di seguito:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrica transazioni con dimensioni "ResponseType" = "AuthorizationError" e "autenticazione" = "anonima"| |
| AnonymousClientOtherError | Metrica transazioni con dimensioni "ResponseType" = "ClientOtherError" e "autenticazione" = "anonima" | |
| AnonymousClientTimeOutError| Metrica transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "autenticazione" = "anonima" | |
| AnonymousNetworkError | Metrica transazioni con dimensioni "ResponseType" = "NetworkError" e "autenticazione" = "anonima" | |
| AnonymousServerOtherError | Metrica transazioni con dimensioni "ResponseType" = "ServerOtherError" e "autenticazione" = "anonima" | |
| AnonymousServerTimeOutError | Metrica transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "autenticazione" = "anonima" | |
| AnonymousSuccess | Metrica transazioni con dimensioni "ResponseType" = "operazione riuscita" e "autenticazione" = "anonima" | |
| AuthorizationError | Metrica transazioni con dimensioni "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| ClientOtherError | Metrica transazioni con dimensioni "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metrica transazioni con dimensioni "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| NetworkError | Metrica transazioni con dimensioni "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Usare `aggregationType` ' Average ' invece di ' Last '. La metrica si applica solo ai servizi BLOB |
| SASAuthorizationError | Metrica delle transazioni con dimensioni "ResponseType" = "AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Metrica delle transazioni con dimensioni "ResponseType" = "ClientOtherError" e "Authentication" = "SAS" | |
| SASClientTimeOutError | Metrica delle transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "Authentication" = "SAS" | |
| SASNetworkError | Metrica delle transazioni con dimensioni "ResponseType" = "NetworkError" e "Authentication" = "SAS" | |
| SASServerOtherError | Metrica delle transazioni con dimensioni "ResponseType" = "ServerOtherError" e "Authentication" = "SAS" | |
| SASServerTimeOutError | Metrica delle transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "Authentication" = "SAS" | |
| SASSuccess | Metrica Transactions con Dimensions "ResponseType" = "success" e "Authentication" = "SAS" | |
| ServerOtherError | Metrica transazioni con dimensioni "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metrica transazioni con dimensioni "ResponseType" = "ServerTimeOutError"  | |
| Riuscito | Metrica transazioni con dimensioni "ResponseType" = "operazione riuscita" | |
| TotalBillableRequests| Transazioni | |
| TotalEgress | Egress | |
| TotalIngress | Ingress | |
| TotalRequests | Transazioni | |

### <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

Per Application Insights, le metriche equivalenti sono illustrate di seguito:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| basicExceptionBrowser.count | exceptions/browser|  Usare `aggregationType` "count" invece di "sum". |
| basicExceptionServer.count | exceptions/server| Usare `aggregationType` "count" invece di "sum".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| clientPerformance.total.value | browserTimings/totalDuration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| performanceCounter. available_bytes. Value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter. number_of_exceps_thrown_per_sec. Value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter. percentage_processor_time_normalized. Value | performanceCounters/processCpuPercentage|   |
| performanceCounter. percentage_processor_time. Value | performanceCounters/processCpuPercentage| Il valore soglia deve essere modificato in modo appropriato perché la metrica originale è stata applicata a tutti i core e la nuova metrica viene normalizzata in un core. Lo strumento di migrazione non modifica le soglie.  |
| performanceCounter. percentage_processor_total. Value | performanceCounters/processorCpuPercentage|   |
| performanceCounter. process_private_bytes. Value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Moltiplica la soglia originale di 1000 come unità per la metrica classica sono in secondi e per una nuova sono in milliSecondi.  |
| request.rate | richieste/frequenza|   |
| requestFailed.count | requests/failed| Usare `aggregationType` "count" invece di "sum".   |
| view.count | pageViews/count| Usare `aggregationType` "count" invece di "sum".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Per Cosmos DB, le metriche equivalenti sono illustrate di seguito:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Dimensioni dei dati | DataUsage| |
| Conteggio documenti | DocumentCount||
| Dimensioni dell'indice | IndexUsage||
| Addebito richiesta numero Mongo| MongoRequestCharge con Dimension "CommandName" = "count"||
| Frequenza richieste conteggio Mongo | MongoRequestsCount con Dimension "CommandName" = "count"||
| Costo richiesta di eliminazione Mongo | MongoRequestCharge con Dimension "CommandName" = "Delete"||
| Frequenza delle richieste di eliminazione Mongo | MongoRequestsCount con Dimension "CommandName" = "Delete"||
| Costo richiesta di inserimento Mongo | MongoRequestCharge con Dimension "CommandName" = "Insert"||
| Frequenza di richieste di inserimento Mongo | MongoRequestsCount con Dimension "CommandName" = "Insert"||
| Addebito richieste di query Mongo | MongoRequestCharge con Dimension "CommandName" = "Find"||
| Frequenza di richieste di query Mongo | MongoRequestsCount con Dimension "CommandName" = "Find"||
| Costo richiesta aggiornamento Mongo | MongoRequestCharge con Dimension "CommandName" = "Update"||
| Servizio non disponibile| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Modalità di creazione di gruppi di azioni equivalenti

Le regole di avviso classiche contengono posta elettronica, webhook, app per la logica e azioni Runbook associate alla regola di avviso. Le nuove regole di avviso usano i gruppi di azioni che possono essere riutilizzati in più regole di avviso. Lo strumento di migrazione crea il gruppo di azioni singolo per le stesse azioni indipendentemente dal numero di regole di avviso che usano l'azione. I gruppi di azioni creati dallo strumento di migrazione usano il formato di denominazione "Migrated_AG *".

> [!NOTE]
> Gli avvisi classici hanno inviato messaggi di posta elettronica localizzati in base alle impostazioni locali dell'amministratore classico quando vengono usati per notificare i ruoli di amministratore classico. I nuovi messaggi di avviso vengono inviati tramite gruppi di azioni e sono solo in lingua inglese.

## <a name="rollout-phases"></a>Fasi di implementazione

Lo strumento di migrazione sta implementando fasi per i clienti che usano le regole di avviso classiche. I proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica quando la sottoscrizione è pronta per la migrazione tramite lo strumento.

> [!NOTE]
> Poiché lo strumento viene implementato in fasi, è possibile notare che alcune sottoscrizioni non sono ancora pronte per essere migrate durante le fasi iniziali.

La maggior parte delle sottoscrizioni è attualmente contrassegnata come pronta per la migrazione. Solo le sottoscrizioni con avvisi classici sui tipi di risorse seguenti non sono ancora pronte per la migrazione.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft. Insights/Components

## <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Tutti gli utenti che dispongono del ruolo predefinito di collaboratore del monitoraggio a livello di sottoscrizione possono attivare la migrazione. Gli utenti che dispongono di un ruolo personalizzato con le autorizzazioni seguenti possono anche attivare la migrazione:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Oltre a disporre delle autorizzazioni precedenti, la sottoscrizione deve essere registrata anche con il provider di risorse Microsoft. AlertsManagement. Questa operazione è necessaria per eseguire correttamente la migrazione degli avvisi di anomalia degli errori in Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemi comuni e soluzioni

Dopo l' [attivazione della migrazione](alerts-using-migration-tool.md), si riceverà un messaggio di posta elettronica presso gli indirizzi forniti per notificare che la migrazione è stata completata o se è necessaria un'azione da parte dell'utente. In questa sezione vengono descritti alcuni problemi comuni e le relative modalità di gestione.

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune recenti modifiche apportate alle regole di avviso classiche nella sottoscrizione, non è possibile eseguire la migrazione della sottoscrizione. Questo problema è temporaneo. È possibile riavviare la migrazione dopo che lo stato della migrazione è stato spostato di nuovo **pronto per la migrazione** entro pochi giorni.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Blocco di criteri o ambito che impedisce la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi per le metriche e nuovi gruppi di azioni, quindi verranno eliminate le regole di avviso classiche. Tuttavia, esiste un criterio o un blocco dell'ambito che impedisce la creazione di risorse. A seconda del criterio o del blocco dell'ambito, non è stato possibile eseguire la migrazione di alcune o di tutte le regole. È possibile risolvere il problema rimuovendo temporaneamente il blocco o il criterio dell'ambito e attivando di nuovo la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)
