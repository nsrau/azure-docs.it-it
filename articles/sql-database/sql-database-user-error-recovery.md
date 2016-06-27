<properties 
   pageTitle="Recupero degli errori del database SQL causati dall'utente" 
   description="Vengono fornite informazioni relative alla modalità di recupero da errori causati dall'utente, del recupero da un danneggiamento accidentale dei dati o dall'eliminazione di un database utilizzando la funzionalità di ripristino temporizzato (PITR) del database SQL di Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="05/10/2016"
   ms.author="carlrab"/>

# Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente

I database SQL di Azure offre due funzionalità principali per il ripristino a seguito di errori causati dagli utenti o a seguito di una modifica imprevista dei dati.

- [Ripristino temporizzato](sql-database-point-in-time-restore.md) 
- [Ripristinare un database eliminato](sql-database-point-in-time-restore.md#restoring-a-recently-deleted-database)


Il database SQL di Azure viene sempre ripristinato con un nuovo database. Le funzionalità di ripristino vengono offerte a tutti i clienti del database: Basic, Standard e Premium.

##Ripristino temporizzato

In caso di errore dell'utente o di una modifica imprevista dei dati, il ripristino temporizzato consente di ripristinare un qualsiasi momento del ciclo di vita del database compreso nel relativo periodo di conservazione.

I database Basic dispongono di 7 giorni di conservazione, i database Standard di 14 giorni e i database Premium di 35 giorni. Per altre informazioni sulla conservazione del database, vedere la [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

Per eseguire un ripristino temporizzato vedere:

- [Ripristino temporizzato con il portale di Azure](sql-database-point-in-time-restore-portal.md)
- [Ripristino temporizzato con PowerShell](sql-database-point-in-time-restore-powershell.md)
- [Ripristino temporizzato con API REST (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## Ripristino di un database eliminato

Nel caso in cui venisse eliminato un database, il database SQL di Azure consente di ripristinare il database eliminato al momento dell'eliminazione. Il database SQL di Azure archivia il backup del database eliminato per il periodo di conservazione del database.

Il periodo di conservazione di un database eliminato è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso). Per altre informazioni sulla conservazione del database, vedere l'articolo sui [backup automatici](sql-database-automated-backups.md).

Per ripristinare un database eliminato:

- [Ripristinare un database eliminato con il portale di Azure](sql-database-restore-deleted-database-portal.md)
- [Ripristinare un database eliminato con PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Ripristinare un database eliminato con l'API REST (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->