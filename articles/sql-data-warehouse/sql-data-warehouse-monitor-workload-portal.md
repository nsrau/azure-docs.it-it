---
title: Monitorare il carico di lavoro-portale di Azure
description: Monitorare SQL Analytics usando il portale di Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 7e93aee405d8a66d850a4e3f07f2e788f1004ef8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197234"
---
# <a name="monitor-workload---azure-portal"></a>Monitorare il carico di lavoro-portale di Azure

Questo articolo descrive come usare la portale di Azure per monitorare il carico di lavoro. Questa operazione include la configurazione dei log di monitoraggio di Azure per analizzare le tendenze di esecuzione delle query e del carico di lavoro con log Analytics per [analisi SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Prerequisites

- Sottoscrizione di Azure: se non è disponibile una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Pool SQL: verranno raccolti i log per un pool SQL. Se non è stato eseguito il provisioning di un pool SQL, vedere le istruzioni riportate in [creare un pool SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Passare al pannello Sfoglia per Log Analytics aree di lavoro e creare un'area di lavoro 

![Aree di lavoro di Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Aggiungi area di lavoro Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Aggiungi area di lavoro Analytics](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Per ulteriori informazioni sulle aree di lavoro, vedere la [documentazione](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)seguente.

## <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica

Configurare le impostazioni di diagnostica per emettere log dal pool SQL. I log sono costituiti da visualizzazioni di telemetria equivalenti alla risoluzione dei problemi di prestazioni più comunemente usata DMV. Attualmente sono supportate le visualizzazioni seguenti:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Abilitazione dei log di diagnostica](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

I log possono essere emessi in archiviazione di Azure, analisi di flusso o Log Analytics. Per questa esercitazione, selezionare Log Analytics.

![Specificare i log](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Eseguire query su Log Analytics

Passare all'area di lavoro Log Analytics in cui è possibile eseguire le operazioni seguenti:

- Analizzare i log usando le query di log e salvare le query per il riutilizzo
- Salva le query per il riutilizzo
- Creare avvisi di log
- Aggiungere i risultati della query a un dashboard

Per informazioni dettagliate sulle funzionalità delle query di log, vedere la [documentazione](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)seguente.

![Editor dell'area di lavoro Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Query dell'area di lavoro Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

Ora che sono stati impostati e configurati i log di monitoraggio di Azure, è possibile [personalizzare i dashboard di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) per condividerli nel team.
