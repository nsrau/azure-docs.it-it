<properties
   pageTitle="Continuità aziendale cloud - Ripristino di un database eliminato - Database SQL | Microsoft Azure"
   description="Informazioni sul ripristino temporizzato, che consente di ripristinare un database SQL di Azure a un momento precedente (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Panoramica: Ripristinare un database SQL di Azure eliminato

> [AZURE.SELECTOR]
- [Panoramica](sql-database-restore-deleted-database.md)
- [Portale di Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

È possibile ripristinare un database eliminato durante il periodo di conservazione per i [Backup automatici del database SQL](sql-database-automated-backups.md). È possibile usare il [portale di Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Se si elimina un database, il backup finale viene conservato per il normale periodo di conservazione che consente di ripristinare il database nel punto in cui è stato eliminato.

## Ripristino di un database eliminato di recente

Per i database eliminati il punto di ripristino è fissato all'ora di eliminazione del database. Quando si ripristina un database eliminato, può essere ripristinato solo nel server che conteneva il database originale. Tenerlo presente quando si elimina un server, perché non sarà possibile ripristinare i database precedentemente dislocati in tale server una volta eliminato.

> [AZURE.IMPORTANT] Se si elimina un'istanza del server di database SQL di Azure, vengono eliminati anche tutti i relativi database e non possono essere recuperati.

## Riepilogo

I backup automatici proteggono i database da eliminazioni accidentali. Questa soluzione a costo zero e senza l'intervento dell'amministratore è disponibile con tutti i database SQL.

## Passaggi successivi

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Ripristinare un database eliminato con il portale di Azure](sql-database-restore-deleted-database-portal.md)
- [Ripristinare un database SQL di Azure con PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Ripristinare un database eliminato con l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backup automatici del database SQL](sql-database-automated-backups.md)

## Risorse aggiuntive

- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->