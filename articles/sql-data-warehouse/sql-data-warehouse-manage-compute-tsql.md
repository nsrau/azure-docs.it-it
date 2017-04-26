---
title: Sospendere, riprendere e ridimensionare con T-SQL in Azure SQL Data Warehouse | Documentazione Microsoft
description: "Attività di Transact-SQL (T-SQL) per scalare orizzontalmente le prestazioni cambiando il numero delle DWU. Risparmiare sui costi eseguendo una scalabilità orizzontale durante le ore non di punta."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e37a943c775261a6817169c95a931f1b268305d9
ms.lasthandoff: 03/31/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gestire la potenza di calcolo in Azure SQL Data Warehouse (T-SQL)
> [!div class="op_single_selector"]
> * [Panoramica](sql-data-warehouse-manage-compute-overview.md)
> * [Portale](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Visualizzare le impostazioni DWU correnti
Per visualizzare le impostazioni DWU correnti per i database:

1. Aprire Esplora oggetti di SQL Server in Visual Studio.
2. Connettersi al database master associato al server di database SQL logico.
3. Effettuare una selezione nella vista a gestione dinamica sys.database_service_objectives. Di seguito è fornito un esempio: 

```sql
SELECT
    db.name [Database]
,    ds.edition [Edition]
,    ds.service_objective [Service Objective]
FROM
     sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le DWU:

1. Connettersi al database master associato al server di database SQL logico.
2. Usare l'istruzione T-SQL [ALTER DATABASE][ALTER DATABASE]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>Controllare lo stato del database e l'avanzamento dell'operazione

1. Connettersi al database master associato al server di database SQL logico.
2. Inviare query per controllare lo stato del database

```sql
SELECT *
FROM
sys.databases
```

3. Inviare query per controllare lo stato dell'operazione

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

Questa DMV restituisce informazioni sulle varie operazioni di gestione in SQL Data Warehouse, ad esempio l'operazione e lo stato dell'operazione, che può essere IN_PROGRESS o COMPLETED.



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Per altre attività di gestione, vedere [Panoramica della gestione][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

