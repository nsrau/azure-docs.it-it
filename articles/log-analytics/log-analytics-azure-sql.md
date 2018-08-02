---
title: Soluzione Azure SQL Analytics in Log Analytics | Microsoft Docs
description: La soluzione Analisi SQL di Azure consente di gestire i database SQL di Azure
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 440e16416b8567178c61c3d6ce2155e0e331521c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216326"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Monitorare i database SQL di Azure usando Analisi SQL di Azure (Anteprima)

![Simbolo di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Analisi SQL di Azure è un cloud per la soluzione di monitoraggio per il monitoraggio delle prestazioni dei database SQL di Azure su larga scala tra più abbonamenti e pool elastici. Raccoglie e Visualizza importanti metriche sulle prestazioni di Database SQL di Azure con funzionalità di intelligence integrata per la risoluzione dei problemi nella parte superiore. 

Usando le metriche raccolte con la soluzione, è possibile creare regole e avvisi di monitoraggio personalizzati. La soluzione consente di identificare i problemi a ogni livello dello stack di applicazioni. Usa le metriche di Diagnostica di Azure insieme alle viste di Log Analytics per presentare i dati su tutti i database SQL di Azure e i pool elastici in un'unica area di lavoro di Log Analytics. Log Analytics consente di raccogliere, correlare e visualizzare dati strutturati e non strutturati.

Questa soluzione in anteprima supporta attualmente fino a 150.000 database SQL di Azure e 5.000 pool elastici SQL per area di lavoro.

Per una panoramica pratica sull'uso della soluzione Analisi SQL di Azure e per scenari di uso tipici, vedere il video incorporato:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Origini connesse

Analisi SQL di Azure è una soluzione di monitoraggio del cloud che supporta il flusso della telemetria di diagnostica per i database SQL di Azure e i pool elastici. Poiché non usa agenti per connettersi al servizio Log Analytics, la soluzione non supporta la connettività con risorse Windows, Linux o SCOM, consultare la tabella di compatibilità riportata di seguito.

| Origine connessa | Supporto | DESCRIZIONE |
| --- | --- | --- |
| **[Diagnostica di Azure](log-analytics-azure-storage.md)** | **Sì** | I dati relativi alle metriche e ai log vengono inviati a Log Analytics direttamente da Azure. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No  | Log Analytics non legge i dati da un account di archiviazione. |
| [Agenti di Windows](log-analytics-windows-agent.md) | No  | Gli agenti Windows diretti non vengono usati dalla soluzione. |
| [Agenti Linux](log-analytics-linux-agents.md) | No  | Gli agenti Linux diretti non vengono usati dalla soluzione. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | No  | Una connessione diretta dall'agente SCOM a Log Analytics non viene usata dalla soluzione. |

## <a name="configuration"></a>Configurazione

Eseguire questa procedura per aggiungere la soluzione Analisi SQL di Azure all'area di lavoro.

1. Aggiungere la soluzione Analisi SQL di Azure all'area di lavoro dal [marketplace di Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Nel portale di Azure, fare clic su **+ Crea una risorsa**, quindi cercare **Analisi SQL di Azure**.  
    ![Monitoraggio e gestione](./media/log-analytics-azure-sql/monitoring-management.png)
3. Selezionare **Analisi SQL di Azure (Anteprima)** dall'elenco
4. Nell'area **Analisi SQL di Azure (anteprima)** fare clic su **Crea**.  
    ![Creare](./media/log-analytics-azure-sql/portal-create.png)
5. Nell'area **Crea nuova soluzione**, crearne una nuova o selezionare un'area di lavoro esistente che si vuole aggiungere alla soluzione e poi fare clic su **Crea**.  
    ![Aggiunta all'area di lavoro](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Configurare i database SQL di Azure e i pool elastici per lo streaming della telemetria di diagnostica

Dopo aver creato la soluzione di Analisi SQL di Azure nell'area di lavoro, per poter monitorare le prestazioni dei database SQL di Azure e/o dei pool elastici, sarà necessario **configurare ogni** Database SQL di Azure e ogni risorsa del pool elastico che si desidera monitorare per trasmettere i dati di telemetria di diagnostica alla soluzione.

- Abilitare Diagnostica di Azure per i database SQL di Azure SQL e i pool elastici e [configurarli per l'invio dei dati a Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

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

Quando si aggiunge la soluzione all'area di lavoro, il riquadro Azure SQL Analytics viene aggiunto all'area di lavoro e visualizzato in Panoramica. Il riquadro mostra il numero di database SQL di Azure e di pool elastici SQL a cui la soluzione è connessa.

![Riquadro Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Visualizzazione dei dati di Analisi SQL di Azure

Fare clic sul riquadro **Analisi SQL di Azure** per aprire il dashboard Analisi SQL di Azure. Il dashboard include la panoramica di tutti i database monitorati tramite prospettive diverse. Per potere usare diverse prospettive, è necessario abilitare le metriche o i log appropriati nelle risorse SQL di cui deve essere eseguito lo streaming nell'area di lavoro di Azure Log Analytics.

![Panoramica di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

La selezione di uno dei riquadri consente di visualizzare un report drill-down nella prospettiva specifica. Dopo che la prospettiva è stata selezionata, viene aperto il report drilldown.

![Timeout di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Ogni prospettiva fornisce riepiloghi relativi a sottoscrizione, server, pool elastico e livello di database. Inoltre ogni prospettiva mostra una prospettiva specifica del report a destra. Selezionando una sottoscrizione, un server, un pool o un database nell'elenco, il drilldown continua.

| Prospettiva | DESCRIZIONE |
| --- | --- |
| Risorsa per tipo | Prospettiva che conta tutte le risorse monitorate. Il drill-down fornisce il riepilogo delle metriche relative a DTU e GB. |
| Informazioni dettagliate | Fornisce il drill-down gerarchico per Intelligent Insights. Altre informazioni su Intelligent Insights. |
| Errors | Fornisce il drill-down gerarchico per gli errori SQL verificatisi nei database. |
| Timeout | Fornisce il drill-down gerarchico per i timeout SQL verificatisi nei database. |
| Blocchi | Fornisce il drill-down gerarchico per i blocchi SQL verificatisi nei database. |
| Attese del database | Fornisce il drill-down gerarchico per le statistiche di attesa SQL a livello di database. Include il riepilogo del tempo di attesa totale e del tempo di attesa per tipo di attesa. |
| Durata delle query | Fornisce il drill-down gerarchico per le statistiche di esecuzione delle query, ad esempio la durata della query, l'utilizzo della CPU, l'utilizzo dei dati di I/O e l'utilizzo dei log di I/O. |
| Attese query | Fornisce il drill-down gerarchico per le statistiche di attesa delle query per categoria di attesa. |

### <a name="intelligent-insights-report"></a>Report di Intelligent Insights

[Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) per il database SQL di Azure assicura un maggior controllo sulle prestazioni del database. È possibile visualizzare e accedere a tutti i dati di Intelligent Insights raccolti tramite la prospettiva di Intelligent Insights.

![Informazioni dettagliate di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Report relativi a pool elastici e database

I pool elastici e i database sono associati a report specifici, che mostrano tutti i dati raccolti per la risorsa nel periodo di tempo specificato.

![Database di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool elastico di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Report delle query

Tramite le prospettive relativa a durata e attese delle query, è possibile correlare le prestazioni di qualsiasi query attraverso il report della query. Questo report confronta le prestazioni della query in database diversi e semplifica l'individuazione dei database che eseguono la query selezionata in modo ottimale, rispetto ai database lenti.

![Query di Analisi SQL di Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analizzare i dati e creare avvisi

È possibile [creare avvisi](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) facilmente con i dati provenienti dalle risorse del database SQL di Azure. Di seguito sono riportate alcune query utili di [ricerca log](log-analytics-log-searches.md) che è possibile usare con un avviso log:



*Elevato utilizzo di DTU nel database SQL di Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Elevato utilizzo di DTU nel pool elastico del database SQL di Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Passaggi successivi

- Usare le [ricerche log](log-analytics-log-searches.md) in Log Analytics per visualizzare i dati dettagliati per Azure SQL.
- [Creare dashboard personalizzati](log-analytics-dashboards.md) che mostrino i dati per Azure SQL.
- [Creare avvisi](log-analytics-alerts.md) quando si verificano eventi specifici relativi ad Azure SQL.
