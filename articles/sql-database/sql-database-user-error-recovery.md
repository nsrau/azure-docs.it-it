<properties 
   pageTitle="Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente | Microsoft Azure" 
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
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente

I database SQL di Azure offre due funzionalità principali per il ripristino a seguito di errori causati dagli utenti o a seguito di una modifica imprevista dei dati.

- [Ripristino temporizzato](sql-database-point-in-time-restore.md) 
- [Ripristinare un database eliminato](sql-database-restore-deleted-database.md)

Il database SQL di Azure viene sempre ripristinato con un nuovo database. Le funzionalità di ripristino vengono offerte a tutti i clienti del database: Basic, Standard e Premium.

##Ripristino temporizzato

In caso di errore dell'utente o di una modifica imprevista dei dati, il ripristino temporizzato consente di ripristinare un qualsiasi momento del ciclo di vita del database compreso nel relativo periodo di conservazione.

I database Basic dispongono di 7 giorni di conservazione, i database Standard di 14 giorni e i database Premium di 35 giorni. Per altre informazioni sulla conservazione del backup del database, vedere l'articolo sui [backup automatici](sql-database-automated-backups.md).

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


## Passaggi successivi

- Per informazioni sull'uso e la configurazione della replica geografica attiva per il ripristino di emergenza, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per informazioni sull'uso del ripristino geografico per il ripristino di emergenza, vedere [Ripristino geografico](sql-database-geo-restore.md)

## Risorse aggiuntive

- [Continuità aziendale e ripristino di emergenza nel database SQL](sql-database-business-continuity.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Configurazione della sicurezza per la replica geografica](sql-database-geo-replication-security-config.md)
- [Domande frequenti su continuità aziendale e ripristino di emergenza nel database SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->