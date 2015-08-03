<properties 
   pageTitle="Ripristinare un database SQL di Azure con l'API REST" 
   description="Database SQL di Microsoft Azure SQL, ripristinare un database eliminato, recuperare un database eliminato, API REST" 
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
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Ripristinare un database SQL di Azure con l'API REST

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## Panoramica

In questa guida viene illustrato come ripristinare un database SQL di Azure eliminato con l'API REST. Vengono forniti collegamenti a operazioni più dettagliate.

Quando si ripristina un database SQL di Azure eliminato viene creato un nuovo database. Il servizio seleziona automaticamente il livello di servizio in base al backup utilizzato nel punto di ripristino. Accertarsi di disporre di una quota sul server logico per creare un altro database. Se si desidera richiedere un aumento della quota, contattare il [supporto Azure](http://azure.microsoft.com/support/options/).

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure eliminato nel portale di Azure](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Procedura: ripristinare un database SQL di Azure mediante l'API REST

1.	Elencare tutti i database ripristinabili eliminati mediante l'operazione [List Restorable Dropped Databases](http://msdn.microsoft.com/library/azure/dn509562.aspx).
	
2.	Ottenere i dettagli del database eliminato che si desidera ripristinare mediante l'operazione [Get Restorable Dropped Database](http://msdn.microsoft.com/library/azure/dn509574.aspx).

3.	Iniziare il ripristino utilizzando l'operazione [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx)
	
4.	Monitorare lo stato del ripristino mediante l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Riferimento dell'API REST di gestione dei servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=July15_HO4-->