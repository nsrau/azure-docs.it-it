<properties
	pageTitle="Continuità aziendale del database SQL di Azure | Microsoft Azure"
	description="Scenari di continuità aziendale del database SQL di Azure"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Scenari di continuità aziendale del database SQL di Azure

Questo articolo presenta diversi scenari di ripristino di emergenza e vari scenari di progettazione di applicazioni per la continuità aziendale.

## Ripristino dopo un'interruzione del servizio

In caso di interruzione, consultare l'articolo [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md) che descrive come usare una delle soluzioni di continuità aziendale seguenti per il ripristino da interruzione:

- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-recovery-using.backups.md#geo-restore)

I passaggi specifici e il periodo di tempo necessario per il ripristino dopo un'interruzione del servizio variano a seconda della soluzione di continuità aziendale scelta. Tuttavia, indipendentemente dalla soluzione di continuità aziendale, è necessario sapere quando avviare il ripristino, conoscere la procedura di ripristino del database per ogni soluzione di continuità aziendale, sapere come configurare il database e l'applicazione dopo il ripristino per completare tale operazione dopo un'interruzione del servizio.

## Ripristino a seguito di un errore

In caso di errore dell'utente o di una modifica imprevista dei dati, consultare l'articolo[Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente](sql-database-user-error-recovery.md) che descrive come usare una delle soluzioni di continuità aziendale seguenti per il ripristino a seguito di errore:

- [Ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Ripristinare un database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore)

I passaggi specifici e il periodo di tempo necessario per il ripristino dopo un'interruzione del servizio variano a seconda della soluzione di continuità aziendale scelta. Tuttavia, indipendentemente dalla soluzione di continuità aziendale, è necessario sapere come eseguire il ripristino a seguito di un errore per ogni soluzione di continuità aziendale.

## Eseguire un'esercitazione per il ripristino di emergenza per prepararsi a un'eventuale interruzione

Per garantire l'efficacia della soluzione di continuità aziendale, è consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

- Simulazione dell'interruzione del livello dati
- Ripristino
- Convalida dell'integrità dell'applicazione dopo il ripristino

L'articolo [Esercitazione per il ripristino di emergenza](sql-database-disaster-recovery-drills.md) illustra come eseguire un'esercitazione di ripristino di emergenza usando una delle soluzioni di continuità aziendale seguenti:

- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-recovery-using-backups.md#geo-restore)

## Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva

L'aggiornamento di un'applicazione cloud con un database SQL è un'operazione che comporta interruzioni e, pertanto, è necessario includere questo scenario come parte della pianificazione e della progettazione della continuità aziendale. L'articolo [Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL](sql-database-manage-application-rolling-upgrade.md) viene descritto come usare la [replica geografica](sql-database-geo-replication-overview.md) nel database SQL per abilitare gli aggiornamenti in sequenza dell'applicazione cloud. L'articolo esamina due metodi diversi per orchestrare il processo di aggiornamento e approfondisce vantaggi e compromessi relativi a ogni opzione.

## Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva

L'articolo [Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva nel database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) descrive come usare la [replica geografica attiva](sql-database-geo-replication-overview.md) nel database SQL per progettare applicazioni resilienti a guasti a livello di area e interruzioni irreversibili. Questo articolo esamina la topologia di distribuzione dell'applicazione, il contratto di servizio di destinazione, la latenza del traffico e i costi e quindi valuta i vantaggi e i compromessi dei modelli di applicazione comuni.

## Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database

L'articolo [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) descrive gli scenari di ripristino d'emergenza che usano i [pool elastici del database](sql-database-elastic-pool.md).

## Passaggi successivi

- Per un quadro generale, vedere la [panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Panoramica: Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere l'articolo relativo agli [scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->