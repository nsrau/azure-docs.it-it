---
title: Gestire e monitorare la priorità del carico di lavoro
description: Informazioni su come gestire e monitorare l'importanza a livello di richiesta in Azure Synapse Analytics.Learn how to manage and monitor request level importance in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 114f8d637a927a899807a676fb3e1b45f5c7687c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585753"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Gestire e monitorare l'importanza del carico di lavoro in Azure Synapse AnalyticsManage and monitor workload importance in Azure Synapse Analytics

Gestire e monitorare l'importanza del livello di richiesta Synapse SQL in Azure Synapse usando DMV e viste del catalogo.

## <a name="monitor-importance"></a>Monitorare l'importanza

Monitorare l'importanza utilizzando la nuova colonna di importanza nella vista a gestione dinamica [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
La query di monitoraggio seguente mostra l'ora di invio e l'ora di inizio per le query. Esamina l'ora di invio e l'ora di inizio insieme all'importanza per vedere in che modo l'importanza ha influenzato la pianificazione.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Per approfondire la pianificazione delle query, utilizzare le viste del catalogo.

## <a name="manage-importance-with-catalog-views"></a>Gestire l'importanza con le visualizzazioni del catalogo

La visualizzazione del catalogo sys.workload_management_workload_classifiers contiene informazioni sui classificatori. Per escludere i classificatori definiti dal sistema che eseguono il mapping alle classi di risorse, eseguire il codice seguente:To exclude the system-defined classifiers that map to resource classes execute the following code:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

La vista del catalogo, [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contiene informazioni sui parametri utilizzati nella creazione del classificatore.  La query seguente mostra che ExecReportsClassifier è stato creato nel parametro per i ```membername``` valori con ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![risultati delle query](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Per semplificare la risoluzione dei problemi di classificazione errata, è consigliabile rimuovere i mapping dei ruoli delle classi di risorse durante la creazione dei classificatori del carico di lavoro. Il codice seguente restituisce le appartenenze ai ruoli della classe di risorsa esistenti. Eseguire sp_droprolemember ```membername``` per ogni restituita dalla classe di risorse corrispondente.
Di seguito è riportato un esempio di controllo dell'esistenza prima dell'eliminazione di un classificatore del carico di lavoro:Below is an example of checking for existence before dropping a workload classifier:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulla classificazione, vedere [Classificazione del carico di lavoro](sql-data-warehouse-workload-classification.md).
- Per altre informazioni sull'importanza, vedere [Importanza del carico di lavoroFor more](sql-data-warehouse-workload-importance.md) information on Importance, see Workload Importance

> [!div class="nextstepaction"]
> [Passare a Configurare l'importanza del carico di lavoro](sql-data-warehouse-how-to-configure-workload-importance.md)
