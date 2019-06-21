---
title: Comprendere il funzionamento dello strumento di migrazione volontaria per gli avvisi di monitoraggio di Azure
description: Comprendere il funzionamento dello strumento di migrazione di avvisi e risoluzione dei problemi.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295534"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendere il funzionamento dello strumento di migrazione

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati dal 31 agosto 2019 (è stato originariamente 30 giugno 2019). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso di classiche e che desiderano migrazione si attivano automaticamente.

Questo articolo spiega come funziona lo strumento di migrazione volontaria. Descrive inoltre informazioni su come risolvere alcuni problemi comuni.

> [!NOTE]
> A causa di un ritardo nell'implementazione dello strumento di migrazione, è stata la data di ritiro per la migrazione di avvisi classici [esteso al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di originariamente annunciata del 30 giugno 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regole di avviso classiche non sarà possibile eseguire la migrazione

> [!IMPORTANT]
> Avvisi del log attività (tra cui servizio gli avvisi di integrità) e gli avvisi del Log non sono interessati dalla migrazione. La migrazione si applica solo alle regole di avviso classiche descritte [qui](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Anche se lo strumento può eseguire la migrazione di quasi tutte le [regole di avviso classiche](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), esistono alcune eccezioni. Le regole di avviso seguente non sarà possibile eseguire la migrazione tramite lo strumento (o durante la migrazione automatica avvio 2019 settembre):

- Regole di avviso classica sulle metriche guest macchina virtuale (Windows e Linux). Vedere le [materiale sussidiario per ricreare tali regole di avviso in nuovi avvisi delle metriche](#guest-metrics-on-virtual-machines) più avanti in questo articolo.
- Regole di avviso classica sulle metriche di archiviazione classico. Vedere le [materiale sussidiario per il monitoraggio degli account di archiviazione classico](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regole di avviso classiche su alcune metriche di account di archiviazione. Visualizzare [dettagli](#storage-account-metrics) più avanti in questo articolo.
- Regole di avviso classiche su alcune metriche di Cosmos DB. Dettagli verranno aggiunto in un aggiornamento futuro.

Se la sottoscrizione include eventuali regole di questo tipo classiche, è necessario eseguirne la migrazione manualmente. Poiché non è possibile offrire una migrazione automatica, qualsiasi avvisi delle metriche classici, esistenti di questi tipi continueranno a funzionare fino a giugno 2020. Questa estensione offre ora per passare a nuovi avvisi. Tuttavia, è possibile creare nessun nuovo avviso classico dopo agosto 2019.

> [!NOTE]
> Oltre alle eccezioni elencate sopra, se le regole di avviso classiche non sono validi, ad esempio si trovano sulla [deprecato metriche](#classic-alert-rules-on-deprecated-metrics) o risorse che sono state eliminate, non verrà eseguite durante la migrazione volontaria. Verranno eliminati eventuali questo tipo non validi classici regole di avviso in caso di migrazione automatica.

### <a name="guest-metrics-on-virtual-machines"></a>Metriche guest nelle macchine virtuali

Prima di creare nuovi avvisi delle metriche per le metriche guest, le metriche guest devono essere inviate all'archivio di metriche personalizzate di monitoraggio di Azure. Seguire queste istruzioni per abilitare il sink di monitoraggio di Azure in impostazioni di diagnostica:

- [Abilitazione delle metriche guest per le macchine virtuali Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Abilitazione delle metriche guest per macchine virtuali Linux](collect-custom-metrics-linux-telegraf.md)

Dopo questi passaggi vengono eseguiti, è possibile creare nuovi avvisi delle metriche per le metriche guest. E dopo aver creato i nuovi avvisi delle metriche, è possibile eliminare gli avvisi classici.

### <a name="storage-account-metrics"></a>Metriche relative all'account di archiviazione

Tutti gli avvisi classici in account di archiviazione possono essere migrati, ad eccezione degli avvisi su queste metriche:

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

Avviso classico delle regole per le metriche percentuale devono essere migrate in base a [il mapping tra le metriche di archiviazione di vecchi e nuovi](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Le soglie dovrà essere modificata in modo appropriato in quanto la nuova metrica disponibile è assoluto.

Le regole di avviso di classiche AnonymousThrottlingError, SASThrottlingError e ThrottlingError devono essere suddivisa in due nuovi avvisi, poiché non esiste alcuna metrica combinata che offre le stesse funzionalità. Le soglie dovrà essere adattato in modo appropriato.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regole di avviso classica sulle metriche deprecate

Si tratta di regole di avviso classica sulle metriche di cui in precedenza erano supportate, ma alla fine sono state deprecate. Una piccola percentuale di clienti potrebbe avere regole di avviso di classiche non è valide su tali metriche. Poiché queste regole di avviso non sono validi, essi non verrà eseguita la migrazione.

| Tipo di risorsa| Metriche deprecate |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | BytesReceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Come vengono creati equivalente nuove regole di avviso e i gruppi di azioni

Lo strumento di migrazione converte le regole di avviso classiche equivalente nuove regole di avviso e i gruppi di azioni. Per la maggior parte delle regole di avviso classico, equivalente nuove regole di avviso sono sulla metrica stessa con le stesse proprietà, ad esempio `windowSize` e `aggregationType`. Tuttavia, esistono alcuni avviso classico le regole riguardano metriche con una metrica diversa, equivalente nel nuovo sistema. I seguenti principi si applicano alla migrazione di avvisi classici se non diversamente specificato nella sezione seguente:

- **Frequenza**: Definisce la frequenza di controlli di una regola di avviso di classica o nuova per la condizione. Il `frequency` nelle regole di avviso classiche non è configurabile dall'utente ed era sempre 5 minuti per tutti i tipi di risorse, ad eccezione dei componenti di Application Insights per il quale è 1 minuto. Frequenza di regole equivalente è inoltre impostata su 5 minuti e 1 min rispettivamente.
- **Tipo di aggregazione**: Definisce la modalità di aggregazione della metrica nell'intervallo di interesse. Il `aggregationType` anche sia lo stesso tra gli avvisi classici e nuovi avvisi per la maggior parte delle metriche. In alcuni casi, poiché la metrica è diversa tra gli avvisi classici e nuovi avvisi, equivalenti `aggregationType` o il `primary Aggregation Type` definito per la metrica viene usata.
- **Unità**: Proprietà della metrica in cui viene creato l'avviso. Alcune metriche equivalente avere diverse unità. La soglia viene regolata in modo appropriato in base alle esigenze. Ad esempio, se la metrica originale ha secondi come unità, ma equivalente nuova metrica è come unità di millisecondi, la soglia originale viene moltiplicata per 1000 per garantire in modo analogo.
- **Dimensioni della finestra**: Definisce la finestra in quale metrica dei dati vengono aggregati per confrontare rispetto al valore soglia. Per il livello standard `windowSize` valori, ad esempio 5 minuti, 15mins, 30mins, 1 ora, 3 ore, 6 ore, 12 ore, 1 giorno, non sono previste modifiche apportate per equivalente nuova regola di avviso. Per altri valori, la località più vicina `windowSize` viene scelto da utilizzare. Per la maggior parte dei clienti, non ha alcun impatto con questa modifica. Per una piccola percentuale di clienti, potrebbe essere necessario modificare la soglia per ottenere stesso identico comportamento.

Nelle sezioni seguenti, sarà trattata la metrica che hanno una metrica diversa, equivalente nel nuovo sistema. Qualsiasi metrica che rimane invariato per classiche e nuove regole di avviso non è elencato. È possibile trovare un elenco delle metriche supportate nel nuovo sistema [qui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Per i servizi di account di archiviazione, ad esempio blob, tabelle, file e code, le metriche seguenti sono mappate alle metriche equivalenti come illustrato di seguito:

| Metrica in avvisi classici | Metrica equivalente in nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Metrica di transazioni con dimensioni "ResponseType" = "AuthorizationError" e "Authentication" = "Anonimo"| |
| AnonymousClientOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ClientOtherError" e "Authentication" = "Anonimo" | |
| AnonymousClientTimeOutError| Metrica di transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "Authentication" = "Anonimo" | |
| AnonymousNetworkError | Metrica di transazioni con dimensioni "ResponseType" = "NetworkError" e "Authentication" = "Anonimo" | |
| AnonymousServerOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ServerOtherError" e "Authentication" = "Anonimo" | |
| AnonymousServerTimeOutError | Metrica di transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "Authentication" = "Anonimo" | |
| AnonymousSuccess | Metrica di transazioni con dimensioni "ResponseType" = "Success" e "Authentication" = "Anonimo" | |
| AuthorizationError | Metrica di transazioni con dimensioni "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Usare `aggregationType` 'Media' anziché 'last'. Metrica si applica solo ai servizi Blob |
| ClientOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Metrica di transazioni con dimensioni "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Usare `aggregationType` 'Media' anziché 'last'. Metrica si applica solo ai servizi Blob |
| NetworkError | Metrica di transazioni con dimensioni "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Usare `aggregationType` 'Media' anziché 'last'. Metrica si applica solo ai servizi Blob |
| SASAuthorizationError | Metrica di transazioni con dimensioni "ResponseType" = "AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ClientOtherError" e "Authentication" = "SAS" | |
| SASClientTimeOutError | Metrica di transazioni con dimensioni "ResponseType" = "ClientTimeOutError" e "Authentication" = "SAS" | |
| SASNetworkError | Metrica di transazioni con dimensioni "ResponseType" = "NetworkError" e "Authentication" = "SAS" | |
| SASServerOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ServerOtherError" e "Authentication" = "SAS" | |
| SASServerTimeOutError | Metrica di transazioni con dimensioni "ResponseType" = "ServerTimeOutError" e "Authentication" = "SAS" | |
| SASSuccess | Metrica di transazioni con dimensioni "ResponseType" = "Success" e "Authentication" = "SAS" | |
| ServerOtherError | Metrica di transazioni con dimensioni "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Metrica di transazioni con dimensioni "ResponseType" = "ServerTimeOutError"  | |
| Riuscito | Metrica di transazioni con dimensioni "ResponseType" = "Operazione riuscita" | |
| TotalBillableRequests| Transazioni | |
| TotalEgress | Egress | |
| TotalIngress | Ingress | |
| TotalRequests | Transazioni | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Per Application Insights, sono metriche equivalenti come illustrato di seguito:

| Metrica in avvisi classici | Metrica equivalente in nuovi avvisi | Commenti|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| basicExceptionBrowser.count | exceptions/browser|  Usare `aggregationType` 'count' anziché 'sum'. |
| basicExceptionServer.count | exceptions/server| Usare `aggregationType` 'count' anziché 'sum'.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| clientPerformance.total.value | browserTimings/totalDuration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Soglia dovrà essere modificato in modo appropriato come metrica originale è stato per tutti i core e nuova metrica è normalizzato secondo un core. Lo strumento di migrazione non modifica le soglie.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Moltiplicare soglia originale per 1000 come unità per la metrica classico sono espresse in secondi e per il nuovo uno sono in millisecondi.  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| Usare `aggregationType` 'count' anziché 'sum'.   |
| view.count | pageViews/count| Usare `aggregationType` 'count' anziché 'sum'.   |

### <a name="how-equivalent-action-groups-are-created"></a>Come vengono creati gruppi di azione equivalenti

Avviso di classico regole era l'indirizzo di posta elettronica, webhook, le azioni di app e il runbook per la logica associate all'avviso regola stessa. Nuove regole di avviso usano gruppi di azioni che possono essere riutilizzati tra più regole di avviso. Lo strumento di migrazione Crea gruppo di azione singola per le azioni stesse indipendentemente dal numero di regole avvisi è utilizzando l'azione. Gruppi di azioni creati dallo strumento di migrazione usano il formato di denominazione 'Migrated_AG *'.

## <a name="rollout-phases"></a>Fasi di implementazione

Lo strumento di migrazione è l'implementazione in fasi ai clienti che usano le regole di avviso di classiche. I proprietari delle sottoscrizioni riceverà un messaggio di posta elettronica quando la sottoscrizione è pronta per eseguire la migrazione usando lo strumento.

> [!NOTE]
> Poiché lo strumento viene implementato in fasi, si noterà che alcune sottoscrizioni non sono ancora pronti per eseguire la migrazione durante le fasi iniziali.

Attualmente, la maggior parte delle sottoscrizioni vengono contrassegnata come pronto per la migrazione. Solo le sottoscrizioni che contengono gli avvisi classici in tipi di risorsa seguenti non sono ancora pronte per la migrazione.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Chi può attivare la migrazione?

Qualsiasi utente che ha il ruolo predefinito collaboratore al monitoraggio a livello di sottoscrizione può attivare la migrazione. Gli utenti che dispongono di un ruolo personalizzato con le autorizzazioni seguenti possono attivare anche la migrazione:

- */lettura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Oltre di sopra delle autorizzazioni, la sottoscrizione deve inoltre essere registrata con provider di risorse Microsoft.AlertsManagement. Ciò è necessario per eseguire correttamente la migrazione di avvisi di errore delle anomalie in Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemi comuni e soluzioni

Dopo aver [attivare la migrazione](alerts-using-migration-tool.md), si riceverà la posta elettronica all'indirizzo di specificato per ricevere una notifica che la migrazione è stata completata o se è necessaria alcuna azione da parte dell'utente. In questa sezione descrive alcuni problemi comuni e come gestirli.

### <a name="validation-failed"></a>Convalida non riuscita

A causa di alcune modifiche recenti alle regole di avviso classiche nella sottoscrizione, è Impossibile eseguire la migrazione della sottoscrizione. Questo problema è temporaneo. È possibile riavviare la migrazione dopo che lo stato di migrazione consente di tornare **pronto per la migrazione** in pochi giorni.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Ambito o criteri di blocco impedisce di eseguire la migrazione delle regole

Come parte della migrazione, verranno creati nuovi avvisi delle metriche e i nuovi gruppi di azioni e quindi le regole di avviso di classiche verranno eliminate. Tuttavia, vi è un criterio o un ambito di blocco impedisce la creazione di risorse. A seconda del blocco di criteri o ambito, potrebbe non essere migrate alcune o tutte le regole. È possibile risolvere questo problema rimuovendo temporaneamente il blocco dell'ambito o i criteri e attivare nuovamente la migrazione.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Preparare la migrazione](alerts-prepare-migration.md)
