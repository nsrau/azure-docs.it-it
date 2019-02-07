---
title: Gestire la conservazione dei backup a lungo termine del database SQL di Azure | Microsoft Docs
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
ms.date: 01/07/2019
ms.openlocfilehash: 8f88579a5fcdb96622a32c5b55bf8fee744d916e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567180"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gestire la conservazione a lungo termine dei backup del database SQL di Azure

Nel database SQL di Azure è possibile configurare un database singolo o in pool con criteri di [conservazione a lungo termine dei backup](sql-database-long-term-retention.md) per conservare automaticamente i backup in Archiviazione BLOB di Azure fino a 10 anni. È quindi possibile ripristinare un database usando questi backup tramite il portale di Azure o PowerShell.

> [!IMPORTANT]
> [Istanza gestita di database SQL di Azure](sql-database-managed-instance.md) non supporta attualmente la conservazione a lungo termine dei backup.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Usare il portale di Azure per configurare i criteri di conservazione a lungo termine e ripristinare i backup

Le sezioni seguenti mostrano come usare il portale di Azure per configurare il periodo di conservazione a lungo termine, visualizzare i backup con conservazione a lungo termine e ripristinare il backup dalla conservazione a lungo termine.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare il database SQL per [conservare i backup automatizzati](sql-database-long-term-retention.md) per un periodo più lungo rispetto al periodo di conservazione associato al livello di servizio. 

1. Nel portale di Azure selezionare l'istanza di SQL Server e quindi fare clic su **Gestisci backup**. Nella scheda **Configura criteri** *selezionare la casella di controllo relativa al database in cui si vogliono impostare o modificare i criteri di conservazione a lungo termine dei backup*. Se la casella di controllo accanto al database non è selezionata, le modifiche dei criteri non verranno applicate al database.  

   ![collegamento di gestione backup](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Nel riquadro **Configure policies** (Configura criteri) scegliere se si vuole conservare i backup per settimane, mesi o anni e specificare per ognuno il periodo di conservazione. 

   ![Configurare criteri](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Al termine, fare clic su **Applica**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visualizzare i backup e ripristinare da un backup usando il portale di Azure

Visualizzare i backup conservati per un database specifico con i criteri di conservazione a lungo termine ed eseguire il ripristino da tali backup. 

1. Nel portale di Azure selezionare l'istanza di SQL Server e quindi fare clic su **Gestisci backup**. Nella scheda **Available backups** (Backup disponibili) selezionare il database per cui si vuole visualizzare i backup disponibili.

   ![Selezionare il database](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Nel riquadro **Available backups** (Backup disponibili) esaminare i backup disponibili. 

   ![Visualizzare i backup](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Selezionare il backup da cui si vuole eseguire il ripristino e quindi specificare il nome del nuovo database.

   ![ripristinare](./media/sql-database-long-term-retention/ltr-restore.png)

5. Fare clic su **OK** per ripristinare nel nuovo database il database dal backup presente nella risorsa di archiviazione di Azure SQL.

6. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento processo di ripristino](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Al termine del processo di ripristino, aprire la pagina **Database SQL** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Usare PowerShell per configurare i criteri di conservazione a lungo termine e ripristinare i backup

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione a lungo termine dei backup, visualizzare i backup nella risorsa di archiviazione di Azure SQL ed eseguire il ripristino da un backup nella risorsa di archiviazione di Azure SQL.

> [!IMPORTANT]
> L'API di conservazione a lungo termine V2 è supportata nelle versioni di PowerShell seguenti:
- [AzureRM.Sql-4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) o successiva
- [AzureRM-6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) o successiva
> 

### <a name="rbac-roles-to-manage-long-term-retention"></a>Ruoli Controllo degli accessi in base al ruolo per gestire la conservazione a lungo termine

Per gestire i backup di conservazione a lungo termine, è necessario avere uno seguenti ruoli: 
- Proprietario della sottoscrizione
- Collaboratore SQL Server nell'ambito **Sottoscrizione**
- Collaboratore database SQL nell'ambito **Sottoscrizione**

Se è necessario un controllo più granulare, è possibile creare ruoli Controllo degli accessi in base al ruolo personalizzati e assegnarli nell'ambito **Sottoscrizione**. 

Per **Get-AzureRmSqlDatabaseLongTermRetentionBackup** e **Restore-AzureRmSqlDatabase** il ruolo deve avere le autorizzazioni seguenti:

Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read
 
Per **Remove-AzureRmSqlDatabaseLongTermRetentionBackup** il ruolo deve avere le autorizzazioni seguenti:

Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


### <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
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
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. È possibile impostare le notifiche su ogni eliminazione in Monitoraggio di Azure filtrando in base all'operazione di eliminazione di un backup con conservazione a lungo termine. Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../azure-monitor/platform/alerts-activity-log.md).
>

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
