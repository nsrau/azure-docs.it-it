---
title: 'Istanza gestita: conservazione dei backup a lungo termine (PowerShell)Managed instance: Long-term backup retention (PowerShell)'
description: Informazioni su come archiviare e ripristinare i backup automatici in contenitori di archiviazione BLOB di Azure separati per un'istanza gestita del database SQL di Azure tramite PowerShell.Learn how to store and restore automated backups on separate Azure Blob storage containers for an Azure SQL Database managed instance using PowerShell.
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
ms.date: 04/14/2020
ms.openlocfilehash: c9edbbf54696a817d0495f6890e0d796e482231f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393725"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Gestire la conservazione dei backup a lungo termine dell'istanza gestita del database SQL di Azure (PowerShell)Manage Azure SQL Database managed instance long-term backup retention (PowerShell)

Nell'istanza gestita del database SQL di Azure è possibile configurare criteri di conservazione dei [backup a lungo termine](sql-database-long-term-retention.md#managed-instance-support) come funzionalità di anteprima pubblica limitata. In questo modo è possibile conservare automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. È quindi possibile ripristinare un database usando questi backup con PowerShell.You can then recover a database using these backups with PowerShell.

   > [!IMPORTANT]
   > LTR per le istanze gestite è attualmente in anteprima limitata e disponibile per le sottoscrizioni EA e CSP caso per caso. Per richiedere la registrazione, creare un ticket di supporto di [Azure](https://azure.microsoft.com/support/create-ticket/) nell'argomento di supporto **Backup, ripristino e conservazione**backup a lungo termine. 


Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione a lungo termine dei backup, visualizzare i backup nella risorsa di archiviazione di Azure SQL ed eseguire il ripristino da un backup nella risorsa di archiviazione di Azure SQL.

## <a name="rbac-roles-to-manage-long-term-retention"></a>Ruoli Controllo degli accessi in base al ruolo per gestire la conservazione a lungo termine

Per **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** e **Restore-AzSqlInstanceDatabase**, è necessario disporre di uno dei ruoli seguenti:

- Ruolo Proprietario sottoscrizione o
- Ruolo Collaboratore istanza gestita o
- Ruolo personalizzato con le autorizzazioni seguenti:Custom role with the following permissions:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read``` ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read```
   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read```

Per **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**, è necessario disporre di uno dei ruoli seguenti:

- Ruolo Proprietario sottoscrizione o
- Ruolo personalizzato con l'autorizzazione seguente:Custom role with the following permission:

   ```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

> [!NOTE]
> Il ruolo Collaboratore istanza gestita non dispone dell'autorizzazione per eliminare i backup LTR.

Le autorizzazioni RBAC possono essere concesse nell'ambito della *sottoscrizione* o del *gruppo di risorse.* Tuttavia, per accedere ai backup LTR che appartengono a un'istanza eliminata, l'autorizzazione deve essere concessa nell'ambito di *sottoscrizione* di tale istanza.

```Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete```

## <a name="create-an-ltr-policy"></a>Creare i criteri di conservazione a lungo termine

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16

```

## <a name="view-ltr-policies"></a>Visualizzare i criteri di conservazione a lungo termine

In questo esempio viene illustrato come elencare i criteri LTR all'interno di un'istanza

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup

```

## <a name="clear-an-ltr-policy"></a>Cancellare i criteri di conservazione a lungo termine

Questo esempio illustra come cancellare i criteri di conservazione a lungo termine da un database

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>Visualizzare i backup con conservazione a lungo termine

In questo esempio viene illustrato come elencare i backup LTR all'interno di un'istanza.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase

```

## <a name="delete-ltr-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo esempio illustra come eliminare un backup con conservazione a lungo termine dall'elenco di backup.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. Per eliminare un backup LTR dopo l'eliminazione dell'istanza, è necessario disporre dell'autorizzazione ambito sottoscrizione. È possibile impostare notifiche su ogni eliminazione in Monitoraggio di Azure filtrando l'operazione 'Elimina un backup di conservazione a lungo termine'. Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../azure-monitor/platform/alerts-activity-log.md).

## <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine

Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro dell'ID risorsa richiede ora l'ID risorsa del backup con conservazione a lungo termine.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName

```

> [!IMPORTANT]
> Per eseguire il ripristino da un backup LTR dopo l'eliminazione dell'istanza, è necessario disporre delle autorizzazioni nell'ambito della sottoscrizione dell'istanza e tale sottoscrizione deve essere attiva. È inoltre necessario omettere il parametro facoltativo -ResourceGroupName.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
