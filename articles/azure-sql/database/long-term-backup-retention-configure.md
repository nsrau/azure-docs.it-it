---
title: 'Database SQL di Azure: gestire la conservazione dei backup a lungo termine'
description: Informazioni su come archiviare e ripristinare i backup automatici per il database SQL di Azure in archiviazione di Azure (per un massimo di 10 anni) usando il portale di Azure e PowerShell
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 04/14/2020
ms.openlocfilehash: 3cab3b262f9116903d0b423cd5e4a0ebd03c46fa
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94984431"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Gestire la conservazione a lungo termine dei backup del database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Nel database SQL di Azure è possibile configurare un database con criteri di [conservazione dei backup a lungo termine](long-term-retention-overview.md) per mantenere automaticamente i backup del database in contenitori di archiviazione BLOB di Azure separati per un massimo di 10 anni. È quindi possibile ripristinare un database usando questi backup tramite il portale di Azure o PowerShell. È possibile configurare la conservazione a lungo termine anche per un [istanza gestita SQL di Azure](../managed-instance/long-term-backup-retention-configure.md) , ma è attualmente disponibile in anteprima pubblica limitata.

## <a name="using-the-azure-portal"></a>Uso del portale di Azure

Le sezioni seguenti mostrano come usare il portale di Azure per configurare il periodo di conservazione a lungo termine, visualizzare i backup con conservazione a lungo termine e ripristinare il backup dalla conservazione a lungo termine.

### <a name="configure-long-term-retention-policies"></a>Configurare i criteri di conservazione a lungo termine

È possibile configurare il database SQL per [conservare i backup automatizzati](long-term-retention-overview.md) per un periodo più lungo rispetto al periodo di conservazione associato al livello di servizio.

1. Nella portale di Azure selezionare l'istanza di SQL Server e quindi fare clic su **Gestisci backup**. Nella scheda **Configura criteri** selezionare la casella di controllo relativa al database in cui si vogliono impostare o modificare i criteri di conservazione a lungo termine dei backup. Se la casella di controllo accanto al database non è selezionata, le modifiche dei criteri non verranno applicate al database.  

   ![collegamento di gestione backup](./media/long-term-backup-retention-configure/ltr-configure-ltr.png)

2. Nel riquadro **Configure policies** (Configura criteri) scegliere se si vuole conservare i backup per settimane, mesi o anni e specificare per ognuno il periodo di conservazione.

   ![Configurare criteri](./media/long-term-backup-retention-configure/ltr-configure-policies.png)

3. Al termine, fare clic su **Applica**.

> [!IMPORTANT]
> Quando si Abilita un criterio di conservazione dei backup a lungo termine, potrebbe essere necessario attendere fino a 7 giorni prima che il primo backup diventi visibile e disponibile per il ripristino. Per informazioni dettagliate sul backup Cadance di LTR, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md).

### <a name="view-backups-and-restore-from-a-backup"></a>Visualizzare i backup e il ripristino da un backup

Visualizzare i backup conservati per un database specifico con un criterio LTR e ripristinare da tali backup.

1. Nella portale di Azure selezionare il server e quindi fare clic su **Gestisci backup**. Nella scheda **Available backups** (Backup disponibili) selezionare il database per cui si vuole visualizzare i backup disponibili.

   ![Selezionare il database](./media/long-term-backup-retention-configure/ltr-available-backups-select-database.png)

1. Nel riquadro **Available backups** (Backup disponibili) esaminare i backup disponibili.

   ![Visualizzare i backup](./media/long-term-backup-retention-configure/ltr-available-backups.png)

1. Selezionare il backup da cui si vuole eseguire il ripristino e quindi specificare il nome del nuovo database.

   ![ripristinare](./media/long-term-backup-retention-configure/ltr-restore.png)

1. Fare clic su **OK** per ripristinare il database dal backup in archiviazione di Azure al nuovo database.

1. Sulla barra degli strumenti fare clic sull'icona di notifica per visualizzare lo stato del processo di ripristino.

   ![avanzamento processo di ripristino](./media/long-term-backup-retention-configure/restore-job-progress-long-term.png)

1. Al termine del processo di ripristino, aprire la pagina **Database SQL** per visualizzare il database appena ripristinato.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per [estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente](recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>Uso di PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Le sezioni seguenti illustrano come usare PowerShell per configurare la conservazione dei backup a lungo termine, visualizzare i backup in archiviazione di Azure e ripristinare da un backup in archiviazione di Azure.

### <a name="azure-roles-to-manage-long-term-retention"></a>Ruoli di Azure per gestire la conservazione a lungo termine

Per **Get-AzSqlDatabaseLongTermRetentionBackup** e **Restore-AzSqlDatabase** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- SQL Server ruolo Collaboratore o
- Ruolo personalizzato con le autorizzazioni seguenti:

   Microsoft. SQL/locations/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/Read

Per **Remove-AzSqlDatabaseLongTermRetentionBackup** è necessario disporre di uno dei ruoli seguenti:

- Ruolo proprietario sottoscrizione o
- Ruolo personalizzato con l'autorizzazione seguente:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Il ruolo Collaboratore SQL Server non dispone dell'autorizzazione per eliminare i backup di LTR.

È possibile concedere le autorizzazioni RBAC di Azure nell'ambito di una *sottoscrizione* o di un *gruppo di risorse* . Tuttavia, per accedere ai backup di LTR che appartengono a un server eliminato, l'autorizzazione deve essere concessa nell'ambito della *sottoscrizione* di tale server.

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
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $serverName | `
    Get-AzSqlDatabaseLongTermRetentionPolicy

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup
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
> L'eliminazione di un backup con conservazione a lungo termine non è reversibile. Per eliminare un backup di LTR dopo che il server è stato eliminato, è necessario disporre dell'autorizzazione per l'ambito della sottoscrizione. È possibile configurare le notifiche relative a ogni eliminazione in monitoraggio di Azure filtrando l'operazione "Elimina un backup di conservazione a lungo termine". Il log attività contiene informazioni su chi ha effettuato la richiesta e sul momento in cui è stata effettuata. Per istruzioni dettagliate, vedere [Creare gli avvisi del log attività](../../azure-monitor/platform/alerts-activity-log.md).

### <a name="restore-from-ltr-backups"></a>Eseguire il ripristino dai backup con conservazione a lungo termine

Questo esempio illustra come eseguire il ripristino da un backup con conservazione a lungo termine. Si noti che questa interfaccia non è stata modificata, ma il parametro Resource ID richiede ora l'ID della risorsa di backup LTR.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Per eseguire il ripristino da un backup di LTR dopo che il server è stato eliminato, è necessario avere l'ambito delle autorizzazioni per la sottoscrizione del server e che la sottoscrizione sia attiva. È inoltre necessario omettere il parametro facoltativo-ResourceGroupName.

> [!NOTE]
> A questo punto è possibile connettersi al database ripristinato usando SQL Server Management Studio per eseguire le attività necessarie, ad esempio per estrarre un bit di dati dal database ripristinato da copiare nel database esistente o per eliminare il database esistente e rinominare il database ripristinato con il nome del database esistente. Vedere [ripristino temporizzato](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations"></a>Limitazioni
- Quando si esegue il ripristino da un backup di LTR, la proprietà scala lettura è disabilitata. Per abilitare, leggere la scalabilità nel database ripristinato, aggiornare il database dopo che è stato creato.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](automated-backups-overview.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](long-term-retention-overview.md)