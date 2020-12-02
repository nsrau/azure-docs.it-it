---
title: Come monitorare l'analisi delle sinapsi con monitoraggio di Azure
description: Informazioni su come monitorare l'area di lavoro di sinapsi Analytics usando le metriche, gli avvisi e i log di monitoraggio di Azure
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f495de13c20aa2953f55e2837fab15a6c62e1083
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467765"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Usare monitoraggio di Azure con l'area di lavoro di Azure sinapsi Analytics

Le applicazioni cloud sono complesse e hanno molte parti mobili. I monitoraggi forniscono i dati per garantire che le applicazioni restino operativi in uno stato integro. I monitoraggi consentono inoltre di evitare potenziali problemi e risolvere i problemi precedenti. È possibile usare i dati di monitoraggio per ottenere informazioni approfondite sulle applicazioni. Queste informazioni consentono di migliorare le prestazioni e la gestibilità delle applicazioni. Consente inoltre di automatizzare le azioni che altrimenti richiedono un intervento manuale.

Monitoraggio di Azure offre metriche, avvisi e log dell'infrastruttura di livello base per la maggior parte dei servizi di Azure. I log di diagnostica di Azure sono generati da una risorsa e offrono dati completi e frequenti sul funzionamento della risorsa stessa. Azure sinapsi Analytics può scrivere log di diagnostica in monitoraggio di Azure.

Per altre informazioni, vedere [Panoramica di Monitoraggio di Azure](../../azure-monitor/overview.md).

## <a name="metrics"></a>Metriche

Con il monitoraggio è possibile ottenere visibilità sulle prestazioni e l'integrità dei carichi di lavoro di Azure. Il tipo più importante di dati di monitoraggio è la metrica, definita anche contatore delle prestazioni. Le metriche vengono emesse dalla maggior parte delle risorse di Azure. Il monitoraggio offre diversi modi per configurare e utilizzare queste metriche per il monitoraggio e la risoluzione dei problemi.

Per accedere a queste metriche, seguire le istruzioni riportate nella [piattaforma dati di monitoraggio di Azure](../../azure-monitor/platform/data-platform.md).

### <a name="workspace-level-metrics"></a>Metriche a livello di area di lavoro

Di seguito sono riportate alcune delle metriche emesse dalle aree di lavoro:

| **Metrica**                           | **Categoria metrica, nome visualizzato**                  | **Unità** | **Tipi di aggregazione** | **Descrizione**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integrazione, metrica delle esecuzioni di attività                     | Conteggio    | Sum (impostazione predefinita), count                | Numero totale di esecuzioni di attività eseguite/terminate in una finestra di 1 minuto. </br></br> Utilizzare la dimensione risultato della metrica per filtrare in base allo stato finale riuscito, non riuscito o annullato.|
| IntegrationPipelineRunsEnded         | Integrazione, metriche delle esecuzioni di pipeline                     | Conteggio    | Sum (impostazione predefinita), count                | Numero totale di esecuzioni di pipeline eseguite/terminate in una finestra di 1 minuto. </br></br> Utilizzare la dimensione risultato della metrica per filtrare in base allo stato finale riuscito, non riuscito o annullato. |
| IntegrationTriggerRunsEnded          | Integrazione, metriche delle esecuzioni di trigger                      | Conteggio    | Sum (impostazione predefinita), count                | Numero totale di esecuzioni di trigger eseguite/terminate in una finestra di 1 minuto. </br></br> Utilizzare la dimensione risultato della metrica per filtrare in base allo stato finale riuscito, non riuscito o annullato. |
| BuiltinSqlPoolDataProcessedBytes     | Pool SQL predefinito, dati elaborati (byte) | Byte | Sum (impostazione predefinita) | Quantità di dati elaborati dal pool SQL senza server incorporato. |
| BuiltinSqlPoolLoginAttempts          | Pool SQL predefinito, tentativi di accesso | Conteggio | Sum (impostazione predefinita) | Numero di tentativi di accesso per il pool SQL senza server incorporato. |
| BuiltinSqlPoolDataRequestsEnded      | Pool SQL predefinito, richieste terminate (byte) | Conteggio | Sum (impostazione predefinita) | Numero di richieste SQL finite per il pool SQL senza server incorporato. </br></br> Utilizzare la dimensione risultato della metrica per filtrare in base allo stato finale. |

### <a name="dedicated-sql-pool-metrics"></a>Metriche del pool SQL dedicato

Di seguito sono riportate alcune delle metriche emesse dai pool SQL dedicati:

| **Metrica**                           | **Nome visualizzato**                  | **Unità** | **Tipi di aggregazione** | **Descrizione**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | Limite DWU                       | Conteggio   | Max (impostazione predefinita), min, AVG | Dimensioni configurate del pool SQL |
| DWUUsed                             | Uso DWU                        | Conteggio   | Max (impostazione predefinita), min, AVG | Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato per limite DWU * DWU% |
| DWUUsedPercent                      | Percentuale di utilizzo di DWU             | Percentuale | Max (impostazione predefinita), min, AVG | Rappresenta una rappresentazione di alto livello dell'utilizzo nel pool SQL. Misurato prendendo il massimo tra la percentuale di CPU e la percentuale di i/o dei dati |
| ConnectionsBlockedByFirewall        | Connessioni bloccate dal firewall | Conteggio   | Sum (impostazione predefinita)   | Numero di connessioni bloccate dalle regole del firewall. Rivedere i criteri di controllo di accesso per il pool SQL e monitorare queste connessioni se il conteggio è elevato |
| AdaptiveCacheHitPercent             | Percentuale riscontri cache adattiva   | Percentuale | Max (impostazione predefinita), min, AVG | Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale di riscontri nella cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache. |
| AdaptiveCacheUsedPercent            | Percentuale utilizzata della cache adattiva  | Percentuale | Max (impostazione predefinita), min, AVG | Misura il modo in cui i carichi di lavoro utilizzano la cache adattiva. Usare questa metrica con la metrica percentuale utilizzata della cache per determinare se ridimensionare la capacità aggiuntiva o rieseguire i carichi di lavoro per idratare la cache. |
| LocalTempDBUsedPercent              | Percentuale di utilizzo del tempdb locale    | Percentuale | Max (impostazione predefinita), min, AVG | Utilizzo tempdb locale in tutti i nodi di calcolo: i valori vengono emessi ogni cinque minuti |
| MemoryUsedPercent                   | Percentuale di memoria utilizzata          | Percentuale | Max (impostazione predefinita), min, AVG | Utilizzo della memoria in tutti i nodi del pool SQL |
| CPUPercent                          | Percentuale di utilizzo CPU             | Percentuale | Max (impostazione predefinita), min, AVG | Utilizzo della CPU in tutti i nodi del pool SQL |
| Connessioni                         | Connessioni                     | Conteggio   | Sum (impostazione predefinita)  | Numero totale di accessi al pool SQL |
| ActiveQueries                      | Query attive                 | Conteggio   | Sum (impostazione predefinita)   | Query attive. Utilizzando questa metrica non filtrata e unsplit, vengono visualizzate tutte le query attive in esecuzione nel sistema |
| QueuedQueries                      | Query in coda                  | Conteggio   | Sum (impostazione predefinita)   | Conteggio cumulativo delle richieste accodate dopo il raggiungimento del limite massimo di concorrenza |
| WLGActiveQueries                   | Query attive del gruppo di carico di lavoro   | Conteggio   | Sum (impostazione predefinita)   | Query attive all'interno del gruppo di carico di lavoro. Utilizzando questa metrica non filtrata e unsplit, vengono visualizzate tutte le query attive in esecuzione nel sistema |
| WLGActiveQueriesTimeouts           | Timeout query del gruppo di carico di lavoro   | Conteggio   | Sum (impostazione predefinita)   | Query per il gruppo del carico di lavoro di cui si è verificato il timeout. I timeout delle query segnalati da questa metrica sono solo dopo l'avvio dell'esecuzione della query (non è incluso il tempo di attesa a causa del blocco o delle attese delle risorse) |
| WLGQueuedQueries                   | Query accodate del gruppo di carico di lavoro   | Conteggio   | Sum (impostazione predefinita)   | Conteggio cumulativo delle richieste accodate dopo il raggiungimento del limite massimo di concorrenza |
| WLGAllocationBySystemPercent        | Allocazione del gruppo di carico di lavoro per percentuale di sistema | Percentuale | Max (impostazione predefinita), min, AVG, Sum | Percentuale di allocazione delle risorse rispetto all'intero sistema |
| WLGAllocationByEffectiveCapResourcePercent   | Allocazione del gruppo di carico di lavoro per percentuale risorse massima | Percentuale | Max (impostazione predefinita), min, AVG | Visualizza la percentuale di allocazione delle risorse rispetto alla percentuale effettiva delle risorse CAP per gruppo di carico di lavoro. Questa metrica fornisce l'utilizzo effettivo del gruppo di carico di lavoro |
| WLGEffectiveCapResourcePercent      | Percentuale limite di risorse effettiva  | Percentuale | Max (impostazione predefinita), min, AVG | Percentuale effettiva della risorsa Cap per il gruppo del carico di lavoro. Se sono presenti altri gruppi di carico di lavoro con min_percentage_resource > 0, il effective_cap_percentage_resource viene ridotto proporzionalmente |
| WLGEffectiveMinResourcePercent      | Percentuale risorse minima effettiva  | Percentuale | Max (impostazione predefinita), min, AVG, Sum | L'impostazione percentuale effettiva minima delle risorse può prendere in considerazione il livello di servizio e le impostazioni del gruppo di carico di lavoro. Il min_percentage_resource effettivo può essere modificato in un livello superiore rispetto ai livelli di servizio più bassi |

### <a name="apache-spark-pool-metrics"></a>Metriche del pool di Apache Spark

Di seguito sono riportate alcune delle metriche emesse dai pool di Apache Spark:

| **Metrica**                           | **Categoria metrica, nome visualizzato**                  | **Unità** | **Tipi di aggregazione** | **Descrizione**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Applicazioni Apache Spark terminate  | Conteggio | Sum (impostazione predefinita) | Numero di applicazioni del pool di Apache Spark terminate |
| BigDataPoolAllocatedCores     | Numero di Vcore allocati al pool di Apache Spark                 | Conteggio | Max (impostazione predefinita), min, AVG | VCore allocati per un pool di Apache Spark |
| BigDataPoolAllocatedMemory    | Quantità di memoria (GB) allocata al pool di Apache Spark            | Conteggio | Max (impostazione predefinita), min, AVG | Memoria allocata per il pool di Apache Spark (GB) |
| BigDataPoolApplicationsActive | Applicazioni Apache Spark attive | Conteggio | Max (impostazione predefinita), min, AVG | Numero di applicazioni del pool di Apache Spark attive |

## <a name="alerts"></a>Avvisi

Accedere al portale di Azure e selezionare **monitoraggio**  >  **avvisi** per creare avvisi.

### <a name="create-alerts"></a>Creare avvisi

1. Selezionare **+ Nuova regola di avviso** per creare un nuovo avviso.

1. Definire la **condizione di avviso** per specificare quando deve essere attivato l'avviso.

    > [!NOTE]
    > Assicurarsi di selezionare **tutto** nell'elenco a discesa **Filtra per tipo di risorsa** .

1. Definire i **Dettagli dell'avviso** per specificare ulteriormente la modalità di configurazione dell'avviso.

1. Definire il **gruppo di azioni** per determinare gli utenti che devono ricevere gli avvisi (e come).

## <a name="logs"></a>Log

### <a name="workspace-level-logs"></a>Log a livello di area di lavoro

Ecco i log emessi dalle aree di lavoro di Azure sinapsi Analytics:

| Nome tabella Log Analytics | Nome categoria log                 | Descrizione |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Richieste API del gateway di Azure sinapsi. |
| SynapseRbacOperations         | SynapseRbacOperations          | Operazioni di controllo degli accessi in base al ruolo di Azure sinapsi (SRBAC). |
| SynapseBuiltinSqlPoolRequestsEnded   | BuiltInSqlReqsEnded    | Le richieste SQL senza server predefinite di Azure sinapsi sono terminate. |

### <a name="dedicated-sql-pool-logs"></a>Log del pool SQL dedicati

Di seguito sono riportati i log generati dai pool SQL dedicati:

| Nome tabella Log Analytics        | Nome categoria log             | Descrizione |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informazioni sulle richieste/query SQL in un pool SQL dedicato a sinapsi di Azure.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informazioni sui ruoli di lavoro che completano i passaggi DMS in un pool SQL dedicato a sinapsi di Azure.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informazioni sui passaggi della richiesta che compongono una determinata richiesta/query SQL in un pool SQL dedicato a sinapsi di Azure.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Informazioni sulle distribuzioni di query dei passaggi di richieste/query SQL in un pool SQL dedicato a sinapsi di Azure.
| SynapseSqlPoolWaits         | In attesa        | Informazioni sugli stati di attesa rilevati durante l'esecuzione di una richiesta/query SQL in un pool SQL dedicato a sinapsi di Azure, inclusi i blocchi e le attese sulle code di trasmissione.

Per ulteriori informazioni su questi log, vedere le seguenti informazioni:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="apache-spark-pool-log"></a>Log del pool di Apache Spark

Ecco il log emesso dai pool di Apache Spark:

| Nome tabella Log Analytics               | Nome categoria log              | Descrizione                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informazioni sulle applicazioni Apache Spark terminate |

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica

Usare le impostazioni di diagnostica per configurare i log di diagnostica per le risorse non di calcolo. Le impostazioni per un controllo risorsa includono le funzionalità seguenti:

* Specificano la posizione in cui vengono inviati i log di diagnostica. Gli esempi includono un account di archiviazione di Azure, un hub eventi di Azure o i log di monitoraggio.
* Specificano le categorie di log da inviare.
* Specificano per quanto tempo ogni categoria di log deve essere conservata in un account di archiviazione.
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 2.147.483.647.
* Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. Questa condizione può verificarsi, ad esempio, quando vengono selezionate solo le opzioni Hub eventi o registri di monitoraggio.
* I criteri di conservazione vengono applicati al giorno. Il limite tra i giorni si verifica a mezzanotte UTC (Coordinated Universal Time). Alla fine di un giorno, i log da giorni che esulano dai criteri di conservazione vengono eliminati. Se, ad esempio, si dispone di un criterio di conservazione di un giorno, all'inizio di oggi vengono eliminati i log da prima di ieri.

Con le impostazioni di diagnostica di monitoraggio di Azure, è possibile indirizzare i log di diagnostica per l'analisi a più destinazioni diverse.

* **Account di archiviazione**: salvare i log di diagnostica in un account di archiviazione per il controllo o l'ispezione manuale. È possibile usare le impostazioni di diagnostica per specificare il periodo di conservazione in giorni.
* **Hub eventi**: trasmettere i log a hub eventi di Azure. I log vengono inseriti in una soluzione di analisi personalizzata o del servizio partner come Power BI.
* **Area di lavoro log Analytics**: analizzare i log con log Analytics. L'integrazione di sinapsi di Azure con Log Analytics è utile negli scenari seguenti:
  * Si vuole scrivere query complesse su un set completo di metriche pubblicate da Azure sinapsi per Log Analytics. È possibile creare avvisi personalizzati per queste query tramite monitoraggio di Azure.
  * Si desidera eseguire il monitoraggio tra le aree di lavoro. È possibile instradare i dati da più aree di lavoro a una singola area di lavoro Log Analytics.

È anche possibile usare un account di archiviazione o uno spazio dei nomi dell'hub eventi che non si trovi nella sottoscrizione della risorsa che emette i log. L'utente che configura l'impostazione deve disporre dell'accesso appropriato per il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) a entrambe le sottoscrizioni.

#### <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Creare o aggiungere impostazioni di diagnostica per l'area di lavoro, il pool SQL dedicato o il pool di Apache Spark.

1. Nel portale passare a monitoraggio. Selezionare **Impostazioni**  >  **impostazioni di diagnostica**.

1. Selezionare l'area di lavoro sinapsi, il pool SQL dedicato o il pool di Apache Spark per cui si vuole creare un'impostazione di diagnostica.

1. Se nell'area di lavoro selezionata non sono presenti impostazioni di diagnostica, verrà richiesto di creare un'impostazione. Selezionare **Attiva diagnostica**.

   Se nell'area di lavoro sono presenti impostazioni di diagnostica esistenti, verrà visualizzato un elenco di impostazioni già configurate nella risorsa. Selezionare **Aggiungi impostazione di diagnostica**.

1. Assegnare un nome all'impostazione, selezionare **Invia a log Analytics** e quindi selezionare un'area di lavoro da **log Analytics area di lavoro**.

    > [!NOTE]
    > Poiché una tabella dei log di Azure non può contenere più di 500 colonne, è **consigliabile** selezionare la _modalità specifica della risorsa_. Per ulteriori informazioni, vedere [log Analytics limitazioni note](../../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Selezionare **Salva**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per l'area di lavoro, il pool SQL dedicato o il pool di Apache Spark. I log di diagnostica vengono trasmessi a tale area di lavoro non appena vengono generati nuovi dati degli eventi. Un massimo di 15 minuti può trascorrere tra il momento in cui viene generato un evento e il momento in cui viene visualizzato in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni della pipeline in sinapsi Studio](how-to-monitor-pipeline-runs.md) . 

Per altre informazioni sul monitoraggio Apache Spark applicazioni, vedere l'articolo [monitorare le applicazioni Apache Spark in sinapsi Studio](apache-spark-applications.md) .

Per altre informazioni sul monitoraggio delle richieste SQL, vedere l'articolo [monitorare le richieste SQL in sinapsi Studio](how-to-monitor-sql-requests.md) .