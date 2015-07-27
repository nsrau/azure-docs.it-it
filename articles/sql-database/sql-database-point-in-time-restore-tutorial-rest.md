<properties 
   pageTitle="Ripristinare un database SQL di Azure mediante il ripristino temporizzato con l'API REST" 
   description="Ripristino temporizzato, database SQL di Microsoft Azure, ripristino del database, recupero del database, API REST" 
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

# Ripristinare un database SQL di Azure mediante il ripristino temporizzato con l'API REST

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## Panoramica

In questa guida viene illustrato come ripristinare un database SQL di Azure mediante il ripristino temporizzato con l'API REST. Vengono forniti collegamenti a operazioni più dettagliate.

Con il ripristino temporizzato viene creato un nuovo database. Il servizio seleziona automaticamente il livello di servizio in base al backup utilizzato nel punto di ripristino. Accertarsi di disporre di una quota sul server logico per creare un altro database. Se si desidera richiedere un aumento della quota, contattare il [supporto Azure](http://azure.microsoft.com/support/options/).

## Restrizioni e sicurezza

Vedere [Ripristinare un database SQL di Azure mediante il ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Procedura: ripristinare un database SQL di Azure mediante l'API REST

1.	Selezionare il database da ripristinare mediante l'operazione [Get Database](http://msdn.microsoft.com/library/azure/dn505708.aspx).

2.	Creare la richiesta di ripristino tramite l'operazione [Create Database Restore Request](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Monitorare la richiesta di ripristino tramite l'operazione [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx).

## Passaggi successivi

Per altre informazioni, vedere quanto segue:

[Continuità aziendale del database SQL di Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Backup e ripristino del database SQL di Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ripristino temporizzato del database SQL di Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Riferimento dell'API REST di gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=July15_HO3-->