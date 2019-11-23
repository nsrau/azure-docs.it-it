---
title: Gestire la conservazione di backup a lungo termine
description: Informazioni su come archiviare i backup automatizzati nella risorsa di archiviazione di SQL Azure e quindi ripristinarli
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: a560f4f1399792a7b150b37c3c048ccc0079b98d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420798"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gestire la conservazione a lungo termine dei backup del database SQL di Azure

In Azure SQL Database, you can configure a single or a pooled database with a [long-term backup retention](sql-database-long-term-retention.md) policy (LTR) to automatically retain the database backups in separate Azure Blob storage containers for up to 10 years. È quindi possibile ripristinare un database usando questi backup tramite il portale di Azure o PowerShell.

> [!IMPORTANT]
> [Azure SQL database managed instance](sql-database-managed-instance.md) does not currently support long-term backup retention.

## <a name="using-azure-portal"></a>Uso del portale di Azure

Le sezioni seguenti mostrano come usare il portale di Azure per configurare il periodo di conservazione a lungo termine, visualizzare i backup con conservazione a lungo termine e ripristinare il backup dalla conservazione a lungo termine.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare il database SQL per [conservare i backup automatizzati](sql-database-long-term-retention.md) per un periodo più lungo rispetto al periodo di conservazione associato al livello di servizio.

1. Nel portale di Azure selezionare l'istanza di SQL Server e quindi fare clic su **Gestisci backup**. Nella scheda **Configura criteri** selezionare la casella di controllo relativa al database in cui si vogliono impostare o modificare i criteri di conservazione a lungo termine dei backup. Se la casella di controllo accanto al database non è selezionata, le modifiche dei criteri non verranno applicate al database.  

   ![collegamento di gestione backup](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Nel riquadro **Configure policies** (Configura criteri) scegliere se si vuole conservare i backup per settimane, mesi o anni e specificare per ognuno il periodo di conservazione.

   ![Configurare criteri](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Al termine, fare clic su **Applica**.

> [!IMPORTANT]
> When you enable a long-term backup retention policy, it may take up to 7 days for the first backup to become visible and available to restore. For details of the LTR backup cadance, see [long-term backup retention](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>View backups and restore from a backup

Visualizzare i backup conservati per un database specifico con i criteri di conservazione a lungo termine ed eseguire il ripristino da tali backup.

1. Nel portale di Azure selezionare l'istanza di SQL Server e quindi fare clic su **Gestisci backup**. Nella scheda **Available backups** (Backup disponibili) selezionare il database per cui si vuole visualizzare i backup disponibili.

   ![Selezionare il database](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. Nel riquadro **Available backups** (Backup disponibili) esaminare i backup disponibili.

   ![Visualizzare i backup](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Selezionare il backup da cui si vuole eseguire il ripristino e quindi specificare il nome del nuovo database.

   ![ripristinare](./media/sql-database-long-term-retention/ltr-restore.png)

1. Fare clic su **OK** per ripristinare nel nuovo database il database dal backup presente nella risorsa di archiviazione di Azure SQL.

1. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento processo di ripristino](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. Al termine del processo di ripristino, aprire la pagina **Database SQL** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione a lungo termine dei backup, visualizzare i backup nella risorsa di archiviazione di Azure SQL ed eseguire il ripristino da un backup nella risorsa di archiviazione di Azure SQL.

### <a name="rbac-roles-to-manage-long-term-retention"></a>Ruoli Controllo degli accessi in base al ruolo per gestire la conservazione a lungo termine

For **Get-AzSqlDatabaseLongTermRetentionBackup** and **Restore-AzSqlDatabase**, you will need to have one of the following roles:

- Subscription Owner role or
- SQL Server Contributor role or
- Custom role with the following permissions:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

For **Remove-AzSqlDatabaseLongTermRetentionBackup**, you will need to have one of the following roles:

- Subscription Owner role or
- Custom role with the following permission:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> The SQL Server Contributor role does not have permission to delete LTR backups.

RBAC permissions could be granted in either *subscription* or *resource group* scope. However, to access LTR backups that belong to a dropped server, the permission must be granted in the *subscription* scope of that server.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visualizzare i criteri di conservazione a lungo termine

Questo esempio illustra come elencare i criteri di conservazione a lungo termine in un server

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>Cancellare i criteri di conservazione a lungo termine

Questo esempio illustra come cancellare i criteri di conservazione a lungo termine da un database

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visualizzare i backup con conservazione a lungo termine

Questo esempio illustra come elencare i backup con conservazione a lungo termine in un server.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. To delete an LTR backup after the server has been deleted you must have Subscription scope permission. È possibile impostare le notifiche su ogni eliminazione in Monitoraggio di Azure filtrando in base all'operazione di eliminazione di un backup con conservazione a lungo termine. Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../azure-monitor/platform/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine

Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro dell'ID risorsa richiede ora l'ID risorsa del backup con conservazione a lungo termine.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> To restore from an LTR backup after the server has been deleted, you must have permissions scoped to the server's subscription and that subscription must be active. You must also omit the optional -ResourceGroupName parameter.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
