---
title: Soluzione Analisi SQL di Azure in monitoraggio di Azure | Microsoft Docs
description: La soluzione Analisi SQL di Azure consente di gestire i database SQL di Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: d1f8a30145cc0d61f110c0f47459a4f1db03325b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249416"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorare il database SQL di Azure usando Analisi SQL di Azure (anteprima)

![Simbolo di Analisi SQL di Azure](./media/azure-sql/azure-sql-symbol.png)

Analisi SQL di Azure è una soluzione di monitoraggio cloud avanzata per il monitoraggio delle prestazioni di tutti i database SQL di Azure su larga scala e tra più sottoscrizioni in un'unica visualizzazione. Analisi SQL di Azure raccoglie e visualizza le metriche delle prestazioni chiave con l'intelligence predefinita per la risoluzione dei problemi relativi alle prestazioni.

Usando queste metriche raccolte, è possibile creare regole e avvisi di monitoraggio personalizzati. Analisi SQL di Azure consente di identificare i problemi a ogni livello dello stack dell'applicazione. Usa le metriche di diagnostica di Azure insieme alle visualizzazioni di monitoraggio di Azure per presentare i dati su tutti i database SQL di Azure in una singola area di lavoro Log Analytics. Monitoraggio di Azure consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

Per una panoramica pratica sull'uso della soluzione Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origini connesse

Analisi SQL di Azure è una soluzione di monitoraggio solo cloud che supporta la trasmissione di dati di telemetria di diagnostica per tutti i database SQL di Azure. Poiché Analisi SQL di Azure non usa gli agenti per connettersi a monitoraggio di Azure, non supporta il monitoraggio di SQL Server ospitati in locale o in macchine virtuali.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| [Impostazioni di diagnostica](../platform/diagnostic-settings.md) | **Sì** | I dati relativi alle metriche e ai log di Azure vengono inviati ai log di monitoraggio di Azure direttamente da Azure. |
| [Account di archiviazione di Azure](../platform/collect-azure-metrics-logs.md) | No | Monitoraggio di Azure non legge i dati da un account di archiviazione. |
| [Agenti di Windows](../platform/agent-windows.md) | No | Gli agenti di Windows diretti non vengono usati da Analisi SQL di Azure. |
| [Agenti Linux](../learn/quick-collect-linux-computer.md) | No | Gli agenti Linux diretti non vengono usati da Analisi SQL di Azure. |
| [Gruppo di gestione di System Center Operations Manager](../platform/om-agents.md) | No | Una connessione diretta dall'agente Operations Manager al monitoraggio di Azure non viene utilizzata da Analisi SQL di Azure. |

## <a name="azure-sql-analytics-options"></a>Opzioni di Analisi SQL di Azure

La tabella seguente descrive le opzioni supportate per due versioni del dashboard di Analisi SQL di Azure, una per il database SQL di Azure e l'altra per i database di Istanza gestita di Azure SQL.

| Opzione Analisi SQL di Azure | Descrizione | Supporto per database SQL | Supporto di SQL Istanza gestita |
| --- | ------- | ----- | ----- |
| Risorsa per tipo | Prospettiva che conta tutte le risorse monitorate. | Sì | Sì |
| Informazioni dettagliate | Fornisce il drill-down gerarchico per Intelligent Insights per le prestazioni. | Sì | Sì |
| Errori | Fornisce il drill-down gerarchico per gli errori SQL verificatisi nei database. | Sì | Sì |
| Timeout | Fornisce il drill-down gerarchico per i timeout SQL verificatisi nei database. | Sì | No |
| Blocchi | Fornisce il drill-down gerarchico per i blocchi SQL verificatisi nei database. | Sì | No |
| Attese del database | Fornisce il drill-down gerarchico per le statistiche di attesa SQL a livello di database. Include il riepilogo del tempo di attesa totale e del tempo di attesa per tipo di attesa. |Sì | No |
| Durata delle query | Fornisce il drill-down gerarchico per le statistiche di esecuzione delle query, ad esempio la durata della query, l'utilizzo della CPU, l'utilizzo dei dati di I/O e l'utilizzo dei log di I/O. | Sì | Sì |
| Attese query | Fornisce il drill-down gerarchico per le statistiche di attesa delle query per categoria di attesa. | Sì | Sì |

## <a name="configuration"></a>Configurazione

Usare la procedura descritta in [aggiungere soluzioni di monitoraggio di Azure dalla raccolta di soluzioni](../../azure-monitor/insights/solutions.md) per aggiungere analisi SQL di Azure (anteprima) all'area di lavoro log Analytics.

### <a name="configure-azure-sql-database-to-stream-diagnostics-telemetry"></a>Configurare il database SQL di Azure per lo streaming della telemetria di diagnostica

Dopo aver creato Analisi SQL di Azure soluzione nell'area di lavoro, è necessario **configurare ogni** risorsa che si vuole monitorare per trasmettere i dati di telemetria di diagnostica al analisi SQL di Azure. Seguire le istruzioni dettagliate in questa pagina:

- Abilitare Diagnostica di Azure per il database per trasmettere i dati di [telemetria di diagnostica ai analisi SQL di Azure](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

La pagina sopra indicata include anche le istruzioni per abilitare il supporto per il monitoraggio di più sottoscrizioni di Azure da una singola area di lavoro di Analisi SQL di Azure come singola finestra.

## <a name="using-azure-sql-analytics"></a>Utilizzo di Analisi SQL di Azure

Quando si aggiungono Analisi SQL di Azure all'area di lavoro, il riquadro Analisi SQL di Azure viene aggiunto all'area di lavoro e viene visualizzato in panoramica. Selezionare Visualizza il collegamento di riepilogo per caricare il contenuto del riquadro.

![Riquadro di riepilogo Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

Una volta caricato, il riquadro Mostra il numero di database e pool elastici nel database SQL e le istanze gestite e i database di istanza in SQL Istanza gestita da cui Analisi SQL di Azure riceve la telemetria di diagnostica.

![Riquadro Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

In Analisi SQL di Azure sono disponibili due visualizzazioni separate, una per il monitoraggio del database SQL e l'altra per il monitoraggio di SQL Istanza gestita.

Per visualizzare il dashboard di monitoraggio Analisi SQL di Azure per il database SQL, fare clic sulla parte superiore del riquadro. Per visualizzare il dashboard di monitoraggio Analisi SQL di Azure per SQL Istanza gestita, fare clic sulla parte inferiore del riquadro.

### <a name="viewing-azure-sql-analytics-data"></a>Visualizzazione dei dati di Analisi SQL di Azure

Il dashboard include la panoramica di tutti i database monitorati tramite prospettive diverse. Per il funzionamento di prospettive diverse, è necessario abilitare le metriche o i log appropriati sulle risorse SQL da trasmettere in Log Analytics area di lavoro.

Se alcune metriche o log non vengono trasmessi in monitoraggio di Azure, i riquadri in Analisi SQL di Azure non vengono popolati con le informazioni di monitoraggio.

### <a name="sql-database-view"></a>Vista del database SQL

Una volta selezionato il riquadro di Analisi SQL di Azure per il database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Ogni prospettiva in questa vista fornisce riepiloghi a livello di sottoscrizione, server, pool elastico e database. Inoltre ogni prospettiva mostra una prospettiva specifica del report a destra. Selezionando una sottoscrizione, un server, un pool o un database nell'elenco, il drilldown continua.

### <a name="sql-managed-instance-view"></a>Visualizzazione Istanza gestita SQL

Una volta selezionato il riquadro di Analisi SQL di Azure per i database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

Selezionando la vista Istanza gestita SQL, vengono visualizzati i dettagli sull'utilizzo dell'istanza gestita, i database in esso contenuti e i dati di telemetria sulle query eseguite nell'istanza.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Report di Intelligent Insights

[Intelligent Insights](../../azure-sql/database/intelligent-insights-overview.md) per il database SQL di Azure assicura un maggior controllo sulle prestazioni di tutti i database SQL di Azure. È possibile visualizzare e accedere a tutti i dati di Intelligent Insights raccolti tramite la prospettiva di Intelligent Insights.

![Informazioni dettagliate di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Pool elastici e report di database

Sia i pool elastici che i database hanno report specifici che mostrano tutti i dati raccolti per la risorsa nel tempo specificato.

![Database di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elastico SQL di Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Report delle query

Attraverso le prospettive di durata e attese delle query, è possibile correlare le prestazioni di qualsiasi query tramite il report della query. Questo report confronta le prestazioni della query in database diversi e semplifica l'individuazione dei database che eseguono la query selezionata in modo ottimale, rispetto ai database lenti.

![Query di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Autorizzazioni

Per usare Analisi SQL di Azure, agli utenti devono essere concesse almeno le autorizzazioni del ruolo di Lettore in Azure. Questo ruolo non consente però di visualizzare il testo della query o di eseguire una qualsiasi azione di ottimizzazione automatica. I ruoli più permissivi in Azure che consentono l'uso di Analisi SQL di Azure alla massima portata sono proprietario, collaboratore, collaboratore database SQL o collaboratore SQL Server. È possibile anche creare nel portale un ruolo personalizzato dotato di autorizzazioni specifiche necessarie solo per usare Analisi SQL di Azure e senza accesso per gestire altre risorse.

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

L'analisi dei dati in Analisi SQL di Azure è basata sul [linguaggio di Log Analytics](../log-query/get-started-queries.md) per la creazione di report e di query personalizzati. Per una descrizione dei dati disponibili raccolti dalla risorsa del database per la creazione di query personalizzate, vedere [Le metriche e i log disponibili](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#metrics-and-logs-available).

Gli avvisi automatici in Analisi SQL di Azure si basano sulla scrittura di una query Log Analytics che attiva un avviso in seguito a una condizione soddisfatta. Di seguito sono riportati alcuni esempi di Log Analytics query su cui è possibile configurare gli avvisi in Analisi SQL di Azure.

### <a name="creating-alerts-for-azure-sql-database"></a>Creazione di avvisi per il database SQL di Azure

È possibile [creare avvisi](../platform/alerts-metric.md) facilmente con i dati provenienti dalle risorse del database SQL di Azure. Di seguito sono riportate alcune utili [query su log](../log-query/log-query-overview.md) che è possibile usare con un avviso log:

#### <a name="high-cpu"></a>CPU elevata

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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base a Analisi SQL di Azure.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

#### <a name="high-cpu-on-elastic-pools"></a>CPU elevata nei pool elastici

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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base a Analisi SQL di Azure.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

#### <a name="storage-in-average-above-95-in-the-last-1-hr"></a>Archiviazione in media superiore al 95% nelle ultime 1 hr

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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base a Analisi SQL di Azure.
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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano il log di diagnostica di sqlinsights al Analisi SQL di Azure.
> - Questa query richiede una regola di avviso da configurare per l'esecuzione con la stessa frequenza di alert_run_interval, per evitare risultati duplicati. La regola deve essere impostata in modo da attivare l'avviso quando vengono restituiti risultati (> 0 risultati) dalla query.
> - Personalizzare il alert_run_interval per specificare l'intervallo di tempo in cui verificare se si è verificata la condizione nei database configurati per trasmettere il log di sqlinsights a Analisi SQL di Azure.
> - Personalizzare il valore di insights_string per acquisire l'output del testo di analisi della causa radice di Insights. Si tratta dello stesso testo visualizzato nell'interfaccia utente di Analisi SQL di Azure che è possibile usare dalle informazioni esistenti. In alternativa, è possibile usare la query seguente per visualizzare il testo di tutte le istanze di Insights generate nella sottoscrizione. Usare l'output della query per raccogliere le varie stringhe per configurare gli avvisi in Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-sql-managed-instance"></a>Creazione di avvisi per SQL Istanza gestita

#### <a name="storage-is-above-90"></a>Archiviazione superiore al 90%

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
> - Il requisito preliminare per la configurazione di questo avviso è che il monitoraggio dell'istanza gestita ha lo streaming del log ResourceUsageStats abilitato per Analisi SQL di Azure.
> - Questa query richiede la configurazione di una regola di avviso per attivare un avviso quando sono presenti risultati (> 0 Risultati) dalla query, che indica che la condizione esiste nell'istanza gestita. L'output corrisponde al consumo percentuale di archiviazione nell'istanza gestita.

#### <a name="cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Il consumo medio della CPU è superiore al 95% nelle ultime 1 hr

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
> - Il prerequisito per la configurazione di questo avviso è che l'istanza gestita monitorata ha lo streaming del log ResourceUsageStats abilitato per Analisi SQL di Azure.
> - Questa query richiede la configurazione di una regola di avviso per attivare un avviso quando sono presenti risultati (> 0 Risultati) dalla query, che indica che la condizione esiste nell'istanza gestita. L'output corrisponde al consumo medio della percentuale di utilizzo della CPU nel periodo definito nell'istanza gestita.

### <a name="pricing"></a>Prezzi

Sebbene Analisi SQL di Azure sia libero da usare, il consumo di dati di telemetria di diagnostica superiore alle unità gratuite di inserimento dati allocati ogni mese si applica, vedere [log Analytics prezzi](https://azure.microsoft.com/pricing/details/monitor). Le unità gratuite di inserimento dati fornite consentono di monitorare gratuitamente più database ogni mese. Più database attivi con carichi di lavoro più pesanti inseriscono più dati rispetto ai database inattivi. È possibile monitorare facilmente il consumo di inserimento dei dati in Analisi SQL di Azure selezionando area di lavoro OMS nel menu di navigazione di Analisi SQL di Azure, quindi selezionando utilizzo e costi stimati.

## <a name="next-steps"></a>Passaggi successivi

- Usare le [query di log](../log-query/log-query-overview.md) in monitoraggio di Azure per visualizzare dati dettagliati di Azure SQL.
- [Creare dashboard personalizzati](../learn/tutorial-logs-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](../platform/alerts-overview.md) quando si verificano eventi specifici relativi ad Azure SQL.
