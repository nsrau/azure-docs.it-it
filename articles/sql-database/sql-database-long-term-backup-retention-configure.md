---
title: Gestire la conservazione dei backup a lungo termine del database SQL di Azure | Microsoft Docs
description: Informazioni su come archiviare i backup automatizzati nella risorsa di archiviazione di SQL Azure e quindi ripristinarli
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 29bfc914dd5c1f4c8b5405ff0e7202b767d032b8
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gestire la conservazione a lungo termine dei backup del database SQL di Azure

È possibile configurare il database SQL di Azure con i criteri di [conservazione a lungo termine dei backup](sql-database-long-term-retention.md) per conservare automaticamente i backup nell'archivio BLOB di Azure fino a 10 anni. È quindi possibile ripristinare un database usando questi backup tramite il portale di Azure o PowerShell.

> [!NOTE]
> Nell'ambito della versione iniziale dell'anteprima di questa funzionalità a ottobre 2016, i backup sono stati archiviati nell'insieme di credenziali di Servizi di ripristino dei servizi di Azure. Questo aggiornamento rimuove tale dipendenza, ma per la compatibilità con le versioni precedenti l'API originale è supportata fino al 31 maggio 2018. Se è necessario interagire con i backup dell'insieme di credenziali di ripristino dei servizi di Azure, vedere [Long-term backup retention using Azure Services Recovery Service vault](sql-database-long-term-backup-retention-configure-vault.md) (Conservazione a lungo termine dei backup con l'insieme di credenziali di Servizi di ripristino dei servizi di Azure). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Usare il portale di Azure per configurare i criteri di conservazione a lungo termine e ripristinare i backup

Le sezioni seguenti mostrano come usare il portale di Azure per configurare il periodo di conservazione a lungo termine, visualizzare i backup con conservazione a lungo termine e ripristinare il backup dalla conservazione a lungo termine.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare il database SQL per [conservare i backup automatizzati](sql-database-long-term-retention.md) per un periodo più lungo rispetto al periodo di conservazione associato al livello di servizio. 

1. Nel portale di Azure selezionare il server SQL e quindi fare clic su **Conservazione backup a lungo termine**.

   ![collegamento conservazione backup a lungo termine](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Nella scheda **Configure policies** (Configura criteri) selezionare il database in cui si vuole impostare o modificare i criteri di conservazione a lungo termine dei backup.

   ![Selezionare il database](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. Nel riquadro **Configure policies** (Configura criteri) scegliere se si vuole conservare i backup per settimane, mesi o anni e specificare per ognuno il periodo di conservazione. 

   ![Configurare criteri](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. Al termine, fare clic su **Applica**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visualizzare i backup e ripristinare da un backup usando il portale di Azure

Visualizzare i backup conservati per un database specifico con i criteri di conservazione a lungo termine ed eseguire il ripristino da tali backup. 

1. Nel portale di Azure selezionare il server SQL e quindi fare clic su **Conservazione backup a lungo termine**.

   ![collegamento conservazione backup a lungo termine](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Nella scheda **Available backups** (Backup disponibili) selezionare il database per cui si vuole visualizzare i backup disponibili.

   ![Selezionare il database](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Nel riquadro **Available backups** (Backup disponibili) esaminare i backup disponibili. 

   ![Visualizzare i backup](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Selezionare il backup da cui si vuole eseguire il ripristino e quindi specificare il nome del nuovo database.

   ![ripristinare](./media/sql-database-long-term-retention/ltr-restore.png)

5. Fare clic su **OK** per ripristinare nel nuovo database il database dal backup presente nella risorsa di archiviazione di Azure SQL.

6. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento del processo di ripristino dall'insieme di credenziali](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Al termine del processo di ripristino, aprire la pagina **Database SQL** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Usare PowerShell per configurare i criteri di conservazione a lungo termine e ripristinare i backup

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione a lungo termine dei backup, visualizzare i backup nella risorsa di archiviazione di Azure SQL ed eseguire il ripristino da un backup nella risorsa di archiviazione di Azure SQL.

### <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visualizzare i criteri di conservazione a lungo termine
Questo esempio illustra come elencare i criteri di conservazione a lungo termine in un server

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Cancellare i criteri di conservazione a lungo termine
Questo esempio illustra come cancellare i criteri di conservazione a lungo termine da un database

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visualizzare i backup con conservazione a lungo termine

Questo esempio illustra come elencare i backup con conservazione a lungo termine in un server. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine
Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro dell'ID risorsa richiede ora l'ID risorsa del backup con conservazione a lungo termine. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
