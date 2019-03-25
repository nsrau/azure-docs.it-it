---
title: Carico di lavoro di monitoraggio - portale di Azure | Microsoft Docs
description: Monitorare Azure SQL Data Warehouse nel portale di Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405783"
---
# <a name="monitor-workload---azure-portal"></a>Carico di lavoro di monitoraggio - portale di Azure

Questo articolo descrive come usare il portale di Azure per monitorare il carico di lavoro. È inoltre necessario impostare il log di monitoraggio di Azure per analizzare le tendenze nel carico di lavoro e l'esecuzione query usando log analitica per [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- Azure SQL Data Warehouse: Si vogliono raccogliere i log per un SQL data warehouse. Se non si dispone di un SQL data warehouse il provisioning, vedere le istruzioni in [creare un SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro di Log Analytics

Passare al pannello di esplorazione per le aree di lavoro di Log Analitica e creare un'area di lavoro 

![Aree di lavoro di Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Aggiungi area di lavoro di Analitica](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Aggiungi area di lavoro di Analitica](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Per altre informazioni sulle aree di lavoro, visitare il [documentazione](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica 

Configurare le impostazioni di diagnostica per generare i log di SQL data warehouse. I log sono costituiti da visualizzazioni di dati di telemetria del data warehouse equivalenti per le prestazioni più diffuso, risoluzione dei problemi di viste a gestione dinamica per SQL Data Warehouse. Attualmente sono supportate le viste seguenti:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Abilitazione dei log di diagnostica](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

I log possono essere creati per archiviazione di Azure, Stream Analitica o Log Analitica. Per questa esercitazione, selezionare Log Analitica.

![Specificare i log](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Eseguire query su Log Analitica

Passare all'area di lavoro di Log Analitica in cui è possibile eseguire le operazioni seguenti:

- Analizzare i log usando le query di log e salvare le query per il riutilizzo
- Salvare le query per il riutilizzo
- Creare avvisi del log
- Risultati della query pin a un dashboard

Per informazioni dettagliate sulle funzionalità di query di log, visitare il [documentazione](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor dell'area di lavoro di log Analitica](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Registrazione di query dell'area di lavoro di Analitica](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

Ora che è impostato e configurato i log di monitoraggio di Azure, [personalizzare i dashboard di Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) condividere tutto il team.
