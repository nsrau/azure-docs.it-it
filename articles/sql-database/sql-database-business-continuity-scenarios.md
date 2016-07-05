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
	ms.date="06/16/2016"
	ms.author="carlrab"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Scenari di continuità aziendale del database SQL di Azure

> [AZURE.SELECTOR]
- [Continuità aziendale](sql-database-business-continuity.md)
- [Scenari](sql-database-business-continuity-scenarios.md)
- [Ripristino temporizzato](sql-database-point-in-time-retore.md)
- [Ripristinare un database eliminato](sql-database-restore-deleted-database.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication)

Questo articolo offre informazioni su diversi scenari di continuità aziendale del database SQL di Azure.

## Ripristino dopo un'interruzione del servizio

[Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md) descrive come eseguire il ripristino in seguito a un'interruzione usando le funzionalità seguenti:

- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-geo-restore.md)

In questo articolo è stato descritto quando avviare il ripristino, come eseguirlo usando ogni funzionalità, come configurare il database dopo il ripristino e come configurare l'applicazione dopo il ripristino.

## Ripristino da un errore utente

[Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente](sql-database-user-error-recovery.md) descrive come eseguire il ripristino in caso di errore dell'utente o modifica imprevista dei dati usando le funzionalità seguenti:

- [Ripristino temporizzato](sql-database-point-in-time-restore.md) 
- [Ripristinare un database eliminato](sql-database-restore-deleted-database.md)

## Esercitazione per il ripristino di emergenza

[Esercitazione per il ripristino di emergenza](sql-database-disaster-recovery-drills.md) illustra come eseguire un'esercitazione di ripristino di emergenza usando le funzionalità seguenti:

- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Ripristino geografico](sql-database-geo-restore.md)

È consigliabile verificare periodicamente la conformità delle applicazioni per il flusso di lavoro di ripristino. La verifica del comportamento di un'applicazione e delle implicazioni legate alla perdita di dati e/o all'interruzione del servizio che comporta il failover è una buona norma di progettazione. Inoltre, questa verifica è richiesta dalla maggior parte degli standard del settore come parte della certificazione di continuità aziendale.

L'esercitazione per il ripristino di emergenza prevede l'esecuzione delle attività seguenti:

- Simulazione dell'interruzione del livello dati
- Ripristino 
- Convalida dell'integrità dell'applicazione dopo il ripristino

## Gestione della sicurezza dopo il ripristino di emergenza

[Come gestire la sicurezza dopo il ripristino di emergenza](sql-database-geo-replication-security-config.md) illustra i requisiti di autenticazione per configurare e controllare la [replica geografica attiva](sql-database-geo-replication-overview.md) e i passaggi necessari per configurare l'accesso utente al database secondario. Descrive anche come abilitare l'accesso al database ripristinato dopo il [ripristino geografico](sql-database-geo-restore.md).

## Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva

[Gestione degli aggiornamenti in sequenza delle applicazioni cloud con la replica geografica attiva del database SQL](sql-database-manage-application-rolling-upgrade.md) descrive come usare la [replica geografica](sql-database-geo-replication-overview.md) nel database SQL per abilitare gli aggiornamenti in sequenza dell'applicazione cloud. L'aggiornamento dovrebbe far parte della pianificazione e della progettazione della continuità aziendale perché è un'operazione che comporta interruzioni. L'articolo esamina due metodi diversi per orchestrare il processo di aggiornamento e approfondisce vantaggi e compromessi relativi a ciascuna opzione.

## Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva

[Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva nel database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) descrive come usare la [replica geografica attiva](sql-database-geo-replication-overview.md) nel database SQL per progettare applicazioni resilienti a guasti a livello di area e interruzioni irreversibili. Questo articolo esamina la topologia di distribuzione dell'applicazione, il contratto di servizio di destinazione, la latenza del traffico e i costi e quindi valuta i vantaggi e i compromessi dei modelli di applicazione comuni.

## Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database

[Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) descrive gli scenari di ripristino d'emergenza che usano i [pool elastici del database](sql-database-elastic-pool.md).

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