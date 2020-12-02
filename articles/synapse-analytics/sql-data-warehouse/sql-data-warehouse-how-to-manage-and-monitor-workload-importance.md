---
title: Gestire e monitorare l'importanza del carico di lavoro nel pool SQL dedicato
description: Informazioni su come gestire e monitorare l'importanza del livello di richiesta pool SQL dedicato per l'analisi delle sinapsi di Azure.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: fb0a5fbf33b48521882646bf8fb5eb3fe5dacca6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459203"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Gestire e monitorare l'importanza del carico di lavoro nel pool SQL dedicato per analisi sinapsi di Azure

Gestire e monitorare l'importanza del livello di richiesta del pool SQL dedicato nella sinapsi di Azure con DMV e viste del catalogo.

## <a name="monitor-importance"></a>Importanza del monitoraggio

Monitorare l'importanza usando la nuova colonna importanza nella vista a gestione dinamica [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .
La query di monitoraggio seguente mostra l'ora di invio e l'ora di inizio per le query. Esaminare l'ora di invio e l'ora di inizio insieme alla priorità per vedere come l'importanza ha influenzato la pianificazione.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Per approfondire le modalità di pianificazione delle query, utilizzare le viste del catalogo.

## <a name="manage-importance-with-catalog-views"></a>Gestire l'importanza con le viste del catalogo

La vista del catalogo sys.workload_management_workload_classifiers contiene informazioni sui classificatori. Per escludere i classificatori definiti dal sistema che eseguono il mapping alle classi di risorse, eseguire il codice seguente:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

La vista del catalogo, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), contiene informazioni sui parametri utilizzati per la creazione del classificatore.  La query seguente mostra che ExecReportsClassifier è stato creato sul ```membername``` parametro per i valori con ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![risultati delle query](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Per semplificare la risoluzione dei problemi di classificazione, è consigliabile rimuovere i mapping dei ruoli della classe di risorse durante la creazione dei classificatori del carico di lavoro. Il codice seguente restituisce le appartenenze ai ruoli della classe di risorse esistenti. Eseguire sp_droprolemember per ogni ```membername``` restituito dalla classe di risorse corrispondente.
Di seguito è riportato un esempio di verifica dell'esistenza prima di eliminare un classificatore del carico di lavoro:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla classificazione, vedere [classificazione dei carichi di lavoro](sql-data-warehouse-workload-classification.md).
- Per altre informazioni sulla priorità, vedere [Priorità del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare a configurare l'importanza del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md)
