<properties 
    pageTitle="Copiare un database SQL di Azure con PowerShell | Microsoft Azure" 
    description="Creare una copia di un database SQL di Azure tramite PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copiare un database SQL di Azure con PowerShell


> [AZURE.SELECTOR]
- [Panoramica](sql-database-copy.md)
- [Portale di Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

La procedura seguente illustra come copiare un database SQL con PowerShell nello stesso server o in un server diverso. L'operazione di copia di database usa il cmdlet [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx).


Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.
- Database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Azure PowerShell. Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).



## Copiare il database SQL

Esistono alcune variabili in cui è necessario sostituire i valori di esempio con i valori specifici per il database e i server in uso. Sostituire i valori segnaposto con i valori per l'ambiente in uso:

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### Copiare un database SQL nello stesso server

Questo comando invia la richiesta di copia del database al servizio. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

### Copiare un database SQL in un server diverso

Questo comando invia la richiesta di copia del database al servizio. A seconda delle dimensioni del database, l'operazione di copia potrebbe richiedere alcuni minuti.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Monitorare lo stato dell'operazione di copia

Dopo aver eseguito **Start-AzureSqlDatabaseCopy**, è possibile controllare lo stato della richiesta di copia. L'esecuzione di tale comando immediatamente dopo la richiesta in genere restituisce lo stato **In sospeso** o **In esecuzione**. È possibile eseguire il comando più volte finché non viene visualizzato lo stato **Completato** nell'output.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## Risolvere gli account di accesso

Per risolvere gli account di accesso al termine dell'operazione di copia, vedere [Risolvere gli account di accesso](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Script di PowerShell di esempio

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

- Vedere [Copiare un database SQL di Azure](sql-database-copy.md) per una panoramica su come copiare un database SQL di Azure.
- Vedere [Copiare un database SQL di Azure tramite il portale di Azure](sql-database-copy-portal.md) per copiare un database tramite il portale di Azure.
- Vedere [Copiare un database SQL di Azure con Transact-SQL](sql-database-copy-transact-sql.md) per copiare un database usando Transact-SQL.
- Vedere [Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) per informazioni sulla gestione di utenti e account di accesso durante la copia di un database in un server logico diverso.


## Risorse aggiuntive

- [Gestire gli accessi](sql-database-manage-logins.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)
- [Esportare il database in un BACPAC](sql-database-export.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->