---
title: Gestire la potenza di calcolo in Azure SQL Data Warehouse (REST) | Documentazione Microsoft
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
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41ab1c4b2709c2ea6890ca526db1dea177b7da1b


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

Ridimensionare le prestazioni tramite la scalabilità orizzontale delle risorse di calcolo e della memoria per soddisfare le diverse esigenze del carico di lavoro. Risparmiare sui costi ridimensionando le risorse durante le ore non di punta o sospendendo completamente il calcolo. 

Questo gruppo di attività usa T-SQL per:

* Visualizzare le impostazioni DWU correnti
* Modificare le risorse di calcolo cambiando il numero di DWU

Per sospendere o riprendere un database, scegliere una delle altre opzioni di piattaforma nella parte superiore di questo articolo.

Per altre informazioni, vedere la [Panoramica sulla gestione della potenza di calcolo][Panoramica sulla gestione della potenza di calcolo].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Visualizzare le impostazioni DWU correnti
Per visualizzare le impostazioni DWU correnti per i database:

1. Aprire Esplora oggetti di SQL Server in Visual Studio 2015.
2. Connettersi al database master associato al server di database SQL logico.
3. Effettuare una selezione nella vista a gestione dinamica sys.database_service_objectives. Di seguito è fornito un esempio: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le DWU:

1. Connettersi al database master associato al server di database SQL logico.
2. Usare l'istruzione TSQL [ALTER DATABASE][ALTER DATABASE]. L'esempio seguente imposta l'obiettivo del livello di servizio su DW1000 per il database MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Per altre attività di gestione, vedere la [Panoramica della gestione][Panoramica della gestione].

<!--Image references-->

<!--Article references-->
[Limiti di capacità del servizio]: ./sql-data-warehouse-service-capacity-limits.md
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Panoramica sulla gestione della potenza di calcolo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Portale di Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


