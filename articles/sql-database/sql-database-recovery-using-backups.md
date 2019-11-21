---
title: Restore a database from a backup
description: Learn about point-in-time restore, which enables you to roll back an Azure SQL database up to 35 days.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: 3b0b5b02fa8f369bdfa03726bd5649b70b7bbd48
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228039"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Recover an Azure SQL database by using automated database backups

By default, Azure SQL Database backups are stored in geo-replicated blob storage (RA-GRS storage type). The following options are available for database recovery by using [automated database backups](sql-database-automated-backups.md). Puoi:

- Create a new database on the same SQL Database server, recovered to a specified point in time within the retention period.
- Create a database on the same SQL Database server, recovered to the deletion time for a deleted database.
- Create a new database on any SQL Database server in the same region, recovered to the point of the most recent backups.
- Create a new database on any SQL Database server in any other region, recovered to the point of the most recent replicated backups.

If you configured [backup long-term retention](sql-database-long-term-retention.md), you can also create a new database from any long-term retention backup on any SQL Database server.

> [!IMPORTANT]
> You can't overwrite an existing database during restore.

When you're using the Standard or Premium service tiers, your database restore might incur an extra storage cost. The extra cost is incurred when the maximum size of the restored database is greater than the amount of storage included with the target database's service tier and performance level. Per i dettagli dei prezzi delle risorse di archiviazione aggiuntive, vedere la [pagina dei prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/). If the actual amount of used space is less than the amount of storage included, you can avoid this extra cost by setting the maximum database size to the included amount.

## <a name="recovery-time"></a>Tempo di ripristino

The recovery time to restore a database by using automated database backups is affected by several factors:

- The size of the database.
- The compute size of the database.
- The number of transaction logs involved.
- The amount of activity that needs to be replayed to recover to the restore point.
- The network bandwidth if the restore is to a different region.
- Il numero di richieste simultanee di ripristino in corso di elaborazione nell'area di destinazione.

For a large or very active database, the restore might take several hours. If there is a prolonged outage in a region, it's possible that a high number of geo-restore requests will be initiated for disaster recovery. When there are many requests, the recovery time for individual databases can increase. Per la maggior parte dei database, il ripristino richiede meno di 12 ore.

For a single subscription, there are limitations on the number of concurrent restore requests. These limitations apply to any combination of point-in-time restores, geo-restores, and restores from long-term retention backup.

| | **Numero massimo di richieste simultanee elaborate** | **Numero massimo di richieste simultanee inviate** |
| :--- | --: | --: |
|Database singolo (per sottoscrizione)|10|60|
|Pool elastico (per pool)|4|200|
||||

There isn't a built-in method to restore the entire server. For an example of how to accomplish this task, see [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> To recover by using automated backups, you must be a member of the SQL Server contributor role in the subscription, or be the subscription owner. For more information, see [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md). You can recover by using the Azure portal, PowerShell, or the REST API. You can't use Transact-SQL.

## <a name="point-in-time-restore"></a>Ripristino temporizzato

You can restore a standalone, pooled, or instance database to an earlier point in time by using the Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), or the [REST API](https://docs.microsoft.com/rest/api/sql/databases). The request can specify any service tier or compute size for the restored database. Ensure that you have sufficient resources on the server to which you are restoring the database. When complete, the restore creates a new database on the same server as the original database. The restored database is charged at normal rates, based on its service tier and compute size. You don't incur charges until the database restore is complete.

In genere si ripristina un database a un punto precedente per scopi di ripristino. You can treat the restored database as a replacement for the original database, or use it as a data source to update the original database.

- **Sostituzione del database**

  If you intend the restored database to be a replacement for the original database, you should specify the original database's compute size and service tier. You can then rename the original database, and give the restored database the original name by using the [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) command in T-SQL.

- **Ripristino dei dati**

  If you plan to retrieve data from the restored database to recover from a user or application error, you need to write and execute a  data recovery script that extracts data from the restored database and applies to the original database. Anche se il completamento dell'operazione di ripristino può richiedere molto tempo, il database in fase di ripristino è visibile nell'elenco dei database per tutto il processo. If you delete the database during the restore, the restore operation will be canceled and you will not be charged for the database that did not complete the restore.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Point-in-time restore by using Azure portal

You can recover a single SQL database or instance database to a point in time from the overview blade of the database you want to restore in the Azure portal.

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

To recover a single or pooled database to a point in time by using the Azure portal, open the database overview page, and select **Restore** on the toolbar. Choose the backup source, and select the point-in-time backup point from which a new database will be created. 

  ![Screenshot of database restore options](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Managed instance database

To recover a managed instance database to a point in time by using the Azure portal, open the database overview page, and select **Restore** on the toolbar. Choose the point-in-time backup point from which a new database will be created. 

  ![Screenshot of database restore options](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> To programmatically restore a database from a backup, see [Programmatically performing recovery using automated backups](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Ripristino di un database eliminato

You can restore a deleted database to the deletion time, or an earlier point in time, on the same SQL Database server or the same managed instance. You can accomplish this through the Azure portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), or the [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). You restore a deleted database by creating a new database from the backup.

> [!IMPORTANT]
> If you delete an Azure SQL Database server or managed instance, all its databases are also deleted, and can't be recovered. You can't restore a deleted server or managed instance.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Deleted database restore by using the Azure portal

You restore deleted databases from the Azure portal from the server and instance resource.

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

To recover a single or pooled deleted database to the deletion time by using the Azure portal, open the server overview page, and select **Deleted databases**. Select a deleted database that you want to restore, and type the name for the new database that will be created with data restored from the backup.

  ![Screenshot of restore deleted Azure SQL database](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Managed instance database

To recover a managed database by using the Azure portal, open the managed instance overview page, and select **Deleted databases**. Select a deleted database that you want to restore, and type the name for the new database that will be created with data restored from the backup.

  ![Screenshot of restore deleted Azure SQL instance database](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Deleted database restore by using PowerShell

Use the following sample scripts to restore a deleted database for Azure SQL Database and a managed instance by using PowerShell.

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

For a sample PowerShell script showing how to restore a deleted Azure SQL database, see [Restore a SQL database using PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Managed instance database

For a sample PowerShell script showing how to restore a deleted instance database, see [Restore deleted database on managed instance using PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> To programmatically restore a deleted database, see [Programmatically performing recovery using automated backups](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Ripristino geografico

Il ripristino geografico consente di ripristinare un database SQL presente su un server in qualsiasi area di Azure usando gli ultimi backup con replica geografica. Geo-restore uses a geo-replicated backup as its source. You can request geo-restore even if the database or datacenter is inaccessible due to an outage.

Geo-restore is the default recovery option when your database is unavailable because of an incident in the hosting region. You can restore the database to a server in any other region. Esiste un ritardo tra il momento in cui un backup viene creato e quando ne viene eseguita la replica geografica in un BLOB di Azure in un'area diversa. As a result, the restored database can be up to one hour behind the original database. The following illustration shows a database restore from the last available backup in another region.

![Graphic of geo-restore](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-restore by using the Azure portal

From the Azure portal, you create a new single or managed instance database, and select an available geo-restore backup. The newly created database contains the geo-restored backup data.

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

To geo-restore a single SQL database from the Azure portal in the region and server of your choice, follow these steps:

1. From **Dashboard**, select **Add** > **Create SQL Database**. On the **Basics** tab, enter the required information.
2. Select **Additional settings**.
3. For **Use existing data**, select **Backup**.
4. For **Backup**, select a backup from the list of available geo-restore backups.

    ![Screenshot of Create SQL Database options](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Complete the process of creating a new database from the backup. When you create the single Azure SQL database, it contains the restored geo-restore backup.

#### <a name="managed-instance-database"></a>Managed instance database

To geo-restore a managed instance database from the Azure portal to an existing managed instance in a region of your choice, select a managed instance on which you want a database to be restored. Seguire questa procedura:

1. Select **New database**.
2. Type a desired database name.
3. Under **Use existing data**, select **Backup**.
4. Select a backup from the list of available geo-restore backups.

    ![Screenshot of New database options](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Complete the process of creating a new database. When you create the instance database, it contains the restored geo-restore backup.

### <a name="geo-restore-by-using-powershell"></a>Geo-restore by using PowerShell

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

For a PowerShell script that shows how to perform geo-restore for a single SQL database, see [Use PowerShell to restore an Azure SQL single database to an earlier point in time](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Managed instance database

For a PowerShell script that shows how to perform geo-restore for a managed instance database, see [Use PowerShell to restore a managed instance database to another geo-region](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Geo-restore considerations

You can't perform a point-in-time restore on a geo-secondary database. You can only do so on a primary database. Per informazioni dettagliate sull'uso del ripristino geografico in caso di un'interruzione del servizio, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Geo-restore is the most basic disaster recovery solution available in SQL Database. It relies on automatically created geo-replicated backups with recovery point objective (RPO) equal to 1 hour, and the estimated recovery time of up to 12 hours. It doesn't guarantee that the target region will have the capacity to restore your databases after a regional outage, because a sharp increase of demand is likely. If your application uses relatively small databases and is not critical to the business, geo-restore is an appropriate disaster recovery solution. For business-critical applications that require large databases and must ensure business continuity, use [Auto-failover groups](sql-database-auto-failover-group.md). It offers a much lower RPO and recovery time objective, and the capacity is always guaranteed. Per altre informazioni sulle opzioni di continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programmatically performing recovery by using automated backups

You can also use Azure PowerShell or the REST API for recovery. Le tabelle seguenti descrivono il set di comandi disponibili.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Arguments for the commands in the Az module and in AzureRm modules are to a great extent identical.

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

To restore a standalone or pooled database, see [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Ottiene uno o più database. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Ottiene una copia di backup con ridondanza geografica di un database. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Ripristina un database SQL. |

  > [!TIP]
  > For a sample PowerShell script that shows how to perform a point-in-time restore of a database, see [Restore a SQL database using PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Managed instance database

To restore a managed instance database, see [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Gets one or more managed instances. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Gets an instance database. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restores an instance database. |

### <a name="rest-api"></a>API REST

To restore a single or pooled database by using the REST API:

| API SmartBear Ready! | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restores a database. |
| [Get Create or Update Database Status](https://docs.microsoft.com/rest/api/sql/operations) |Returns the status during a restore operation. |

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

#### <a name="single-azure-sql-database"></a>Single Azure SQL database

To restore a single or pooled database by using the Azure CLI, see [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Managed instance database

To restore a managed instance database by using the Azure CLI, see [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Summary

I backup automatici proteggono i database da errori dell'utente e delle applicazioni, dall'eliminazione accidentale e da interruzioni prolungate. Questa funzionalità incorporata è disponibile per tutti i livelli di servizio e le dimensioni di calcolo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Backup automatici del database SQL](sql-database-automated-backups.md)
- [Conservazione a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni su opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-active-geo-replication.md) o [Gruppi di failover automatico](sql-database-auto-failover-group.md).
