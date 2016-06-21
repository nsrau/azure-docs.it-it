<properties
   pageTitle="Eseguire il ripristino di un database in Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Attività di PowerShell per il ripristino di un database attivo, eliminato o inaccessibile in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="elfish;barbkess;sonyama;kevin"/>

# Eseguire il backup e il ripristino di un database in Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-overview-manage-database-restore.md)
- [Portale](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST](sql-data-warehouse-manage-database-restore-rest-api.md)

Attività di PowerShell per il ripristino di un database in SQL Data Warehouse.

Attività contenute in questo argomento:

- Ripristino di un database attivo
- Ripristino di un database eliminato

[AZURE.INCLUDE [Criteri di conservazione dei backup di SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Prima di iniziare

### Verificare la capacità DTU del database SQL. 
SQL Data Warehouse si ripristina su un nuovo database nel server logico di SQL. Per questo motivo, è importante verificare che il server SQL di cui si esegue il ripristino abbia una capacità DTU sufficiente per il nuovo database SQL Server. Vedere questo post di blog per altre informazioni su [come visualizzare e aumentare la quota DTU][].

### Installare PowerShell

Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0 o successiva. È possibile controllare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente utilizzando [Microsoft Web Platform Installer][]. Per altre informazioni sull’installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

## Ripristino di un database attivo

Per ripristinare un database da uno snapshot, utilizzare il cmdlet [Restore-AzureRmSqlDatabase][] di PowerShell.

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Specificare i punti di ripristino per il database.
5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.
6. Ripristinare il database al punto di ripristino desiderato.
7. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Per il server foo.database.windows.net, usare "foo" come -ServerName nei cmdlet di PowerShell sopraindicati.

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato seguendo la guida sulla [finalizzazione di un database ripristinato][].

## Ripristino di un database eliminato

Per ripristinare un database eliminato, utilizzare il cmdlet [Restore-AzureRmSqlDatabase][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database eliminato da ripristinare.
4. Specificare il database eliminato.
5. Ripristinare il database eliminato.
6. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Per il server foo.database.windows.net, usare "foo" come -ServerName nei cmdlet di PowerShell sopraindicati.

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato seguendo la guida sulla [finalizzazione di un database ripristinato][].

## Passaggi successivi
Per altre informazioni, vedere [Continuità aziendale del database SQL di Azure][] e [Panoramica sulla gestione][].

<!--Image references-->

<!--Article references-->
[Continuità aziendale del database SQL di Azure]: sql-database-business-continuity.md
[finalizzazione di un database ripristinato]: sql-database-recovered-finalize.md
[Come installare e configurare Azure PowerShell]: powershell-install-configure.md
[Panoramica sulla gestione]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0608_2016-->