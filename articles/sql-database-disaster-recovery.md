<properties 
   pageTitle="Ripristino di emergenza del database SQL" 
   description="Informazioni su come ripristinare un database da un errore o da un'interruzione del servizio del data center dell'area con le funzionalità di replica geografica e di ripristino geografico del database SQL." 
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

# Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio

Il database SQL di Azure offre alcune funzionalità per il ripristino in seguito a un'interruzione del servizio:

- Replica geografica attiva [(blog)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Replica geografica standard [(blog)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Ripristino geografico [(blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

Per informazioni su come prepararsi per possibili emergenze e su quando ripristinare il database, vedere la pagina [Progettazione per la continuità aziendale](sql-database-business-continuity-design.md).

##Quando avviare il ripristino 

L'operazione di ripristino ha un impatto sull'applicazione. Richiede la modifica della stringa di connessione SQL e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Quando l'applicazione viene distribuita nell'ambiente di produzione, è consigliabile eseguire il monitoraggio regolare dell'integrità dell'applicazione e usare i punti dati seguenti per determinare se il ripristino è possibile:

1. Si è verificato un errore di connettività permanente dal livello applicazione al database.
2. Il portale di Azure visualizza un avviso relativo a un evento imprevisto con un vasto impatto nell'area.

## Failover al database secondario con replica geografica
> [AZURE.NOTE]Per avere un database secondario da usare per il failover è necessario configurare la [replica geografica standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) o la [replica geografica attiva](https://msdn.microsoft.com/library/azure/dn741339.aspx). La funzionalità di replica geografica è disponibile solo per i database Standard e Premium.

In caso di interruzione del servizio nel database primario, è possibile eseguire il failover a un database secondario per ripristinare la disponibilità. A tale scopo è necessario forzare la terminazione della relazione di copia continua. Per una descrizione completa della terminazione delle relazioni di copia continua, vedere [qui](https://msdn.microsoft.com/library/azure/dn741323.aspx).



###Portale di Azure
1. Accedere al [portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA** e quindi selezionare **Database SQL**.
3. Individuare e selezionare il database. 
4. Nella parte inferiore del pannello del database selezionare la **mappa della replica geografica**.
4. In **Secondari** fare clic con il pulsante destro del mouse sulla riga con il nome del database che si desidera ripristinare e scegliere **Arresta**.

Dopo aver terminato la relazione di copia continua, sarà possibile configurare il database ripristinato da usare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).
###PowerShell
Usare PowerShell per eseguire il ripristino del database a livello di codice.

Per terminare la relazione dal database secondario, usare il cmdlet [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223).
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
Dopo aver terminato la relazione di copia continua, sarà possibile configurare il database ripristinato da usare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).
###API REST 
Usare REST per eseguire il ripristino del database a livello di codice.

1. Ottenere la copia continua del database mediante l'operazione [Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx).
2. Arrestare la copia continua del database mediante l'operazione [Stop Database Copy](https://msdn.microsoft.com/library/azure/dn509573.aspx). Usare il nome del server secondario e il nome del database nell'URI della richiesta Stop Database Copy.

 Dopo aver terminato la relazione di copia continua, sarà possibile configurare il database ripristinato da usare seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).
## Ripristino con la funzionalità di ripristino geografico

In caso di interruzione del servizio di un database, è possibile ripristinare il database dalla più recente copia di backup con ridondanza geografica usando la funzionalità di ripristino geografico.

###Portale di Azure
1. Accedere al [portale di Azure](https://portal.Azure.com).
2. Sul lato sinistro della schermata fare clic su **NUOVO**, selezionare **Dati e archiviazione** e quindi **Database SQL**.
2. Selezionare **BACKUP** come origine e quindi selezionare la copia di backup con ridondanza geografica che si desidera ripristinare.
3. Specificare il resto delle proprietà del database e quindi fare clic su **Crea**.
4. Il processo di ripristino del database inizierà e potrà essere monitorato tramite **NOTIFICHE** sul lato sinistro della schermata.

Dopo aver completato il ripristino del database, sarà possibile configurare il database per l'uso seguendo la guida [Finalizzare un database ripristinato](articles/sql-database-recovered-finalize.md).
###PowerShell 
Usare PowerShell per eseguire il ripristino del database a livello di codice.

Per avviare una richiesta di ripristino geografico, usare il cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx). Per la descrizione dettagliata della procedura, vedere l'apposito [video](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

Dopo aver completato il ripristino del database, sarà possibile configurare il database per l'uso seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).
###API REST 

Usare REST per eseguire il ripristino del database a livello di codice.

1.	Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

Dopo aver completato il ripristino del database, sarà possibile configurare il database per l'uso seguendo la guida [Finalizzare un database ripristinato](sql-database-recovered-finalize.md).

<!---HONumber=58-->