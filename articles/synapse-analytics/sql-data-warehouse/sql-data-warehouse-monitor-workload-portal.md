---
title: Monitorare il carico di lavoro - Portale di AzureMonitor workload - Azure portal
description: Monitorare Synapse SQL usando il portale di AzureMonitor Synapse SQL using the Azure portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 9eacb813c3ddce028fcd9b24c86c6d32ed7a7584
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633217"
---
# <a name="monitor-workload---azure-portal"></a>Monitorare il carico di lavoro - Portale di AzureMonitor workload - Azure portal

Questo articolo descrive come usare il portale di Azure per monitorare il carico di lavoro. Ciò include la configurazione dei log di Monitoraggio di Azure per analizzare le tendenze dell'esecuzione delle query e del carico di lavoro usando l'analisi dei log per [Synapse SQL.](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Pool SQL: verranno raccolti i log per un pool SQL. Se non è stato eseguito il provisioning di un pool SQL, vedere le istruzioni in [Creare un pool SQL.](load-data-from-azure-blob-storage-using-polybase.md)

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Passare al pannello di esplorazione per le aree di lavoro di Log Analytics e creare un'area di lavoro

![Aree di lavoro di Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Area di lavoro Aggiungi analisi](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Area di lavoro Aggiungi analisi](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Per ulteriori informazioni sulle aree di lavoro, consultare la [documentazione](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)seguente.

## <a name="turn-on-diagnostic-logs"></a>Attivare i registri di diagnostica

Configurare le impostazioni di diagnostica per generare log dal pool SQL. I log sono costituiti da viste di telemetria equivalenti alle DMV per la risoluzione dei problemi di prestazioni più comunemente usate. Attualmente sono supportate le seguenti viste:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)

![Abilitazione dei log di diagnostica](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

I log possono essere generati in Archiviazione di Azure, Analisi di flusso o Log Analytics.Logs can be mitted to Azure Storage, Stream Analytics, or Log Analytics. Per questa esercitazione, selezionare Log Analytics.For this tutorial, select Log Analytics.

![Specificare i registri](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Eseguire query su Log Analytics

Passare all'area di lavoro di Log Analytics in cui è possibile eseguire le operazioni seguenti:Navigate to your Log Analytics workspace where you can do the following:

- Analizzare i log utilizzando le query di log e salvare le query per il riutilizzoAnalyze logs using log queries and save queries for reuse
- Salvare le query per il riutilizzo
- Creare avvisi di log
- Aggiungere risultati di query a un dashboard

Per informazioni dettagliate sulle funzionalità delle query di log, visitare la [documentazione riportata](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)di seguito.

![Editor area di lavoro di Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Query dell'area di lavoro di Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Query di log di esempio

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato e configurato i log di monitoraggio di Azure, [personalizzare i dashboard](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) di Azure per la condivisione tra il team.
