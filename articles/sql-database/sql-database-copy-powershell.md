<properties 
    pageTitle="Creare una copia di un database SQL di Azure tramite PowerShell | Microsoft Azure" 
    description="Creare una copia di un database SQL di Azure tramite PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Creare una copia di un database SQL di Azure tramite PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



La procedura seguente illustra come copiare un database SQL di Azure con PowerShell. L'operazione di copia del database consente di copiare un database SQL in un nuovo database usando il cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). La copia è un backup snapshot del database creato nello stesso server o in un server diverso.

> [AZURE.NOTE]Il database SQL di Azure crea automaticamente e conserva i backup per ogni database dell'utente che è possibile ripristinare. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

Al completamento del processo di copia, il nuovo database è completamente funzionante, indipendente dal database di origine. Al completamento della copia, il nuovo database è coerente da un punto di vista transazionale con il database di origine. Il livello di servizio e il livello di prestazioni (piano tariffario) della copia del database sono gli stessi del database di origine. Al termine del processo di copia, la copia diventa un database indipendente e completamente funzionante. Gli account di accesso, gli utenti e le autorizzazioni possono essere gestiti in modo indipendente.


Quando si copia un database nello stesso server logico, è possibile usare gli stessi account di accesso per entrambi i database. L'entità di sicurezza usata per copiare il database diventa il proprietario del database (DBO) nel nuovo database. Tutti gli utenti del database, le relative autorizzazioni e i relativi identificatori di sicurezza (SID) vengono copiati nella copia del database.


Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Azure PowerShell. Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).



## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure classico.

	Add-AzureAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**) È possibile copiare l'ID sottoscrizione dalle informazioni visualizzate nel passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di altre informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent:True**.


## Impostare le variabili per l'ambiente specifico

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e i server in uso.

Sostituire i valori segnaposto con i valori per l'ambiente in uso:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





## Copiare un database SQL nello stesso server

Questo comando invia la richiesta di copia del database al servizio. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

## Copiare un database SQL in un server diverso

Questo comando invia la richiesta di copia del database al servizio. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Monitorare lo stato dell'operazione di copia

Dopo aver eseguito **Start-AzureSqlDatabaseCopy**, è possibile controllare lo stato della richiesta di copia. L'esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce lo stato **In sospeso** o **In esecuzione**. È possibile eseguire il comando più volte finché non viene visualizzato lo stato **Completato** nell'output.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


## Copiare lo script di PowerShell del database SQL

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Passaggi successivi

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export-powershell.md)


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0121_2016-->