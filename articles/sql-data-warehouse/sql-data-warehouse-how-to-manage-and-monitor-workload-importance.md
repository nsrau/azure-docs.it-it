---
title: Gestire e monitorare l'importanza del carico di lavoro in Azure SQL Data Warehouse | Microsoft Docs
description: Informazioni su come gestire e monitorare l'importanza a livello di richiesta.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235937"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gestire e monitorare l'importanza del carico di lavoro in Azure SQL Data Warehouse

Gestire e monitorare importanza a livello di richiesta in Azure SQL Data Warehouse tramite viste del catalogo e viste a gestione dinamica.

## <a name="monitor-importance"></a>Importanza di monitoraggio

Monitorare importanza usando la nuova colonna di priorità nei [DM pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) vista a gestione dinamica.
La seguente query Mostra ora di invio / ora di inizio per le query di monitoraggio. Esaminare l'ora di invio e ora insieme a importanza per vedere come priorità possono dipendere la pianificazione di avvio.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Per esaminare ulteriormente la modalità query è in corso pianificazione, utilizzare le viste del catalogo.

## <a name="manage-importance-with-catalog-views"></a>Gestire l'importanza con le viste del catalogo

La vista del catalogo sys.workload_management_workload_classifiers contiene informazioni su classificatori nell'istanza di Azure SQL Data Warehouse. Per escludere i classificatori definito dal sistema che eseguono il mapping a classi di risorse eseguire il codice seguente:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

La vista del catalogo [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contiene informazioni sui parametri usati nella creazione del classificatore.  La seguente query mostra che è stato creato ExecReportsClassifier il ```membername``` parametro per i valori con ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Risultati della query](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Per semplificare la risoluzione dei problemi di errata classificazione, è consigliabile che rimuoverlo mapping dei ruoli di classe di risorse durante la creazione di classificatori di carico di lavoro. Il codice seguente restituisce la risorsa esistente le appartenenze ai ruoli di classe. Eseguire sp_droprolemember per ognuno ```membername``` restituiti dalla classe di risorse corrispondente.
Di seguito è riportato un esempio di verifica dell'esistenza prima di eliminare un classificatore di carico di lavoro:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulla classificazione, vedere [carico di lavoro classificazione](sql-data-warehouse-workload-classification.md).
- Per altre informazioni sull'importanza, vedere [importanza del carico di lavoro](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Passa a configurare la priorità del carico di lavoro ](sql-data-warehouse-how-to-configure-workload-importance.md)
