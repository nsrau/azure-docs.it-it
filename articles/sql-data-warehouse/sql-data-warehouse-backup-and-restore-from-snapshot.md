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
   ms.date="03/28/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Ripristinare un database dall’errore dell’utente in SQL Data Warehouse

SQL Data Warehouse offre due funzionalità principali per il ripristino dall’errore dell'utente che causa l’eliminazione o il danneggiamento dei dati non intenzionale:

- Ripristino di un database attivo
- Ripristino di un database eliminato

Entrambe queste funzionalità eseguono il ripristino in un nuovo database nello stesso server. È importante assicurarsi che il server in cui si esegue il ripristino abbia una capacità sufficiente DTU per il nuovo database. È possibile richiedere un aumento della quota da [contattare il supporto][].


## Ripristinare un database attivo
Il servizio Azure SQL Data Warehouse protegge tutti i database dinamici eseguendo snapshot almeno ogni 8 ore e conservandoli per 7 giorni in modo da offrire un set discreto di punti di ripristino. Gli snapshot del database vengono creati e conservati per 7 giorni anche quando il database viene sospeso o rimosso. Se l’errore dell'utente causa la modifica dei dati non intenzionale, è possibile ripristinare il database a uno dei punti di ripristino entro il periodo di conservazione.


### Portale di Azure

Per eseguire il ripristino usando il portale di Azure, seguire questa procedura.

1. Accedere al [Portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Database SQL**.
3. Individuare e selezionare il database.
4. Nella parte superiore del pannello database fare clic su **Ripristina**.
5. Specificare un nuovo **Nome database**, quindi selezionare un **Punto di ripristino** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.


### PowerShell

Usare Azure PowerShell per eseguire un ripristino del database a livello di codice. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). È possibile controllare la versione in uso eseguendo Get-Module -ListAvailable -Name AzureRM.Sql. Questo articolo si basa sulla versione 1.0.5 del modulo AzureRM.Sql di Microsoft Azure PowerShell.

Per ripristinare un database, usare il cmdlet [Restore-AzureRmSqlDatabase][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Specificare i punti di ripristino per il database.
5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.
6. Ripristinare il database al punto di ripristino desiderato.
7. Verificare che il database ripristinato sia online.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>").RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" 

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Per il server foo.database.windows.net, usare "foo" come -ServerName nei cmdlet di PowerShell sopraindicati.


### API REST
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere l'elenco dei punti di ripristino del database utilizzando l'operazione Get Database Restore Points.
2. Iniziare il ripristino utilizzando l'operazione [Create database restore request][]
3. Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].


>[AZURE.NOTE] Dopo aver completato il ripristino, sarà possibile configurare il database seguendo la guida [Finalizzare un database ripristinato][].


## Ripristinare un database eliminato
Prima dell'eliminazione del database, Azure SQL Data Warehouse esegue uno snapshot del database e lo conserva per 7 giorni. Se si elimina un database per errore, è possibile ripristinarlo alle condizioni presenti al momento dell'eliminazione.


### Portale di Azure

Per ripristinare un database eliminato tramite il portale di Azure, seguire questa procedura.

1. Accedere al [Portale di Azure][].
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Server SQL**.
3. Individuare il server e selezionarlo.
4. Scorrere verso il basso fino a Operazioni nel pannello del server e fare clic sul riquadro **Database eliminati**.
5. Fare clic sul database eliminato che si desidera ripristinare.
5. Specificare un nuovo **Nome database** e fare clic su **Crea**.
6. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**.


### PowerShell
Utilizzare Azure PowerShell per eseguire un ripristino del database eliminato a livello di programmazione. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). È possibile controllare la versione in uso eseguendo Get-Module -ListAvailable -Name AzureRM.Sql. Questo articolo si basa sulla versione 1.0.5 del modulo AzureRM.Sql di Microsoft Azure PowerShell.

Per ripristinare un database eliminato, usare il cmdlet [Restore-AzureRmSqlDatabase][].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database eliminato da ripristinare.
4. Specificare il database eliminato.
5. Ripristinare il database eliminato.
6. Verificare che il database ripristinato sia online.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Per il server foo.database.windows.net, usare "foo" come -ServerName nei cmdlet di PowerShell sopraindicati.


### API REST
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1.	Elencare tutti i database eliminati ripristinabili mediante l'operazione [List Restorable Dropped Databases][].
2.	Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database][].
3.	Iniziare il ripristino utilizzando l'operazione [Create database restore request][].
4.	Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status][].


>[AZURE.NOTE] Dopo aver completato il ripristino, sarà possibile configurare il database seguendo la guida [Finalizzare un database ripristinato][].


## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale di altre versioni del database SQL di Azure, leggere la [Panoramica sulla continuità aziendale per database SQL di Azure][].


<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale per database SQL di Azure]: sql-database/sql-database-business-continuity.md
[Finalizzare un database ripristinato]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Create database restore request]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Portale di Azure]: https://portal.azure.com/
[contattare il supporto]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0406_2016-->