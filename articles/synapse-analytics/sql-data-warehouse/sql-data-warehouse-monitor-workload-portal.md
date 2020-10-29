---
title: Monitorare il carico di lavoro-portale di Azure
description: Monitorare le sinapsi SQL usando il portale di Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 53c68f44273b14658bd4d95ce7cf3cf847c077d0
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025953"
---
# <a name="monitor-workload---azure-portal"></a>Monitorare il carico di lavoro-portale di Azure

Questo articolo descrive come usare la portale di Azure per monitorare il carico di lavoro. Ciò include la configurazione dei log di monitoraggio di Azure per analizzare le tendenze di esecuzione delle query e del carico di lavoro con log Analytics per [sinapsi SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Pool SQL: verranno raccolti i log per un pool SQL. Se non è stato eseguito il provisioning di un pool SQL, vedere le istruzioni riportate in [creare un pool SQL](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Passare al pannello Sfoglia per Log Analytics aree di lavoro e creare un'area di lavoro

![Aree di lavoro di Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Screenshot mostra le aree di lavoro Log Analytics in cui è possibile selezionare Aggiungi.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Screenshot mostra l'area di lavoro Log Analytics in cui è possibile immettere i valori.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Per ulteriori informazioni sulle aree di lavoro, vedere la [documentazione](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)seguente.

## <a name="turn-on-resource-logs"></a>Attivare i log delle risorse

Configurare le impostazioni di diagnostica per emettere log dal pool SQL. I log sono costituiti da visualizzazioni di telemetria equivalenti alla risoluzione dei problemi di prestazioni più comunemente usata DMV. Attualmente sono supportate le visualizzazioni seguenti:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Abilitazione dei log delle risorse](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

I log possono essere emessi in archiviazione di Azure, analisi di flusso o Log Analytics. Per questa esercitazione, selezionare Log Analytics.

![Specificare i log](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Eseguire query su Log Analytics

Passare all'area di lavoro Log Analytics in cui è possibile eseguire le operazioni seguenti:

- Analizzare i log usando le query di log e salvare le query per il riutilizzo
- Salva le query per il riutilizzo
- Creare avvisi di log
- Aggiungere i risultati della query a un dashboard

Per informazioni dettagliate sulle funzionalità delle query di log, vedere la [documentazione](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)seguente.

![Editor dell'area di lavoro Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Query dell'area di lavoro Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Ora che sono stati impostati e configurati i log di monitoraggio di Azure, è possibile [personalizzare i dashboard di Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) per condividerli nel team.
