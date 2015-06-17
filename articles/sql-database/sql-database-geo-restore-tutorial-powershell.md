<properties 
	pageTitle="Ripristinare un database SQL di Azure mediante Geo-Restore in Azure PowerShell" 
	description="Geo-Restore, Database SQL di Microsoft Azure, ripristinare un database, recuperare un database, Azure PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="elfisher" 
	manager="jeffreyg" 
	editor="v-romcal"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="storage-backup-recovery" 
	ms.date="02/24/2015"
	ms.author="elfish; v-romcal; v-stste"/>

# Ripristinare un database SQL di Azure mediante Geo-Restore in Azure PowerShell

> [AZURE.SELECTOR]
- [Geo-Restore - portale](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Geo-Restore - API REST](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/) 

## Panoramica

In questa esercitazione viene illustrato come ripristinare un database SQL di Azure mediante Geo-Restore in [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Geo-Restore è la protezione da ripristino di emergenza inclusa per tutti i livelli di servizio Basic, Standard e Premium dei database SQL di Azure.

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure mediante Geo-Restore nel portale di Azure](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Procedura: Ripristinare un database SQL di Azure con Geo-Restore in Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Per eseguire i cmdlet seguenti, è necessario utilizzare l'autenticazione basata su certificati. Per altre informazioni, vedere la sezione *Utilizzare il metodo certificato* in [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Ottenere l'elenco dei database recuperabili mediante il cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Specificare il seguente parametro:
	* **ServerName** dove si trova il database.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Scegliere il database che si desidera ripristinare mediante il cmdlet [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx). Specificare i seguenti parametri:
	* **ServerName** dove si trova il database.
	* **DatabaseName** del database da cui si esegue il ripristino.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" -DatabaseName "mydb"`
	 
3. Avviare il ripristino mediante il cmdlet [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Specificare i seguenti parametri:	
	* **SourceDatabase** che si desidera ripristinare.
	* **TargetDatabaseName** del database in cui si esegue il ripristino.
	* **TargetServerName** in cui di desidera ripristinare il database.

	Archiviare il risultato restituito in una variabile denominata **$RestoreRequest**. La variabile contiene l'ID della richiesta di ripristino utilizzato per il monitoraggio dello stato di un ripristino.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database -TargetDatabaseName "myrecoveredDB" -TargetServerName "mytargetserver"`
	
Il completamento di un ripristino di database può richiedere tempo. Per monitorare lo stato del ripristino, utilizzare il cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e specificare i seguenti parametri:

* **ServerName** del database in cui si sta effettuando il ripristino.
* **OperationGuid** vale a dire l'ID richiesta di ripristino archiviata nella variabile **$RecoveryRequest** nel passaggio 3.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "mytargetserver" -OperationGuid $RecoveryRequest.ID`

Lo stato del ripristino viene mostrato nei campi **State** e **PercentComplete**.

## Passaggi successivi

Per altre informazioni, vedere quanto segue: 

[Ripristinare un database SQL di Azure mediante il ripristino temporizzato nel portale di Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Ripristinare un database SQL di Azure eliminato nel portale di Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Geo-Restore del database SQL di Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
 