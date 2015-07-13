<properties 
   pageTitle="Ripristinare un database SQL di Azure mediante Ripristino geografico con l'API REST" 
   description="Ripristino geografico, Database SQL di Microsoft Azure, ripristinare il database, recuperare il database, API REST" 
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

# Ripristinare un database SQL di Azure mediante Ripristino geografico con l'API REST

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)

## Panoramica

In questa guida viene illustrato come ripristinare un database SQL di Azure mediante l'API REST. Vengono forniti collegamenti a operazioni più dettagliate. Ripristino geografico è la protezione da ripristino di emergenza inclusa per tutti i livelli di servizio Basic, Standard e Premium dei database SQL di Azure.

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure mediante Ripristino geografico nel portale di Azure](sql-database-geo-restore-tutorial-management-portal.md).

## procedura: recuperare un database SQL di Azure mediante l'API REST

1.	Ottenere l'elenco dei database recuperabili mediante l'operazione [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx).
	
2.	Ottenere il database che si desidera ripristinare mediante l'operazione [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx).
	
3.	Creare la richiesta di ripristino mediante l'operazione [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Tenere traccia dello stato di ripristino mediante l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Ripristinare un database SQL di Azure mediante il ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Ripristinare un database SQL di Azure nel portale di Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ripristino geografico del database SQL di Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Riferimento dell'API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=62-->