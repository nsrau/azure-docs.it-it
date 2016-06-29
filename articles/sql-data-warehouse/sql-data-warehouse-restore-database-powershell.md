<properties
   pageTitle="Ripristinare un'istanza di Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Attività di PowerShell per il ripristino di un'istanza di Azure SQL Data Warehouse."
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
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Ripristinare un'istanza di Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Panoramica][]
- [Portale][]
- [PowerShell][]
- [REST][]

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse usando PowerShell.

## Prima di iniziare

### Verificare la capacità DTU del database SQL. 

Ogni istanza di SQL Data Warehouse è ospitata da un server logico SQL, il cui limite di capacità è misurato in DTU. Prima di ripristinare un'istanza di SQL Data Warehouse, è importante assicurarsi che il server logico SQL che ospita il database abbia una capacità in DTU sufficiente per il database da ripristinare. Per altre informazioni su [come visualizzare e aumentare la quota DTU][], vedere il relativo post di blog.

### Installare PowerShell

Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0 o successiva. È possibile controllare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente da [Installazione guidata piattaforma Web Microsoft][]. Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

## Ripristinare un database attivo o sospeso

Per ripristinare un database da uno snapshot, usare il cmdlet di PowerShell [Restore-AzureRmSqlDatabase][].

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

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato seguendo la guida sulla [finalizzazione di un database ripristinato][].

## Ripristino di un database eliminato

Per ripristinare un database eliminato, usare il cmdlet [Restore-AzureRmSqlDatabase][].

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

Al termine del ripristino sarà possibile configurare il database ripristinato seguendo la guida disponibile in [Finalizzare il database SQL di Azure ripristinato][].

## Eseguire il ripristino da un'area geografica di Azure

Per ripristinare un database, usare il cmdlet [Restore-AzureRmSqlDatabase][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status

```

### Configurare il database dopo l'esecuzione di un ripristino geografico

Si tratta di un elenco di controllo che può essere utilizzato per avere pronta la produzione di database ripristinati.

1. **Aggiornare le stringhe di connessione**: verificare che le stringhe di connessione degli strumenti client facciano riferimento al database appena ripristinato.
2. **Modificare le regole del firewall**: verificare le regole del firewall sul server di destinazione, quindi assicurarsi che le connessioni tra i computer client o Azure e il server e il database appena ripristinato siano abilitate.
3. **Verificare gli account di accesso al server e gli utenti del database**: controllare che tutti gli account di accesso usati dall'applicazione siano presenti sul server che ospita il database ripristinato. Ricreare gli account di accesso mancanti e concedere loro le autorizzazioni appropriate per il database ripristinato. 
4. **Abilitare il controllo**: se è necessario il controllo di accesso al database, attivarlo dopo il ripristino.

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.


## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, leggere la [panoramica sulla continuità aziendale per database SQL di Azure][].

<!--Image references-->

<!--Article references-->
[panoramica sulla continuità aziendale per database SQL di Azure]: sql-database-business-continuity.md
[Finalizzare il database SQL di Azure ripristinato]: sql-database-recovered-finalize.md
[finalizzazione di un database ripristinato]: sql-database-recovered-finalize.md
[Come installare e configurare Azure PowerShell]: powershell-install-configure.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Blog references-->
[come visualizzare e aumentare la quota DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps

<!---HONumber=AcomDC_0615_2016-->