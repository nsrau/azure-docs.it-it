---
title: Gestire e monitorare la priorità del carico di lavoro
description: Informazioni su come gestire e monitorare l'importanza del livello di richiesta in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692702"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gestire e monitorare l'importanza del carico di lavoro in Azure SQL Data Warehouse

Gestire e monitorare l'importanza del livello di richiesta nelle Azure SQL Data Warehouse usando DMV e le viste del catalogo.

## <a name="monitor-importance"></a>Importanza del monitoraggio

Monitorare l'importanza usando la nuova colonna importanza nella vista a gestione dinamica [sys. dm _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
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

La vista del catalogo sys. workload_management_workload_classifiers contiene informazioni sui classificatori nell'istanza di Azure SQL Data Warehouse. Per escludere i classificatori definiti dal sistema che eseguono il mapping alle classi di risorse, eseguire il codice seguente:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

La vista del catalogo [sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)contiene informazioni sui parametri utilizzati per la creazione del classificatore.  La query seguente mostra che ExecReportsClassifier è stato creato nel parametro ```membername``` per i valori con ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Risultati query](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Per semplificare la risoluzione dei problemi di classificazione, è consigliabile rimuovere i mapping dei ruoli della classe di risorse durante la creazione dei classificatori del carico di lavoro. Il codice seguente restituisce le appartenenze ai ruoli della classe di risorse esistenti. Eseguire sp_droprolemember per ogni ```membername``` restituito dalla classe di risorse corrispondente.
Di seguito è riportato un esempio di verifica dell'esistenza prima di eliminare un classificatore del carico di lavoro:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulla classificazione, vedere [classificazione dei carichi di lavoro](sql-data-warehouse-workload-classification.md).
- Per ulteriori informazioni sull'importanza, vedere [priorità del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passare a configurare l'importanza del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md)
