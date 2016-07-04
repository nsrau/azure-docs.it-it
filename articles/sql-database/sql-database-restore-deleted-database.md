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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Panoramica: Ripristinare un database SQL di Azure eliminato

> [AZURE.SELECTOR]
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristinare un database eliminato](sql-database-restore-deleted-database.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)


È possibile ripristinare un database eliminato durante il periodo di conservazione per i [Backup automatici del database SQL](sql-database-automated-backups.md) per il proprio [livello di servizio](sql-database-service-tiers.md). È possibile usare il [portale di Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) o l'[API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## Ripristino di un database eliminato di recente

È possibile ripristinare il database eliminato assegnando al database lo stesso nome del server logico contenente il database originale oppure un nome diverso. Per i database eliminati il punto di ripristino è fissato all'ora di eliminazione del database.

> [AZURE.IMPORTANT] Se si elimina un'istanza del server di database SQL di Azure, vengono eliminati anche tutti i relativi database e non possono essere recuperati.

## Tempi di ripristino

Il tempo impiegato per ripristinare un database dipende da molti fattori, tra cui la dimensione del database, il numero di log delle transazioni, il punto di ripristino selezionato e la quantità di attività che deve essere ripetuta per ricostituire lo stato nel punto selezionato. Per un database molto grande e/o attivo il ripristino potrebbe richiedere diverse ore. Il ripristino di un database crea sempre un nuovo database nello stesso server del database originale, quindi al database ripristinato deve essere assegnato un nuovo nome. Per la maggior parte dei database, il ripristino richiede al massimo 12 ore.

## Riepilogo

I backup automatici proteggono i database da eliminazioni accidentali. Questa soluzione a costo zero e senza l'intervento dell'amministratore è disponibile con tutti i database SQL.

## Passaggi successivi

- Per informazioni dettagliate su come ripristinare un database eliminato tramite il portale di Azure, vedere l'articolo su come [ripristinare un database eliminato tramite il portale di Azure](sql-database-restore-deleted-database-portal.md).
- Per informazioni dettagliate su come ripristinare un database eliminato tramite PowerShell, vedere l'articolo su come [ripristinare un database eliminato tramite PowerShell](sql-database-restore-deleted-database-powershell.md).
- Per informazioni su come ripristinare un database eliminato, vedere [ripristinare un database eliminato utilizzando l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Per informazioni dettagliate sul backup automatico dei database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).

## Risorse aggiuntive

- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->