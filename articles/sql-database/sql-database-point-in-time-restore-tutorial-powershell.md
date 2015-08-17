<properties 
   pageTitle="Ripristinare un database SQL di Azure mediante il ripristino temporizzato in Azure PowerShell" 
   description="Ripristino temporizzato, Database SQL di Microsoft Azure, ripristino del database, recupero del database, Azure PowerShell" 
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
   ms.date="07/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Ripristinare un database SQL di Azure mediante il ripristino temporizzato in Azure PowerShell

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## Panoramica

In questa esercitazione viene illustrato come ripristinare un database SQL di Azure mediante il ripristino temporizzato in [Azure PowerShell](../powershell-install-configure.md). Il database SQL di Azure dispone di backup incorporati per il supporto del ripristino temporizzato automatico per i livelli di servizio Basic, Standard e Premium.

Con il ripristino temporizzato viene creato un nuovo database. Il servizio seleziona automaticamente il livello di servizio in base al backup utilizzato nel punto di ripristino. Accertarsi di disporre di una quota sul server logico per creare un altro database. Se si desidera richiedere un aumento della quota, contattare il [supporto Azure](http://azure.microsoft.com/support/options/).

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure mediante il ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Procedura: ripristinare un database SQL di Azure mediante il ripristino temporizzato in Azure PowerShell

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell]

Per eseguire i cmdlet seguenti, è necessario utilizzare l'autenticazione basata su certificati. Per altre informazioni, vedere la sezione *Utilizzare il metodo certificato* in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

1. Ottenere il database che si desidera ripristinare tramite il cmdlet [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx). Specificare i seguenti parametri:
	* **ServerName** dove si trova il database.
	* **DatabaseName** del database che si desidera ripristinare.	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" –DatabaseName “mydb”`

2. Avviare il ripristino utilizzando il cmdlet [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Specificare i seguenti parametri:
	* **SourceDatabase** da cui si desidera effettuare il ripristino.
	* **TargetDatabaseName** del database in cui si effettua il ripristino.
	* **PointInTime** che si desidera ripristinare.

	Archiviare il risultato restituito in una variabile denominata **$RestoreRequest**. La variabile contiene l'ID della richiesta di ripristino utilizzato per il monitoraggio dello stato di un ripristino.

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “myrestoredDB” –PointInTime “2015-01-01 06:00:00”`

Il completamento di un ripristino può richiedere del tempo. Per monitorare lo stato del ripristino, utilizzare il cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) e specificare i seguenti parametri:

* **ServerName** del database in cui si sta effettuando il ripristino.
* **OperationGuid**, vale a dire l'ID richiesta di ripristino archiviato nella variabile **$RestoreRequest** nel passaggio 2.

	`PS C:\>Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

Lo stato del ripristino viene mostrato nei campi **State** e **PercentComplete**.

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ripristino temporizzato del database SQL di Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=August15_HO6-->