---
title: Comprendere il funzionamento dello strumento di migrazione volontaria per gli avvisi di Monitoraggio di AzureUnderstand how the voluntary migration tool works for Azure Monitor alerts
description: Comprendere il funzionamento dello strumento di migrazione degli avvisi e risolvere i problemi.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 8cc77d13567910797cd519ac193b848f3ea434da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274814"
---
# <a name="understand-how-the-migration-tool-works"></a>Informazioni sul funzionamento dello strumento di migrazione

Come [annunciato in precedenza,](monitoring-classic-retirement.md)gli avvisi classici in Monitoraggio di Azure vengono ritirati entro il 31 agosto 2019 (originariamente era il 30 giugno 2019). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso classiche e che vogliono attivare la migrazione autonomamente.

Questo articolo spiega come funziona lo strumento di migrazione volontaria. Descrive anche rimedi per alcuni problemi comuni.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di pensionamento per la migrazione degli avvisi classici è stata [estesa al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data originariamente annunciata del 30 giugno 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regole di avviso classiche di cui non verrà eseguita la migrazione

> [!IMPORTANT]
> Gli avvisi del log attività (inclusi gli avvisi di integrità del servizio) e gli avvisi del log non sono interessati dalla migrazione. La migrazione si applica solo alle regole di avviso classiche descritte [di seguito.](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)

Sebbene lo strumento possa eseguire la migrazione di quasi tutte le regole di [avviso classiche,](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)esistono alcune eccezioni. Le regole di avviso seguenti non verranno migrate utilizzando lo strumento (o durante la migrazione automatica a partire da settembre 2019):

- Regole di avviso classiche sulle metriche guest della macchina virtuale (sia Windows che Linux). Vedere le linee guida per ricreare tali regole di [avviso nei nuovi avvisi di metrica](#guest-metrics-on-virtual-machines) più avanti in questo articolo.
- Regole di avviso classiche sulle metriche di archiviazione classiche. Vedere le indicazioni per il monitoraggio degli [account di archiviazione classici](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regole di avviso classiche in alcune metriche dell'account di archiviazione. Vedere [i dettagli](#storage-account-metrics) più avanti in questo articolo.
- Regole di avviso classiche su alcune metriche cosmos DB. Vedere [i dettagli](#cosmos-db-metrics) più avanti in questo articolo.
- Regole di avviso classiche su tutte le macchine virtuali classiche e le metriche dei servizi cloud (Microsoft.ClassicCompute/virtualMachines e Microsoft.ClassicCompute/domainNames/slots/roles). Vedere [i dettagli](#classic-compute-metrics) più avanti in questo articolo.

Se l'abbonamento include regole classiche di questo tipo, è necessario eseguirne la migrazione manualmente. Poiché non è possibile fornire una migrazione automatica, tutti gli avvisi di metrica classica esistenti di questi tipi continueranno a funzionare fino a giugno 2020. Questa estensione ti dà il tempo di passare a nuovi avvisi. È inoltre possibile continuare a creare nuovi avvisi classici sulle eccezioni sopra elencate fino a giugno 2020. Tuttavia, per tutto il resto, non è possibile creare nuovi avvisi classici dopo agosto 2019.

> [!NOTE]
> Oltre alle eccezioni elencate sopra, se le regole di avviso classiche non sono valide, ovvero sono su [metriche deprecate](#classic-alert-rules-on-deprecated-metrics) o risorse che sono state eliminate, non verranno migrate e non saranno disponibili dopo il ritiro del servizio.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche guest nelle macchine virtualiGuest metrics on virtual machines

Prima di poter creare nuovi avvisi di metrica nelle metriche guest, le metriche guest devono essere inviate all'archivio delle metriche personalizzate di Monitoraggio di Azure.Before you can create new metric alerts on guest metrics, the guest metrics must be sent to the Azure Monitor custom metrics store. Seguire queste istruzioni per abilitare il sink di Monitoraggio di Azure nelle impostazioni di diagnostica:Follow these instructions to enable the Azure Monitor sink in diagnostic settings:

- [Abilitazione delle metriche guest per le macchine virtuali WindowsEnabling guest metrics for Windows VMs](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Abilitazione delle metriche guest per le macchine virtuali LinuxEnabling guest metrics for Linux VMs](collect-custom-metrics-linux-telegraf.md)

Al termine di questi passaggi, è possibile creare nuovi avvisi di metrica nelle metriche guest. Dopo aver creato nuovi avvisi di metrica, è possibile eliminare gli avvisi classici.

### <a name="storage-account-metrics"></a>Metriche relative all'account di archiviazione

È possibile eseguire la migrazione di tutti gli avvisi classici sugli account di archiviazione, ad eccezione degli avvisi sulle metriche seguenti:All classic alerts on storage accounts can be migrated except alerts on these metrics:

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

Le regole di avviso classiche nelle metriche Percentuale devono essere migrate in base [al mapping tra metriche di archiviazione vecchie e nuove.](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics) Le soglie dovranno essere modificate in modo appropriato perché la nuova metrica disponibile è assoluta.

Le regole di avviso classiche su AnonymousThrottlingError, SASThrottlingError e ThrottlingError devono essere suddivise in due nuovi avvisi perché non esiste una metrica combinata che fornisca la stessa funzionalità. Le soglie dovranno essere adattate in modo appropriato.

### <a name="cosmos-db-metrics"></a>Metriche di Cosmos DB

È possibile eseguire la migrazione di tutti gli avvisi classici sulle metriche Cosmos DB, ad eccezione degli avvisi relativi a queste metriche:All classic alerts on Cosmos DB metrics can be migrated except alerts on these metrics:

- Media di richieste al secondo
- Livello di coerenza
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Internal Server Error
- Numero massimo di RUPM consumato al minutoMax RUPM Consumed Per Minute
- Numero massimo di RU al secondoMax RUs Per Second
- Richieste non riuscite del conteggio Mongo
- Richieste non riuscite di eliminazione mongoMongo Delete Failed Requests
- Richieste di inserimento Mongo non riuscite
- Mongo Altre richieste non riuscite
- Mongo Altro addebito richiesta
- Mongo Altra frequenza richieste
- Richieste di query Mongo non riuscite
- Richieste di aggiornamento Mongo non riuscite
- Latenza di lettura osservata
- Latenza scrittura osservataObserved Write Latency
- Disponibilità del servizio
- Capacità di archiviazione
- Richieste limitate
- Totale richieste

Media richieste al secondo, Livello di coerenza, Numero massimo di RUPM consumati al minuto, Ru massimo al secondo, Latenza lettura osservata, Latenza scrittura osservata, Capacità di archiviazione non sono attualmente disponibili nel [nuovo sistema.](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)

Gli avvisi sulle metriche delle richieste come Http 2xx, Http 3xx, Http 400, Http 401, Internal Server Error, Service Availability, Throttled Requests e Total Requests are not migrated because the way le requests are counted is different between classic metrics and new metrics. Gli avvisi su questi dovranno essere ricreati manualmente con soglie regolate.

Le metriche degli avvisi sulle richieste non riuscite di Mongo devono essere suddivise in più avvisi perché non esiste una metrica combinata che fornisca la stessa funzionalità. Le soglie dovranno essere adattate in modo appropriato.

### <a name="classic-compute-metrics"></a>Metriche di calcolo classiche

Tutti gli avvisi sulle metriche di calcolo classiche non verranno migrati usando lo strumento di migrazione poiché le risorse di calcolo classiche non sono ancora supportate con i nuovi avvisi. Il supporto per nuovi avvisi per questi tipi di risorse verrà aggiunto in futuro. Una volta che è disponibile, i clienti devono ricreare nuove regole di avviso equivalenti in base alle loro regole di avviso classiche prima di giugno 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regole di avviso classiche sulle metriche deprecate

Si tratta di regole di avviso classiche sulle metriche che in precedenza erano supportate ma che alla fine erano deprecate. Una piccola percentuale di clienti potrebbe avere regole di avviso classiche non valide su tali metriche. Poiché queste regole di avviso non sono valide, non verranno migrate.

| Tipo di risorsa| Metriche deprecate |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, limitazione delle richieste, dtu_consumption_percent storage_used |
| Microsoft.Web/hostingAmbienti/pool multiruolo | mediamemoriaworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytericevuti, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Creazione di nuove regole di avviso e gruppi di azioni equivalenti

Lo strumento di migrazione converte le regole di avviso classiche in nuove regole di avviso e gruppi di azioni equivalenti. Per la maggior parte delle regole di avviso classiche, le `windowSize` `aggregationType`nuove regole di avviso equivalenti si trovano nella stessa metrica con le stesse proprietà, ad esempio e . Tuttavia, esistono alcune regole di avviso classiche relative alle metriche con una metrica equivalente diversa nel nuovo sistema. I principi seguenti si applicano alla migrazione degli avvisi classici, a meno che non venga specificato nella sezione seguente:

- **Frequenza**: Definisce la frequenza con cui una regola di avviso classica o nuova controlla la condizione. Le `frequency` regole di avviso in classiche non erano configurabili dall'utente ed erano sempre 5 min per tutti i tipi di risorse ad eccezione dei componenti di Application Insights per i quali era 1 min. Quindi la frequenza delle regole equivalenti è anche impostata su 5 min e 1 min rispettivamente.
- **Tipo di aggregazione**: Definisce la modalità di aggregazione della metrica nella finestra di interesse. Lo `aggregationType` stesso è anche lo stesso tra avvisi classici e nuovi avvisi per la maggior parte delle metriche. In alcuni casi, poiché la metrica è `aggregationType` diversa `primary Aggregation Type` tra gli avvisi classici e i nuovi avvisi, viene utilizzato un equivalente o il definito per la metrica.
- **Unità**: Proprietà della metrica in cui viene creato l'avviso. Alcune metriche equivalenti hanno unità diverse. La soglia viene regolata in modo appropriato in base alle esigenze. Ad esempio, se la metrica originale ha secondi come unità ma la metrica nuova equivalente ha milliSeconds come unità, la soglia originale viene moltiplicata per 1000 per garantire lo stesso comportamento.
- **Dimensione finestra**: Definisce la finestra su cui vengono aggregati i dati delle metriche per il confronto con la soglia. Per `windowSize` i valori standard come 5mins, 15mins, 30mins, 1 hour, 3 hours, 6 ore, 12 ore, 1 giorno, non viene apportata alcuna modifica per una nuova regola di avviso equivalente. Per altri valori, `windowSize` viene scelto il più vicino da utilizzare. Per la maggior parte dei clienti, non vi è alcun impatto con questa modifica. Per una piccola percentuale di clienti, potrebbe essere necessario modificare la soglia per ottenere lo stesso comportamento esatto.

Nelle sezioni seguenti vengono descritte in dettaglio le metriche con una metrica equivalente diversa nel nuovo sistema. Qualsiasi metrica che rimane invariata per le regole di avviso classiche e nuove non è elencata. È possibile trovare un elenco delle metriche supportate nel nuovo sistema [qui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/servizi

Per i servizi dell'account di archiviazione, ad esempio BLOB, tabella, file e coda, le metriche seguenti vengono mappate alle metriche equivalenti, come illustrato di seguito:For Storage account services like blob, table, file and queue, the following metrics are mapped to equivalent metrics as shown below:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrica delle transazioni con le dimensioni "ResponseType" , "AuthorizationError" e "Authentication" -"Anonymous"| |
| AnonymousClientOtherError | Metrica delle transazioni con le dimensioni "ResponseType" - "ClientOtherError" e "Autenticazione" "Anonimo" | |
| AnonymousClientTimeOutError (AnonymousClientTimeOutError)| Metrica delle transazioni con le dimensioni "ResponseType" , "ClientTimeOutError" e "Authentication" -"Anonymous" | |
| AnonymousNetworkError | Metrica delle transazioni con le dimensioni "ResponseType""NetworkError" e "Authentication" -"Anonymous" | |
| AnonymousServerOtherError | Metrica delle transazioni con le dimensioni "ResponseType""ServerOtherError" e "Authentication" "Anonymous" | |
| AnonymousServerTimeOutError (Errore AnonymousServerTimeOutError) | Metrica delle transazioni con le dimensioni "ResponseType""ServerTimeOutError" e "Authentication" -"Anonymous" | |
| AnonymousSuccess | Metrica delle transazioni con le dimensioni "ResponseType""Success" e "Authentication" -"Anonymous" | |
| AuthorizationError | Metrica delle transazioni con le dimensioni "ResponseType" "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Utilizzare `aggregationType` 'media' anziché 'ultimo'. La metrica si applica solo ai servizi BLOB |
| ClientOtherError | Metrica delle transazioni con le dimensioni "ResponseType" -"ClientOtherError"  | |
| ClientTimeoutError | Metrica delle transazioni con le dimensioni "ResponseType" -"ClientTimeOutError" | |
| ContainerCount | ContainerCount | Utilizzare `aggregationType` 'media' anziché 'ultimo'. La metrica si applica solo ai servizi BLOB |
| NetworkError | Metrica delle transazioni con le dimensioni "ResponseType" -"NetworkError" | |
| ObjectCount | BlobCount| Utilizzare `aggregationType` 'media' anziché 'ultimo'. La metrica si applica solo ai servizi BLOB |
| SASAuthorizationError | Metrica delle transazioni con le dimensioni "ResponseType" - "AuthorizationError" e "Authentication" "SAS" | |
| SASClientOtherError | Metrica delle transazioni con le dimensioni "ResponseType" - "ClientOtherError" e "Autenticazione" "SAS" | |
| SASClientTimeOutError (Errore SASClientTimeOutError) | Metrica delle transazioni con le dimensioni "ResponseType" - "ClientTimeOutError" e "Autenticazione" "SAS" | |
| SASNetworkError | Metrica delle transazioni con le dimensioni "ResponseType""NetworkError" e "Authentication" -"SAS" | |
| SASServerOtherError | Metrica delle transazioni con le dimensioni "ResponseType""ServerOtherError" e "Authentication" "SAS" | |
| SASServerTimeOutError (SASServerTimeOutError) | Metrica delle transazioni con le dimensioni "ResponseType""ServerTimeOutError" e "Authentication" -"SAS" | |
| SASSuccess | Metrica delle transazioni con le dimensioni "ResponseType""Success" e "Authentication" -"SAS" | |
| ServerOtherError | Metrica delle transazioni con le dimensioni "ResponseType" "ServerOtherError" | |
| ServerTimeOutError (Errore server) | Metrica delle transazioni con le dimensioni "ResponseType" -"ServerTimeOutError"  | |
| Operazione completata | Metrica delle transazioni con le dimensioni "ResponseType" - "Success" | |
| TotalBillableRequests| Transazioni | |
| TotalEgress | Egress | |
| TotalIngress | Dati in ingresso | |
| TotalRequests | Transazioni | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Per Application Insights, le metriche equivalenti sono illustrate di seguito:For Application Insights, equivalent metrics are as shown below:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.valore | availabilityRisultati/availabilityPercentuale|   |
| availability.durationMetric.value | availabilityResults/duration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| basicExceptionBrowser.count | exceptions/browser|  Utilizzare `aggregationType` 'count' anziché 'sum'. |
| basicExceptionServer.count | exceptions/server| Utilizzare `aggregationType` 'count' anziché 'sum'.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| clientPerformance.networkConnection.valore | browserTimings/networkDuration|  Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| clientPerformance.total.value | browserTimings/totalDuration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| performanceCounter.available_bytes.valore | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.valore | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.valore | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.valore | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.valore | performanceCounters/processCpuPercentage| La soglia dovrà essere modificata in modo appropriato poiché la metrica originale era in tutti i core e la nuova metrica viene normalizzata a un core. Lo strumento di migrazione non modifica le soglie.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.valore | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.valore | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.valore | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.valore | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Moltiplicare la soglia originale per 1000 come unità per la metrica classica in secondi e per una nuova in milliSeconds.  |
| richiesta.tasso | richieste/tasso|   |
| requestFailed.count | requests/failed| Utilizzare `aggregationType` 'count' anziché 'sum'.   |
| view.count | pageViews/count| Utilizzare `aggregationType` 'count' anziché 'sum'.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Per Cosmos DB, le metriche equivalenti sono come illustrato di seguito:For Cosmos DB, equivalent metrics are as shown below:

| Metrica negli avvisi classici | Metrica equivalente nei nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Dimensioni dei dati | DataUsage| |
| Conteggio documenti | DocumentCount||
| Dimensioni dell'indice | IndexUsage||
| Addebito richiesta conteggio Mongo| MongoRequestCharge con la dimensione "CommandName" - "count"||
| Frequenza richieste conteggio Mongo | MongoRequestsCount con la dimensione "CommandName" - "count"||
| Addebito richiesta eliminazione Mongo | MongoRequestCharge con la dimensione "CommandName" - "delete"||
| Frequenza richieste eliminazione mongo | MongoRequestsCount con dimensione "CommandName" - "delete"||
| Addebito richiesta inserimento Mongo | MongoRequestCharge con la quota "CommandName" - "inserto"||
| Frequenza richieste inserimento Mongo | MongoRequestsCount con la dimensione "CommandName" - "insert"||
| Addebito richiesta query Mongo | MongoRequestCharge con la dimensione "CommandName" - "find"||
| Frequenza richieste query Mongo | MongoRequestsCount con la dimensione "CommandName" - "find"||
| Addebito richiesta aggiornamento Mongo | MongoRequestCharge con la dimensione "CommandName" - "update"||
| Servizio non disponibile| ServiceAvailability||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Modalità di creazione di gruppi di azioni equivalenti

Le regole di avviso classiche avevano email, webhook, app per la logica e azioni del runbook legate alla regola di avviso stessa. Le nuove regole di avviso utilizzano gruppi di azioni che possono essere riutilizzati in più regole di avviso. Lo strumento di migrazione crea un singolo gruppo di azioni per le stesse azioni indipendentemente dal numero di regole di avviso che utilizzano l'azione. I gruppi di azioni creati dallo strumento di migrazione utilizzano il formato di denominazione 'Migrated_AG'.

> [!NOTE]
> Gli avvisi classici inviano messaggi di posta elettronica localizzati in base alle impostazioni locali dell'amministratore classico quando vengono utilizzati per notificare i ruoli di amministratore classici. Le nuove e-mail di avviso vengono inviate tramite gruppi di azioni e sono solo in inglese.

## <a name="rollout-phases"></a>Fasi di implementazione

Lo strumento di migrazione viene distribuito in fasi ai clienti che utilizzano le regole di avviso classiche. I proprietari dell'abbonamento riceveranno un messaggio di posta elettronica quando la sottoscrizione è pronta per la migrazione tramite lo strumento.

> [!NOTE]
> Poiché lo strumento viene implementato in fasi, è possibile che alcune sottoscrizioni non siano ancora pronte per essere migrate durante le prime fasi.

La maggior parte delle sottoscrizioni sono attualmente contrassegnate come pronte per la migrazione. Solo le sottoscrizioni con avvisi classici per i tipi di risorse seguenti non sono ancora pronte per la migrazione.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Qualsiasi utente che ha il ruolo predefinito di Collaboratore di monitoraggio a livello di sottoscrizione può attivare la migrazione. Gli utenti che dispongono di un ruolo personalizzato con le autorizzazioni seguenti possono anche attivare la migrazione:Users who have a custom role with the following permissions can also trigger the migration:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/.
- Microsoft.AlertsManagement/smartDetectorAlertRules/

> [!NOTE]
> Oltre a disporre di autorizzazioni precedenti, la sottoscrizione deve essere registrata anche con il provider di risorse Microsoft.AlertsManagement.In addition to having above permissions, your subscription should additionally be registered with Microsoft.AlertsManagement resource provider. Questa operazione è necessaria per eseguire correttamente la migrazione degli avvisi anomalie di errore in Application Insights.This is required to successfully migrate Failure Anomaly alerts on Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemi e rimedi comuni

Dopo aver [attivato la migrazione,](alerts-using-migration-tool.md)si riceverà un messaggio di posta elettronica agli indirizzi forniti per notificare che la migrazione è stata completata o se è necessaria un'azione da parte dell'utente. In questa sezione vengono descritti alcuni problemi comuni e come gestirli.

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune modifiche recenti alle regole di avviso classiche nella sottoscrizione, non è possibile eseguire la migrazione della sottoscrizione. Questo problema è temporaneo. È possibile riavviare la migrazione dopo lo spostamento dello stato della migrazione **Pronto per la migrazione** in pochi giorni.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Blocco dell'ambito che ci impedisce la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi di metrica e nuovi gruppi di azioni, quindi verranno eliminate le regole di avviso classiche. Tuttavia, un blocco dell'ambito può impedirci di creare o eliminare risorse. A seconda del blocco dell'ambito, non è stato possibile eseguire la migrazione di alcune o tutte le regole. È possibile risolvere questo problema rimuovendo il blocco dell'ambito per la sottoscrizione, il gruppo di risorse o la risorsa, elencato nello strumento di [migrazione,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)e attivando nuovamente la migrazione. Il blocco dell'ambito non può essere disabilitato e deve essere rimosso per tutta la durata del processo di migrazione. [Ulteriori informazioni sulla gestione dei blocchi](../../azure-resource-manager/management/lock-resources.md#portal)di ambito .

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Criteri con effetto "Nega" che ci impediscono di eseguire la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi di metrica e nuovi gruppi di azioni, quindi verranno eliminate le regole di avviso classiche. Tuttavia, una politica può impedirci di creare risorse. A seconda del criterio, non è stato possibile eseguire la migrazione di alcune o tutte le regole. I criteri che bloccano il processo sono elencati nello strumento di [migrazione.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) Risolvere il problema:

- Esclusione delle sottoscrizioni o dei gruppi di risorse per la durata del processo di migrazione dall'assegnazione dei criteri. [Ulteriori informazioni sulla gestione dell'ambito](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)di esclusione dei criteri .
- Rimozione o modifica dell'effetto su "audit" o "append" (che, ad esempio, può risolvere i problemi relativi ai tag mancanti). [Ulteriori informazioni sulla gestione dei criteri effetto](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)
