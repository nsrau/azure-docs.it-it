<properties
   pageTitle="Ripristinare un database dall’errore dell’utente in SQL Data Warehouse | Microsoft Azure"
   description="Procedura per ripristinare un database dall’errore dell’utente in SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Ripristinare un database dall’errore dell’utente in SQL Data Warehouse

SQL Data Warehouse offre due funzionalità principali per il ripristino dall’errore dell'utente che causa l’eliminazione o il danneggiamento dei dati non intenzionale:

- Ripristino di un database attivo
- Ripristino di un database eliminato

Entrambe queste funzionalità eseguono il ripristino in un nuovo database nello stesso server.

Il ripristino di un database SQL Data Warehouse è supportato da due API differenti: Azure PowerShell e API REST. È possibile usare indifferentemente l'una o l'altra per accedere alla funzionalità di ripristino di SQL Data Warehouse.

## Ripristinare un database attivo
Se l’errore dell'utente causa la modifica dei dati non intenzionale, è possibile ripristinare il database a uno dei punti di ripristino entro il periodo di conservazione. Gli snapshot del database per un database attivo si verificano almeno ogni 8 ore e vengono conservati per 7 giorni.

### PowerShell

Utilizzare PowerShell di Azure per eseguire un ripristino del database a livello di codice. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). È possibile controllare la versione in uso eseguendo Get-Module -ListAvailable -Name Azure. Questo articolo si basa su Microsoft Azure PowerShell versione 1.0.4.

Per ripristinare un database, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Elencare i punti di ripristino per il database (richiede la modalità Gestione risorse di Azure)
5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.
6. Ripristinare il database al punto di ripristino desiderato.
7. Monitorare lo stato del ripristino.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Se il server è foo.database.windows.net, utilizzare "foo" come NomeServer nei cmdlet sopraindicati di powershell.

### API REST
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato][].

## Ripristinare un database eliminato
Se si elimina un database, è possibile ripristinare il database eliminato al momento dell'eliminazione. SQL Data Warehouse di Azure esegue uno snapshot del database prima dell’eliminazione e la mantiene per 7 giorni.

### PowerShell
Utilizzare Azure PowerShell per eseguire un ripristino del database eliminato a livello di programmazione. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).

Per ripristinare un database eliminato, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore][].

1. Aprire Microsoft Azure PowerShell
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database eliminato da ripristinare.
4. Cercare il database e la relativa data di eliminazione nell'elenco dei database eliminati.

```Powershell
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Selezionare il database eliminato e avviare il ripristino.

```Powershell
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Se il server è foo.database.windows.net, utilizzare "foo" come NomeServer nei cmdlet sopraindicati di powershell.

### API REST
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1.	Elencare tutti i database eliminati ripristinabili mediante l'operazione [List Restorable Dropped Databases][].
2.	Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database][].
3.	Iniziare il ripristino utilizzando l'operazione [Create database restore request][].
4.	Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato][].


## Passaggi successivi
Per ulteriori informazioni sulle funzionalità di continuità aziendale di altre versioni del database SQL di Azure, leggere la [Panoramica sulla continuità aziendale per database SQL Azure][].


<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale per database SQL Azure]: sql-database/sql-database-business-continuity.md
[Finalizzare un database ripristinato]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->