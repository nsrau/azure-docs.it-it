---
title: Soluzione di analisi SQL di Azure in Monitoraggio di Azure . Documenti Microsoft
description: La soluzione Analisi SQL di Azure consente di gestire i database SQL di Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275464"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorare il database SQL di Azure usando Analisi SQL di Azure (anteprima)

![Simbolo di Analisi SQL di Azure](./media/azure-sql/azure-sql-symbol.png)

Analisi SQL di Azure è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Analisi SQL di Azure raccoglie e visualizza le metriche chiave delle prestazioni con funzionalità integrate per la risoluzione dei problemi relativi alle prestazioni.

Utilizzando queste metriche raccolte, è possibile creare regole e avvisi di monitoraggio personalizzati. Analisi SQL di Azure consente di identificare i problemi a ogni livello dello stack di applicazioni. Usa le metriche di diagnostica di Azure insieme alle visualizzazioni di Monitoraggio di Azure per presentare i dati su tutti i database SQL di Azure in una singola area di lavoro di Log Analytics.It uses Azure Diagnostic metrics along with Azure Monitor views to present data about all your Azure SQL databases in a single Log Analytics workspace. Monitoraggio di Azure consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

Per una panoramica pratica sull'uso della soluzione Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origini connesse

Analisi SQL di Azure è una soluzione di monitoraggio solo cloud che supporta lo streaming di dati di telemetria di diagnostica per tutti i database SQL di Azure.Azure SQL Analytics is a cloud only monitoring solution supporting streaming of diagnostics telemetry for all of your Azure SQL databases. Poiché Azure SQL Analytics non usa agenti per connettersi a Monitoraggio di Azure, non supporta il monitoraggio di SQL Server ospitato in locale o nelle macchine virtuali.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| [Impostazioni di diagnostica](../platform/diagnostic-settings.md) | **Sì** | Azure metric and log data are sent to Azure Monitor Logs directly by Azure. |
| [Account di archiviazione di AzureAzure storage account](../platform/collect-azure-metrics-logs.md) | No | Monitoraggio azure non legge i dati da un account di archiviazione. |
| [Agenti di Windows](../platform/agent-windows.md) | No | Gli agenti di Windows diretti non vengono usati da Azure SQL Analytics.Direct Windows agents aren't used by Azure SQL Analytics. |
| [Agenti Linux](../learn/quick-collect-linux-computer.md) | No | Gli agenti Linux diretti non vengono usati da Azure SQL Analytics.Direct Linux agents aren't used by Azure SQL Analytics. |
| [Gruppo di gestione di System Center Operations Manager](../platform/om-agents.md) | No | Una connessione diretta dall'agente Operations Manager ad Monitoraggio di Azure non viene utilizzata da Analisi SQL di Azure.A direct connection from the Operations Manager agent to Azure Monitor is not used by Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opzioni di Analisi SQL di AzureAzure SQL Analytics options

Nella tabella seguente vengono descritte le opzioni supportate per due versioni del dashboard di Analisi SQL di Azure, una per i database singoli e in pool e i pool elastici e l'altra per le istanze gestite e i database di istanza.

| Opzione Analisi SQL di AzureAzure SQL Analytics option | Descrizione | Supporto per pool di database ed elastici singoli e in pool | Supporto di database di istanze gestite e istanze |
| --- | ------- | ----- | ----- |
| Risorsa per tipo | Prospettiva che conta tutte le risorse monitorate. | Sì | Sì |
| Informazioni dettagliate | Fornisce il drill-down gerarchico per Intelligent Insights per le prestazioni. | Sì | Sì |
| Errors | Fornisce il drill-down gerarchico per gli errori SQL verificatisi nei database. | Sì | Sì |
| Timeout | Fornisce il drill-down gerarchico per i timeout SQL verificatisi nei database. | Sì | No |
| Blocchi | Fornisce il drill-down gerarchico per i blocchi SQL verificatisi nei database. | Sì | No |
| Attese del database | Fornisce il drill-down gerarchico per le statistiche di attesa SQL a livello di database. Include il riepilogo del tempo di attesa totale e del tempo di attesa per tipo di attesa. |Sì | No |
| Durata delle query | Fornisce il drill-down gerarchico per le statistiche di esecuzione delle query, ad esempio la durata della query, l'utilizzo della CPU, l'utilizzo dei dati di I/O e l'utilizzo dei log di I/O. | Sì | Sì |
| Attese query | Fornisce il drill-down gerarchico per le statistiche di attesa delle query per categoria di attesa. | Sì | Sì |

## <a name="configuration"></a>Configurazione

Usare il processo descritto in [Aggiungere soluzioni di Monitoraggio di Azure dalla raccolta soluzioni](../../azure-monitor/insights/solutions.md) per aggiungere Azure SQL Analytics (anteprima) all'area di lavoro Log Analytics.Use the process described in Add Azure Monitor solutions from the Solutions Gallery to add Azure SQL Analytics (Preview) to your Log Analytics workspace.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Configurare i database SQL di Azure per eseguire il flusso dei dati di telemetria di diagnosticaConfigure Azure SQL databases to stream diagnostics telemetry

Dopo aver creato la soluzione Analisi SQL di Azure nell'area di lavoro, è necessario **configurare ogni** risorsa che si vuole monitorare per trasmettere i dati di telemetria di diagnostica ad Analisi SQL di Azure.After you have created Azure SQL Analytics solution in your workspace, you need to configure each resource that you want to monitor to stream its diagnostics telemetry to Azure SQL Analytics. Seguire le istruzioni dettagliate in questa pagina:

- Abilitare Diagnostica di Azure per il database SQL di Azure per [trasmettere i dati di telemetria di diagnostica ad Analisi SQL di Azure](../../sql-database/sql-database-metrics-diag-logging.md).

La pagina sopra indicata include anche le istruzioni per abilitare il supporto per il monitoraggio di più sottoscrizioni di Azure da una singola area di lavoro di Analisi SQL di Azure come singola finestra.

## <a name="using-azure-sql-analytics"></a>Uso di Analisi SQL di AzureUsing Azure SQL Analytics

Quando si aggiunge Analisi SQL di Azure all'area di lavoro, il riquadro Analisi SQL di Azure viene aggiunto all'area di lavoro e viene visualizzato in Panoramica.When you add Azure SQL Analytics to your workspace, the Azure SQL Analytics tile is added to your workspace, and it appears in Overview. Selezionare Visualizza collegamento di riepilogo per caricare il contenuto del riquadro.

![Riquadro di riepilogo di Analisi SQL di AzureAzure SQL Analytics summary tile](./media/azure-sql/azure-sql-sol-tile-01.png)

Una volta caricato, il riquadro mostra il numero di database singoli e in pool, pool elastici, istanze gestite e database di istanze gestite da cui Analisi SQL di Azure riceve dati di telemetria di diagnostica.

![Riquadro Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

Analisi SQL di Azure offre due viste separate, una per il monitoraggio di singoli database e database in pool e pool elastici, e l'altra visualizzazione per il monitoraggio delle istanze gestite e dei database delle istanze.

Per visualizzare il dashboard di monitoraggio di Azure SQL Analytics per database e pool singoli e in pool, fare clic sulla parte superiore del riquadro. Per visualizzare il dashboard di monitoraggio di Azure SQL Analytics per le istanze gestite e i database delle istanze, fare clic sulla parte inferiore del riquadro.

### <a name="viewing-azure-sql-analytics-data"></a>Visualizzazione dei dati di Analisi SQL di Azure

Il dashboard include la panoramica di tutti i database monitorati tramite prospettive diverse. Affinché prospettive diverse funzionino, è necessario abilitare le metriche o i log delle risorse SQL da trasmettere all'area di lavoro di Log Analytics.For different perspectives to work, you must enable proper metrics or logs on your SQL resources to be streamed to Log Analytics workspace.

Se alcune metriche o log non vengono trasmessi in Azure Monitor, i riquadri in Analisi SQL di Azure non vengono popolati con informazioni di monitoraggio.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Visualizzazione di database e pool singoli e in pool

Una volta selezionato il riquadro di Analisi SQL di Azure per il database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Ogni prospettiva in questa visualizzazione fornisce riepiloghi a livello di sottoscrizione, server, pool elastico e database. Inoltre ogni prospettiva mostra una prospettiva specifica del report a destra. Selezionando una sottoscrizione, un server, un pool o un database nell'elenco, il drilldown continua.

### <a name="managed-instance-and-instances-databases-view"></a>Visualizzazione dei database delle istanze gestite e delle istanze

Una volta selezionato il riquadro di Analisi SQL di Azure per i database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

Selezionando la visualizzazione dell'istanza gestita, vengono visualizzati i dettagli sull'utilizzo dell'istanza gestita, sui database in essa contenuti e sui dati di telemetria nelle query eseguite nell'istanza.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Report di Intelligent Insights

[Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) per il database SQL di Azure assicura un maggior controllo sulle prestazioni di tutti i database SQL di Azure. È possibile visualizzare e accedere a tutti i dati di Intelligent Insights raccolti tramite la prospettiva di Intelligent Insights.

![Informazioni dettagliate di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pool elastici e report di database

Sia i pool elastici che i database dispongono di report specifici che mostrano tutti i dati raccolti per la risorsa nel tempo specificato.

![Database di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elastico SQL di Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Report delle query

Tramite le prospettive relative alla durata e alle attese delle query, è possibile correlare le prestazioni di qualsiasi query tramite il report della query. Questo report confronta le prestazioni della query in database diversi e semplifica l'individuazione dei database che eseguono la query selezionata in modo ottimale, rispetto ai database lenti.

![Query di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Autorizzazioni

Per usare Analisi SQL di Azure, agli utenti devono essere concesse almeno le autorizzazioni del ruolo di Lettore in Azure. Questo ruolo non consente però di visualizzare il testo della query o di eseguire una qualsiasi azione di ottimizzazione automatica. I ruoli più permissivi in Azure che consentono di usare Azure SQL Analytics nella misura massima sono Proprietario, Collaboratore, Collaboratore database SQL o Collaboratore di SQL Server.More permissive roles in Azure that allow using Azure SQL Analytics to the full extent are Owner, Contributor, SQL DB Contributor, or SQL Server Contributor. È possibile anche creare nel portale un ruolo personalizzato dotato di autorizzazioni specifiche necessarie solo per usare Analisi SQL di Azure e senza accesso per gestire altre risorse.

### <a name="creating-a-custom-role-in-portal"></a>Creazione di un ruolo personalizzato nel portale

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Alcune organizzazioni applicano controlli severi sulle autorizzazioni in Azure. Lo script di PowerShell seguente consente di creare un ruolo personalizzato "Operatore di monitoraggio Analisi SQL" nel portale di Azure con le autorizzazioni minime di lettura e scrittura per usare Analisi SQL di Azure nella sua completezza.

Sostituire "{SubscriptionId}" nello script seguente con l'ID della sottoscrizione di Azure ed eseguire lo script dopo aver eseguito l'accesso ad Azure con il ruolo di Proprietario o di Collaboratore.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Dopo aver creato il nuovo ruolo, assegnarlo a ogni utente a cui è necessario concedere autorizzazioni personalizzate per l'utilizzo di Analisi SQL di Azure.

## <a name="analyze-data-and-create-alerts"></a>Analizzare i dati e creare avvisi

L'analisi dei dati in Analisi SQL di Azure è basata sul [linguaggio di Log Analytics](../log-query/get-started-queries.md) per la creazione di report e di query personalizzati. Per una descrizione dei dati disponibili raccolti dalla risorsa del database per la creazione di query personalizzate, vedere [Le metriche e i log disponibili](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

Gli avvisi automatici in Analisi SQL di Azure si basano sulla scrittura di una query di Log Analytics che attiva un avviso in base a una condizione soddisfatta. Di seguito sono riportati alcuni esempi di query di Log Analytics su cui è possibile configurare gli avvisi in Analisi SQL di Azure.Find below several examples on Log Analytics queries on which alerting can be set up in Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Creazione di avvisi per il database SQL di Azure

È possibile [creare avvisi](../platform/alerts-metric.md) facilmente con i dati provenienti dalle risorse del database SQL di Azure. Di seguito sono riportate alcune utili [query su log](../log-query/log-query-overview.md) che è possibile usare con un avviso log:

#### <a name="high-cpu-on-azure-sql-database"></a>Elevato utilizzo della CPU nel database SQL di Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che i database monitorati trasmettono le metriche di base in Analisi SQL di Azure.Pre-requirement of setting up this alert is that monitored databases stream basic metrics to Azure SQL Analytics.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Elevato utilizzo della CPU nei pool elastici del database SQL di Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che i database monitorati trasmettono le metriche di base in Analisi SQL di Azure.Pre-requirement of setting up this alert is that monitored databases stream Basic metrics to Azure SQL Analytics.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Archiviazione di database SQL di Azure in media superiore al 95% nell'ultima ora

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che i database monitorati trasmettono le metriche di base in Analisi SQL di Azure.Pre-requirement of setting up this alert is that monitored databases stream basic metrics to Azure SQL Analytics.
> - Questa query richiede una regola di avviso da impostare in modo da attivare un avviso quando vengono restituiti risultati (> 0 risultati) dalla query, a indicare che la condizione è presente in alcuni database. L'output è un elenco di risorse del database superiore alla soglia di archiviazione all'interno dell'intervallo di tempo definito.
> - L'output è un elenco di risorse del database superiore alla soglia di archiviazione all'interno dell'intervallo di tempo definito.

#### <a name="alert-on-intelligent-insights"></a>Avvisi in Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che i database monitorati trasmettono il log di diagnostica SQLInsights in Analisi SQL di Azure.Pre-requirement of setting up this alert is that monitored databases stream SQLInsights diagnostics log to Azure SQL Analytics.
> - Questa query richiede una regola di avviso da configurare per l'esecuzione con la stessa frequenza di alert_run_interval, per evitare risultati duplicati. La regola deve essere impostata in modo da attivare l'avviso quando vengono restituiti risultati (> 0 risultati) dalla query.
> - Personalizzare il alert_run_interval per specificare l'intervallo di tempo per verificare se la condizione si è verificata nei database configurati per il flusso del log di SQLInsights in Analisi SQL di Azure.Customize the alert_run_interval to specify the time range to check if the condition has occurred on databases configured to stream SQLInsights log to Azure SQL Analytics.
> - Personalizzare il valore di insights_string per acquisire l'output del testo di analisi della causa radice di Insights. Si tratta dello stesso testo visualizzato nell'interfaccia utente di Analisi SQL di Azure che è possibile usare dalle informazioni dettagliate esistenti. In alternativa, è possibile usare la query seguente per visualizzare il testo di tutte le istanze di Insights generate nella sottoscrizione. Usare l'output della query per raccogliere le varie stringhe per configurare gli avvisi in Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Creazione di avvisi per le istanze gestiteCreating alerts for managed instances

#### <a name="managed-instance-storage-is-above-90"></a>L'archiviazione dell'istanza gestita è superiore al 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che il monitoraggio dell'istanza gestita sia abilitato al flusso del log ResourceUsageStats in Analisi SQL di Azure.Pre-requirement of setting up this alert is that monitored the managed instance has the streaming of ResourceUsageStats log enabled to Azure SQL Analytics.
> - Questa query richiede che venga impostata una regola di avviso per generare un avviso quando esistono risultati (risultati > 0) dalla query, indicando che la condizione esiste nell'istanza gestita. L'output è una percentuale di utilizzo dell'archiviazione nell'istanza gestita.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Il consumo medio della CPU dell'istanza gestita è superiore al 95% negli ultimi 1 ora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - Il prerequisito per l'impostazione di questo avviso è che nell'istanza gestita monitorata sia abilitato il flusso del log ResourceUsageStats in Analisi SQL di Azure.Pre-requirement of setting up this alert is that the monitored managed instance has the streaming of ResourceUsageStats log enabled to Azure SQL Analytics.
> - Questa query richiede che venga impostata una regola di avviso per generare un avviso quando esistono risultati (risultati > 0) dalla query, indicando che la condizione esiste nell'istanza gestita. L'output è il consumo percentuale di utilizzo medio della CPU in un periodo definito nell'istanza gestita.

### <a name="pricing"></a>Prezzi

Mentre Azure SQL Analytics è gratuito, si applica l'utilizzo di dati di telemetria di diagnostica al di sopra delle unità gratuite di inserimento dati allocate ogni mese, vedere Prezzi di [Log Analytics.](https://azure.microsoft.com/pricing/details/monitor) Le unità gratuite di inserimento dati fornite consentono di monitorare gratuitamente più database ogni mese. I database più attivi con carichi di lavoro più pesanti ingeriscono più dati rispetto ai database inattivi. È possibile monitorare facilmente l'utilizzo dell'inserimento dei dati in Analisi SQL di Azure selezionando Area di lavoro OMS nel menu di spostamento di Analisi SQL di Azure e quindi selezionando Utilizzo e costi stimati.

## <a name="next-steps"></a>Passaggi successivi

- Usare [le query di log](../log-query/log-query-overview.md) in Monitoraggio di Azure per visualizzare dati SQL di Azure dettagliati.
- [Creare dashboard personalizzati](../learn/tutorial-logs-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](../platform/alerts-overview.md) quando si verificano eventi specifici relativi ad Azure SQL.
