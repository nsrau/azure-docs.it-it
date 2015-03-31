<properties 
 pageTitle="Ripristinare un database SQL di Azure in Azure PowerShell" 
 description="Database SQL di Microsoft Azure, ripristinare un database eliminato, recuperare un database eliminato, Azure PowerShell" 
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

# Ripristinare un database SQL di Azure in Azure PowerShell

> [AZURE.SELECTOR]
- [Ripristinare un database eliminato - portale](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [Ripristinare un database eliminato - API REST](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## Panoramica

In questa esercitazione viene illustrato come ripristinare un database SQL di Azure eliminato in [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). È possibile ripristinare un database che era stato eliminato durante il periodo di conservazione al punto in cui è stato eliminato. Il periodo di conservazione viene determinato dal livello di servizio del database.

Quando si ripristina un database SQL di Azure eliminato viene creato un nuovo database. Il servizio seleziona automaticamente il livello di servizio in base al backup utilizzato nel punto di ripristino. Accertarsi di disporre di una quota sul server logico per creare un altro database. Se si desidera richiedere un aumento della quota, contattare [il supporto Azure](http://azure.microsoft.com/support/options/).

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure eliminato nel portale di Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/).

## Procedura: Ripristinare un database SQL di Azure in Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Per eseguire i cmdlet seguenti, è necessario utilizzare l'autenticazione basata su certificati. Per altre informazioni, vedere la sezione *Utilizzare il metodo certificato* in [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Ottenere l'elenco dei database ripristinabile mediante il cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx).
	* Utilizzare l'opzione **RestorableDropped** e specificare **ServerName** del server da cui è stato eliminato il database.
	* Se si esegue il comando indicato di seguito, i risultati vengono archiviati in una variabile denominata **$RecoverableDBs**.
	
	`PS C:\>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" -RestorableDropped`

2. Scegliere il database eliminato che si desidera ripristinare dall'elenco dei database eliminati.

	* Digitare il numero del database eliminato dall'elenco **$RecoverableDBs**. 

	`PS C:\>$Database = $RecoverableDBs[<deleted database number>]`

	* Per altre informazioni su come ottenere un database oggetto di database eliminato ripristinabile, vedere [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Avviare il ripristino utilizzando il cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Specificare i seguenti parametri:	
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName** del database in cui si effettua il ripristino.

	Archiviare il risultato restituito in una variabile denominata **$RestoreRequest**. La variabile contiene l'ID della richiesta di ripristino utilizzato per il monitoraggio dello stato di un ripristino.
	
	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database -TargetDatabaseName "myrestoredDB"`

Il completamento di un ripristino può richiedere del tempo. Per monitorare lo stato del ripristino, utilizzare il cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e specificare i seguenti parametri:

* **ServerName** del database in cui si sta effettuando il ripristino.
* **OperationGuid**, vale a dire l'ID richiesta di ripristino archiviato nella variabile **$RestoreRequest** nel passaggio 3.

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Lo stato del ripristino viene mostrato nei campi **State** e **PercentComplete**.

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)
<!--HONumber=47-->
