---
title: Soluzione Azure SQL Analytics in Log Analytics | Microsoft Docs
description: La soluzione Analisi SQL di Azure consente di gestire i database SQL di Azure
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: ''
ms.openlocfilehash: d16f9add2cd31eb5a8db650798c241c3dcf2610f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379305"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorare il database SQL di Azure usando Analisi SQL di Azure (anteprima)

![Simbolo di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Analisi SQL di Azure è una soluzione di monitoraggio del cloud per il monitoraggio delle prestazioni dei database SQL di Azure, dei pool elastici e delle istanze gestite su larga scala e tra più sottoscrizioni. Raccoglie e Visualizza importanti metriche sulle prestazioni di Database SQL di Azure con funzionalità di intelligence integrata per la risoluzione dei problemi.

Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. La soluzione consente di identificare i problemi a ogni livello dello stack di applicazioni. Usa le metriche di Diagnostica di Azure insieme alle viste di Log Analytics per presentare i dati su tutti i database SQL di Azure, i pool elastici e i database nelle istanze gestite in un'unica area di lavoro di Log Analytics. Log Analytics consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

Per una panoramica pratica sull'uso della soluzione Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Origini connesse

Analisi SQL di Azure è una soluzione di monitoraggio solo per cloud che supporta il flusso dei dati di telemetria di diagnostica per il database SQL di Azure, i database in Istanza gestita e i pool elastici.

Non usando agenti per connettersi al servizio Log Analytics, la soluzione non supporta il monitoraggio di SQL Server ospitato in locale o in macchine virtuali. Vedere la tabella di compatibilità riportata di seguito.

| Origine connessa | Supportato | DESCRIZIONE |
| --- | --- | --- |
| **[Diagnostica di Azure](log-analytics-azure-storage.md)** | **Sì** | I dati relativi alle metriche e ai log vengono inviati a Log Analytics direttamente da Azure. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No  | Log Analytics non legge i dati da un account di archiviazione. |
| [Agenti Windows](log-analytics-windows-agent.md) | No  | Gli agenti Windows diretti non vengono usati dalla soluzione. |
| [Agenti Linux](log-analytics-linux-agents.md) | No  | Gli agenti Linux diretti non vengono usati dalla soluzione. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | No  | Una connessione diretta dall'agente SCOM a Log Analytics non viene usata dalla soluzione. |

## <a name="configuration"></a>Configurazione

Eseguire questa procedura per aggiungere la soluzione Analisi SQL di Azure al dashboard di Azure.

1. Aggiungere la soluzione Analisi SQL di Azure all'area di lavoro dal [marketplace di Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Nel portale di Azure, fare clic su **+ Crea una risorsa**, quindi cercare **Analisi SQL di Azure**.  
    ![Monitoraggio e gestione](./media/log-analytics-azure-sql/monitoring-management.png)
3. Selezionare **Analisi SQL di Azure (Anteprima)** dall'elenco
4. Nell'area **Analisi SQL di Azure (anteprima)** fare clic su **Crea**.  
    ![Creare](./media/log-analytics-azure-sql/portal-create.png)
5. Nell'area **Crea nuova soluzione**, crearne una nuova o selezionare un'area di lavoro esistente che si vuole aggiungere alla soluzione e poi fare clic su **Crea**.

    ![Aggiunta all'area di lavoro](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Configurare i database SQL di Azure, i pool elastici e le istanze gestite per lo streaming dei dati di telemetria di diagnostica

Dopo aver creato la soluzione di Analisi SQL di Azure nell'area di lavoro, per poter monitorare le prestazioni dei database SQL di Azure, dei database in Istanza gestita e dei pool elastici, sarà necessario **configurare ognuna** di queste risorse da monitorare per trasmettere i dati di telemetria di diagnostica alla soluzione.

- Abilitare Diagnostica di Azure per il database SQL di Azure, i database in Istanza gestita e i pool elastici per [trasmettere i dati di telemetria di diagnostica ad Analisi SQL di Azure](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Per configurare più sottoscrizioni di Azure
 
Per supportare più sottoscrizioni, usare lo script di PowerShell contenuto in [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Abilitare la registrazione delle metriche sulle risorse di Azure usando PowerShell). Specificare l'ID risorsa dell'area di lavoro come parametro quando si esegue lo script per inviare i dati di diagnostica dalle risorse in una sottoscrizione di Azure a un'area di lavoro in un'altra sottoscrizione di Azure.

**Esempio**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione all'area di lavoro, il riquadro Azure SQL Analytics viene aggiunto all'area di lavoro e visualizzato in Panoramica. Il riquadro mostra il numero di database SQL di Azure, pool elastici, istanze gestite e database in istanze gestite da cui la soluzione riceve dati di telemetria di diagnostica.

![Riquadro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

La soluzione offre due visualizzazioni distinte: una per il monitoraggio dei database SQL di Azure e dei pool elastici e l'altra per il monitoraggio delle istanze gestite e dei database in istanze gestite.

Per visualizzare il dashboard di monitoraggio di Analisi SQL di Azure per i database SQL di Azure e i pool elastici, fare clic sulla parte superiore del riquadro. Per visualizzare il dashboard di monitoraggio di Analisi SQL di Azure per Istanza gestita e i database in Istanza gestita, fare clic sulla parte inferiore del riquadro.

### <a name="viewing-azure-sql-analytics-data"></a>Visualizzazione dei dati di Analisi SQL di Azure

Il dashboard include la panoramica di tutti i database monitorati tramite prospettive diverse. Per potere usare diverse prospettive, è necessario abilitare le metriche o i log appropriati nelle risorse SQL di cui deve essere eseguito lo streaming nell'area di lavoro di Azure Log Analytics.

Si noti che se alcune metriche o log non vengono trasmessi in Azure Log Analytics, nei riquadri della soluzione non saranno specificate le informazioni di monitoraggio.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Database SQL di Azure e visualizzazione dei pool elastici

Dopo che è stato selezionato il riquadro di Analisi SQL di Azure per i database SQL di Azure e i pool elastici, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

![Timeout di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Ogni prospettiva in questa visualizzazione fornisce riepiloghi relativi a sottoscrizione, server, pool elastico e livello di database. Inoltre ogni prospettiva mostra una prospettiva specifica del report a destra. Selezionando una sottoscrizione, un server, un pool o un database nell'elenco, il drilldown continua.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Visualizzazione di Istanza gestita e dei database in Istanza gestita

Dopo che è stato selezionato il riquadro di Analisi SQL di Azure per le istanze gestite e i database in Istanza gestita, viene visualizzato il dashboard di monitoraggio.

![Panoramica di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview-mi.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

Selezionando la visualizzazione di Istanza gestita, vengono visualizzate informazioni dettagliate sull'utilizzo di Istanza gestita, i database in essa contenuti e i dati di telemetria sulle query eseguite all'interno dell'istanza.

![Timeout di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Prospettive

La tabella seguente descrive le prospettive supportate per due versioni del dashboard, una per il database SQL di Azure e i pool elastici e l'altra per Istanza gestita.

| Prospettiva | DESCRIZIONE | Supporto per il database SQL e i pool elastici | Supporto per Istanza gestita |
| --- | ------- | ----- | ----- |
| Risorsa per tipo | Prospettiva che conta tutte le risorse monitorate. | Yes | Yes | 
| Informazioni dettagliate | Fornisce il drill-down gerarchico per Intelligent Insights per le prestazioni. | Yes | Yes |
| Errors | Fornisce il drill-down gerarchico per gli errori SQL verificatisi nei database. | Yes | Yes |
| Timeout | Fornisce il drill-down gerarchico per i timeout SQL verificatisi nei database. | Yes | No  |
| Blocchi | Fornisce il drill-down gerarchico per i blocchi SQL verificatisi nei database. | Yes | No  |
| Attese del database | Fornisce il drill-down gerarchico per le statistiche di attesa SQL a livello di database. Include il riepilogo del tempo di attesa totale e del tempo di attesa per tipo di attesa. |Yes | Yes |
| Durata delle query | Fornisce il drill-down gerarchico per le statistiche di esecuzione delle query, ad esempio la durata della query, l'utilizzo della CPU, l'utilizzo dei dati di I/O e l'utilizzo dei log di I/O. | Yes | Yes |
| Attese query | Fornisce il drill-down gerarchico per le statistiche di attesa delle query per categoria di attesa. | Yes | Yes |

### <a name="intelligent-insights-report"></a>Report di Intelligent Insights

[Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) per il database SQL di Azure assicura un maggior controllo sulle prestazioni dei database SQL di Azure e dei database in Istanza gestita. È possibile visualizzare e accedere a tutti i dati di Intelligent Insights raccolti tramite la prospettiva di Intelligent Insights.

![Informazioni dettagliate di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Report relativi a pool elastici e database

I pool elastici e i database SQL sono associati a report specifici, che mostrano tutti i dati raccolti per la risorsa nel periodo di tempo specificato.

![Database di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool elastico SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Report delle query

Tramite le prospettive relativa a durata e attese delle query, è possibile correlare le prestazioni di qualsiasi query attraverso il report della query. Questo report confronta le prestazioni della query in database diversi e semplifica l'individuazione dei database che eseguono la query selezionata in modo ottimale, rispetto ai database lenti.

![Query di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="pricing"></a>Prezzi

La soluzione è disponibile gratuitamente. Viene addebitato il consumo di telemetria di diagnostica oltre il limite mensile di unità gratuite di inserimento dati. Per informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/en-us/pricing/details/monitor). Le unità gratuite di inserimento dati fornite consentono di monitorare gratuitamente più database ogni mese. Si noti che i database più attivi con carichi di lavoro più pesanti inseriranno più dati rispetto ai database inattivi. È possibile monitorare facilmente il consumo di dati nella soluzione selezionando Area di lavoro di OMS nel menu di spostamento di Analisi SQL di Azure e quindi scegliendo Utilizzo e costi stimati.

### <a name="analyze-data-and-create-alerts"></a>Analizzare i dati e creare avvisi

### <a name="creating-alerts-for-azure-sql-database"></a>Creazione di avvisi per il database SQL di Azure

È possibile [creare avvisi](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) facilmente con i dati provenienti dalle risorse del database SQL di Azure. Di seguito sono riportate alcune query utili di [ricerca log](log-analytics-log-searches.md) che è possibile usare con un avviso log:

*Elevato utilizzo della CPU nel database SQL di Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Il prerequisito di configurazione di questo avviso è che i database monitorati trasmettano le metriche di diagnostica (opzione "Tutte le metriche") alla soluzione.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

*Elevato utilizzo della CPU nei pool elastici del database SQL di Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - Il prerequisito di configurazione di questo avviso è che i database monitorati trasmettano le metriche di diagnostica (opzione "Tutte le metriche") alla soluzione.
> - Sostituire il valore cpu_percent di MetricName con dtu_consumption_percent per ottenere risultati di DTU elevati.

*Archiviazione di database SQL di Azure in media superiore al 95% nell'ultima ora*

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
> - Il prerequisito di configurazione di questo avviso è che i database monitorati trasmettano le metriche di diagnostica (opzione "Tutte le metriche") alla soluzione.
> - Questa query richiede una regola di avviso da impostare in modo da attivare un avviso quando vengono restituiti risultati (> 0 risultati) dalla query, a indicare che la condizione è presente in alcuni database. L'output è un elenco di risorse del database superiore alla soglia di archiviazione all'interno dell'intervallo di tempo definito.
> - L'output è un elenco di risorse del database superiore alla soglia di archiviazione all'interno dell'intervallo di tempo definito.

*Avvisi in Intelligent Insights*

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

* Le risorse di archiviazione per Istanza gestita sono superiori al 90%

```
let storage_percentage_treshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_treshold
```

> [!NOTE]
> - Il prerequisito di configurazione di questo avviso prevede che per l'istanza gestita monitorata sia abilitata la trasmissione del log ResourceUsageStats alla soluzione.
> - Questa query richiede una regola di avviso da impostare in modo da attivare un avviso quando vengono restituiti risultati (> 0 risultati) dalla query, a indicare che la condizione è presente nell'istanza gestita. L'output è il consumo percentuale delle risorse di archiviazione nell'istanza gestita.

## <a name="next-steps"></a>Passaggi successivi

- Usare le [ricerche log](log-analytics-log-searches.md) in Log Analytics per visualizzare i dati dettagliati per Azure SQL.
- [Creare dashboard personalizzati](log-analytics-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](log-analytics-alerts.md) quando si verificano eventi specifici relativi ad Azure SQL.
