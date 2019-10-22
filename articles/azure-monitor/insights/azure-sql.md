---
title: Soluzione Analisi SQL di Azure in monitoraggio di Azure | Microsoft Docs
description: La soluzione Analisi SQL di Azure consente di gestire i database SQL di Azure
services: log-analytics
ms.service: log-analytics
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: carlrab
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: f27ea984e2c11be03d69295d206c1b99791a3f72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693286"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorare il database SQL di Azure usando Analisi SQL di Azure (anteprima)

![Simbolo di Analisi SQL di Azure](./media/azure-sql/azure-sql-symbol.png)

Analisi SQL di Azure è una soluzione cloud avanzata che consente di monitorare le prestazioni di database SQL di Azure, istanze gestite e pool elastici in scala e in più abbonamenti da una singola finestra. Raccoglie e Visualizza importanti metriche sulle prestazioni di Database SQL di Azure con funzionalità di intelligence integrata per la risoluzione dei problemi.

Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. La soluzione consente di identificare i problemi a ogni livello dello stack di applicazioni. Usa le metriche di diagnostica di Azure insieme alle visualizzazioni di monitoraggio di Azure per presentare i dati su tutti i database SQL di Azure, i pool elastici e i database nelle istanze gestite in un'unica area di lavoro Log Analytics. Monitoraggio di Azure consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

Per una panoramica pratica sull'uso della soluzione Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Origini connesse

Analisi SQL di Azure è una soluzione di monitoraggio solo per cloud che supporta il flusso dei dati di telemetria di diagnostica per i database SQL di Azure: singoli, in pool e dell'istanza gestita. Poiché la soluzione non usa gli agenti per connettersi a monitoraggio di Azure, la soluzione non supporta il monitoraggio di SQL Server ospitati in locale o in macchine virtuali, vedere la tabella compatibilità riportata di seguito.

| Origine connessa | Supportato | Description |
| --- | --- | --- |
| [Impostazioni di diagnostica](../platform/diagnostic-settings.md) | **Sì** | I dati relativi alle metriche e ai log di Azure vengono inviati ai log di monitoraggio di Azure direttamente da Azure. |
| [Account di archiviazione di Azure](../platform/collect-azure-metrics-logs.md) | No | Monitoraggio di Azure non legge i dati da un account di archiviazione. |
| [Agenti di Windows](../platform/agent-windows.md) | No | Gli agenti Windows diretti non vengono usati dalla soluzione. |
| [Agenti Linux](../learn/quick-collect-linux-computer.md) | No | Gli agenti Linux diretti non vengono usati dalla soluzione. |
| [Gruppo di gestione di System Center Operations Manager](../platform/om-agents.md) | No | Una connessione diretta dall'agente Operations Manager al monitoraggio di Azure non viene usata dalla soluzione. |

## <a name="configuration"></a>Configurazione
Usare la procedura descritta in [aggiungere soluzioni di monitoraggio di Azure dalla raccolta di soluzioni](../../azure-monitor/insights/solutions.md) per aggiungere la soluzione analisi SQL di Azure (anteprima) all'area di lavoro log Analytics.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurare i database SQL di Azure, i pool elastici e le istanze gestite per lo streaming dei dati di telemetria di diagnostica

Dopo aver creato la soluzione Analisi SQL di Azure nell'area di lavoro, è necessario **configurare ogni risorsa** che si vuole monitorare per trasmettere i relativi dati di telemetria di diagnostica alla soluzione. Seguire le istruzioni dettagliate in questa pagina:

- Abilitare Diagnostica di Azure per il database SQL di Azure per [trasmettere i dati di telemetria di diagnostica ad Analisi SQL di Azure](../../sql-database/sql-database-metrics-diag-logging.md).

La pagina sopra indicata include anche le istruzioni per abilitare il supporto per il monitoraggio di più sottoscrizioni di Azure da una singola area di lavoro di Analisi SQL di Azure come singola finestra.

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione all'area di lavoro, il riquadro Azure SQL Analytics viene aggiunto all'area di lavoro e visualizzato in Panoramica. Selezionare Visualizza il collegamento di riepilogo per caricare il contenuto del riquadro.

![Riquadro di riepilogo Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-tile-01.png)

Al termine del caricamento, il riquadro Mostra il numero di database SQL di Azure, i pool elastici, le istanze gestite e i database nelle istanze gestite da cui la soluzione riceve i dati di telemetria di diagnostica.

![Riquadro Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-02.png)

La soluzione offre due visualizzazioni distinte: una per il monitoraggio dei database SQL di Azure e dei pool elastici e l'altra per il monitoraggio delle istanze gestite e dei database in istanze gestite.

Per visualizzare il dashboard di monitoraggio di Analisi SQL di Azure per i database SQL di Azure e i pool elastici, fare clic sulla parte superiore del riquadro. Per visualizzare il dashboard di monitoraggio di Analisi SQL di Azure per Istanza gestita e i database in Istanza gestita, fare clic sulla parte inferiore del riquadro.

### <a name="viewing-azure-sql-analytics-data"></a>Visualizzazione dei dati di Analisi SQL di Azure

Il dashboard include la panoramica di tutti i database monitorati tramite prospettive diverse. Per il funzionamento di prospettive diverse, è necessario abilitare le metriche o i log appropriati sulle risorse SQL da trasmettere in Log Analytics area di lavoro.

Si noti che se alcune metriche o log non vengono trasmessi in monitoraggio di Azure, i riquadri della soluzione non vengono popolati con le informazioni di monitoraggio.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Database SQL di Azure e visualizzazione dei pool elastici

Una volta selezionato il riquadro di Analisi SQL di Azure per il database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Ogni prospettiva in questa visualizzazione fornisce riepiloghi relativi a sottoscrizione, server, pool elastico e livello di database. Inoltre ogni prospettiva mostra una prospettiva specifica del report a destra. Selezionando una sottoscrizione, un server, un pool o un database nell'elenco, il drilldown continua.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Visualizzazione di Istanza gestita e dei database in Istanza gestita

Una volta selezionato il riquadro di Analisi SQL di Azure per i database, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

Selezionando la visualizzazione di Istanza gestita, vengono visualizzate informazioni dettagliate sull'utilizzo di Istanza gestita, i database in essa contenuti e i dati di telemetria sulle query eseguite all'interno dell'istanza.

![Timeout di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Prospettive

La tabella seguente descrive le prospettive supportate per due versioni del dashboard, una per il database SQL di Azure e i pool elastici e l'altra per Istanza gestita.

| Prospettiva | Description | Supporto per il database SQL e i pool elastici | Supporto per Istanza gestita |
| --- | ------- | ----- | ----- |
| Risorsa per tipo | Prospettiva che conta tutte le risorse monitorate. | SÌ | SÌ |
| Informazioni approfondite | Fornisce il drill-down gerarchico per Intelligent Insights per le prestazioni. | SÌ | SÌ |
| Errors | Fornisce il drill-down gerarchico per gli errori SQL verificatisi nei database. | SÌ | SÌ |
| Timeout | Fornisce il drill-down gerarchico per i timeout SQL verificatisi nei database. | SÌ | No |
| Blocchi | Fornisce il drill-down gerarchico per i blocchi SQL verificatisi nei database. | SÌ | No |
| Attese del database | Fornisce il drill-down gerarchico per le statistiche di attesa SQL a livello di database. Include il riepilogo del tempo di attesa totale e del tempo di attesa per tipo di attesa. |SÌ | SÌ |
| Durata delle query | Fornisce il drill-down gerarchico per le statistiche di esecuzione delle query, ad esempio la durata della query, l'utilizzo della CPU, l'utilizzo dei dati di I/O e l'utilizzo dei log di I/O. | SÌ | SÌ |
| Attese query | Fornisce il drill-down gerarchico per le statistiche di attesa delle query per categoria di attesa. | SÌ | SÌ |

### <a name="intelligent-insights-report"></a>Report di Intelligent Insights

[Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) per il database SQL di Azure assicura un maggior controllo sulle prestazioni di tutti i database SQL di Azure. È possibile visualizzare e accedere a tutti i dati di Intelligent Insights raccolti tramite la prospettiva di Intelligent Insights.

![Informazioni dettagliate di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Report relativi a pool elastici e database

I pool elastici e i database SQL sono associati a report specifici, che mostrano tutti i dati raccolti per la risorsa nel periodo di tempo specificato.

![Database di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elastico SQL di Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Report delle query

Tramite le prospettive relativa a durata e attese delle query, è possibile correlare le prestazioni di qualsiasi query attraverso il report della query. Questo report confronta le prestazioni della query in database diversi e semplifica l'individuazione dei database che eseguono la query selezionata in modo ottimale, rispetto ai database lenti.

![Query di Analisi SQL di Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>autorizzazioni

Per usare Analisi SQL di Azure, agli utenti devono essere concesse almeno le autorizzazioni del ruolo di Lettore in Azure. Questo ruolo non consente però di visualizzare il testo della query o di eseguire una qualsiasi azione di ottimizzazione automatica. I ruoli più permissivi di Azure che consentono di usare la soluzione nella sua completezza sono Proprietario, Collaboratore, Collaboratore Database SQL e Collaboratore SQL Server. È possibile anche creare nel portale un ruolo personalizzato dotato di autorizzazioni specifiche necessarie solo per usare Analisi SQL di Azure e senza accesso per gestire altre risorse.

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

La generazione di avvisi automatica nella soluzione è basata sulla scrittura di una query di Log Analytics che genera un avviso al verificarsi di una condizione. Di seguito sono riportati diversi esempi di query di Log Analytics per le quali è possibile configurare gli avvisi nella soluzione.

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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base alla soluzione.
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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base alla soluzione.
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
> - Il prerequisito per la configurazione di questo avviso è che i database monitorati inviano metriche di base alla soluzione.
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
> - Il prerequisito di configurazione di questo avviso prevede che i database monitorati trasmettano il log di diagnostica SQLInsights alla soluzione.
> - Questa query richiede una regola di avviso da configurare per l'esecuzione con la stessa frequenza di alert_run_interval, per evitare risultati duplicati. La regola deve essere impostata in modo da attivare l'avviso quando vengono restituiti risultati (> 0 risultati) dalla query.
> - Personalizzare il valore di alert_run_interval in modo da specificare l'intervallo di tempo in cui controllare se la condizione si è verificata nel database configurato, al fine di trasmettere il log SQLInsights alla soluzione.
> - Personalizzare il valore di insights_string per acquisire l'output del testo di analisi della causa radice di Insights. Si tratta dello stesso testo visualizzato nell'interfaccia utente della soluzione che è possibile usare dall'istanza di Insights esistente. In alternativa, è possibile usare la query seguente per visualizzare il testo di tutte le istanze di Insights generate nella sottoscrizione. Usare l'output della query per raccogliere le varie stringhe per configurare gli avvisi in Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Creazione di avvisi per Istanza gestita

#### <a name="managed-instance-storage-is-above-90"></a>Risorse di archiviazione per Istanza gestita superiori al 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Il prerequisito di configurazione di questo avviso prevede che per l'istanza gestita monitorata sia abilitata la trasmissione del log ResourceUsageStats alla soluzione.
> - Questa query richiede una regola di avviso da impostare in modo da attivare un avviso quando vengono restituiti risultati (> 0 risultati) dalla query, a indicare che la condizione è presente nell'istanza gestita. L'output è il consumo percentuale delle risorse di archiviazione nell'istanza gestita.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Consumo medio della CPU dell'istanza gestita superiore al 95% nell'ultima ora

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Il prerequisito di configurazione di questo avviso prevede che per l'istanza gestita monitorata sia abilitata la trasmissione del log ResourceUsageStats alla soluzione.
> - Questa query richiede una regola di avviso da impostare in modo da attivare un avviso quando vengono restituiti risultati (> 0 risultati) dalla query, a indicare che la condizione è presente nell'istanza gestita. L'output è il consumo medio della percentuale di utilizzo della CPU in un periodo definito nell'istanza gestita.

### <a name="pricing"></a>Prezzi

La soluzione è disponibile gratuitamente. Viene addebitato il consumo di telemetria di diagnostica oltre il limite mensile di unità gratuite di inserimento dati. Per informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/monitor). Le unità gratuite di inserimento dati fornite consentono di monitorare gratuitamente più database ogni mese. Si noti che i database più attivi con carichi di lavoro più pesanti inseriscono più dati rispetto ai database inattivi. È possibile monitorare facilmente il consumo di dati nella soluzione selezionando Area di lavoro di OMS nel menu di spostamento di Analisi SQL di Azure e quindi scegliendo Utilizzo e costi stimati.

## <a name="next-steps"></a>Passaggi successivi

- Usare le [query di log](../log-query/log-query-overview.md) in monitoraggio di Azure per visualizzare dati dettagliati di Azure SQL.
- [Creare dashboard personalizzati](../learn/tutorial-logs-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](../platform/alerts-overview.md) quando si verificano eventi specifici relativi ad Azure SQL.
