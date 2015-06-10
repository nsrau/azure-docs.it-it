<properties 
   pageTitle="Recupero degli errori del database SQL causati dall'utente" 
   description="Vengono fornite informazioni relative alla modalità di recupero da errori causati dall'utente, del recupero da un danneggiamento accidentale dei dati o dall'eliminazione di un database utilizzando la funzionalità di ripristino temporizzato (PITR) del database SQL di Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="elfish"/>

# Ripristino di un database SQL di Azure a seguito di un errore causato dall'utente

I database SQL di Azure offre due funzionalità principali per il ripristino a seguito di errori causati dagli utenti o a seguito di una modifica imprevista dei dati.

- Ripristino temporizzato 
- Ripristinare un database eliminato

Ulteriori informazioni su queste funzionalità sono disponibili in questo [post di blog](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

Il database SQL di Azure viene sempre ripristinato con un nuovo database. Le funzionalità di ripristino vengono offerte a tutti i clienti del database: Basic, Standard e Premium.
##Ripristino di tipo temporizzato
In caso di errore dell'utente o di una modifica imprevista dei dati, il ripristino temporizzato consente di ripristinare un qualsiasi momento del ciclo di vita del database compreso nel relativo periodo di conservazione.

I database Basic dispongono di 7 giorni di conservazione, i database Standard di 14 giorni e i database Premium di 35 giorni. Per ulteriori informazioni sulla conservazione dei database, leggere la [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

###Portale di Azure
1. Accedere al [Portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Database SQL**.
3. Individuare e selezionare il database.
4. Nella parte superiore del pannello del database, selezionare **Ripristina**.
5. Specificare un nome di database, il momento specifico, quindi fare clic su **Crea**.
6. Il processo di ripristino del database inizierà e potrà essere monitorato tramite le **NOTIFICHE** sul lato sinistro della schermata.

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).
###PowerShell
Utilizzare PowerShell per eseguire un ripristino del database a livello di codice.

Per ripristinare un database di tipo temporizzato, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Per un'analisi dettagliata, vedere il relativo [Video "procedura"](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

###API REST 
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1. Selezionare il database da ripristinare mediante l'operazione [Get Database](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Creare la richiesta di ripristino tramite l'operazione [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Monitorare la richiesta di ripristino tramite l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

##Ripristinare un database eliminato
Nel caso in cui venisse eliminato un database, il database SQL di Azure consente di ripristinare il database eliminato al momento dell'eliminazione. Il database SQL di Azure archivia il backup del database eliminato per il periodo di conservazione del database.

Il periodo di conservazione di un database eliminato è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso). Per ulteriori informazioni sulla conservazione dei database, leggere la [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

###Portale di Azure
1. Accedere al [Portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA**, quindi su **Server SQL**.
3. Individuare e selezionare il server.
4. Sotto la voce **Operazioni** nel pannello del server, selezionare **Database eliminati**.
5. Fare clic sul database eliminato che si desidera ripristinare.
6. Specificare un nome di database e fare clic su **Crea**.
7. Il processo di ripristino del database inizierà e potrà essere monitorato tramite le **NOTIFICHE** sul lato sinistro della schermata.

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

###PowerShell
Utilizzare PowerShell per eseguire un ripristino del database a livello di codice.

Per ripristinare un database eliminato, utilizzare il cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Per un'analisi dettagliata, vedere il relativo [Video "procedura"](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Cercare il database eliminato e la relativa data di eliminazione nell'elenco dei database eliminati.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Selezionare il database eliminato e avviare il ripristino.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

###API REST 
Utilizzare REST per eseguire il ripristino del database a livello di codice.

1.	Elencare tutti i database ripristinabili eliminati mediante l'operazione [List Restorable Dropped Databases](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Iniziare il ripristino utilizzando l'operazione [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx) 
	
4.	Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Dopo aver completato il ripristino, sarà possibile configurare il database ripristinato da utilizzare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

<!---HONumber=58-->