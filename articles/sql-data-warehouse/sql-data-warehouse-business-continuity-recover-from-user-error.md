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
   ms.date="06/26/2015"
   ms.author="sahajs"/>

# Ripristinare un database dall’errore dell’utente in SQL Data Warehouse

SQL Data Warehouse offre due funzionalità principali per il ripristino dall’errore dell'utente che causa l’eliminazione o il danneggiamento dei dati non intenzionale:

- Ripristino di un database attivo
- Ripristino di un database eliminato

Entrambe queste funzionalità eseguono il ripristino in un nuovo database nello stesso server.

## Ripristinare un database attivo
Se l’errore dell'utente causa la modifica dei dati non intenzionale, è possibile ripristinare il database a uno dei punti di ripristino entro il periodo di conservazione. Gli snapshot del database per un database attivo si verificano ogni 8 ore e vengono conservati per 7 giorni.

### PowerShell

Utilizzare PowerShell per eseguire un ripristino del database a livello di codice. Per ripristinare un database, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore][].

1. Selezionare la sottoscrizione con l'account che contiene il database da ripristinare.
2. Elencare i punti di ripristino per il database (richiede la modalità Gestione risorse di Azure)
3. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.
3. Ripristinare il database al punto di ripristino desiderato.
4. Monitorare lo stato del ripristino.

```
Select-AzureSubscription -SubscriptionId <Subscription_GUID>

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### API REST
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato][].

## Ripristinare un database eliminato
Se si elimina un database, è possibile ripristinare il database eliminato al momento dell'eliminazione. SQL Data Warehouse di Azure esegue uno snapshot del database prima dell’eliminazione e la mantiene per 7 giorni.

### PowerShell
Utilizzare PowerShell per eseguire un ripristino del database eliminato a livello di programmazione. Per ripristinare un database eliminato, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore][].

1. Cercare il database eliminato e la relativa data di eliminazione nell'elenco dei database eliminati.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

2. Selezionare il database eliminato e avviare il ripristino.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

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
[Panoramica sulla continuità aziendale per database SQL Azure]: sql-database/sql-database-business-continuity/,d
[Finalizzare un database ripristinato]: sql-database/sql-database-recovered-finalize/

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/it-it/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=July15_HO1-->