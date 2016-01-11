<properties
   pageTitle="Migrazione di un database SQL Server nel database SQL di Azure"
   description="Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Migrazione di un database SQL Server nel database SQL di Azure

Questo articolo illustra come eseguire la migrazione di un database locale di SQL Server 2005 o versione successiva a un database SQL di Azure. In questo processo si esegue la migrazione dello schema e dei dati dal database di SQL Server nell'ambiente corrente al database SQL, a condizione che il database esistente superi i test di compatibilità. Con la [versione 12 del database SQL](sql-database-v12-whats-new.md) esistono pochissimi problemi di compatibilità rimanenti, a eccezione delle operazioni di livello server e tra database. I database e le applicazioni basate su [funzionalità non supportate o supportate parzialmente](sql-database-transact-sql-information.md) dovranno essere riprogettate per [risolvere tali incompatibilità](sql-database-cloud-migrate-fix-compatibility-issues.md) prima della migrazione del database di SQL Server.

> [AZURE.NOTE]Per eseguire la migrazione di database diversi dai database di SQL Server, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 nel database SQL di Azure, vedere il post di blog su [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).

## Determinare se un database di SQL Server è compatibile per la migrazione al database SQL

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

Per verificare i problemi di compatibilità del database SQL prima di iniziare il processo di migrazione, usare uno dei metodi seguenti:

- [Usa SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage è un'utilità della riga di comando che esegue test per rilevare i problemi di compatibilità e genera un report contenente i problemi rilevati.
- [Usare SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): nella configurazione guidata applicazione per l'esportazione del livello dati in SQL Server Management Studio verranno visualizzati gli errori rilevati sullo schermo.

## Risolvere i problemi di compatibilità

Se vengono rilevati problemi di compatibilità, è necessario risolverli prima di procedere con la migrazione.

- Usare [Migrazione guidata SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usare [SQL Server Data Tools per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usare [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Eseguire la migrazione di un database di SQL Server compatibile nel database SQL

Per eseguire la migrazione di un database di SQL Server compatibile, Microsoft fornisce diversi metodi di migrazione per i vari scenari. Il metodo scelto dipende dalla tolleranza per i tempi di inattività, dalle dimensioni e dalla complessità del database di SQL Server e dalla connettività al cloud di Microsoft Azure.

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Per scegliere il metodo di migrazione, è necessario stabilire innanzitutto se è possibile portare il database fuori produzione durante la migrazione. La migrazione di un database mentre si verificano transazioni attive può causare problemi di coerenza e un possibile danneggiamento del database. Per disattivare un database sono disponibili vari metodi, dalla disabilitazione della connettività client alla creazione di uno [snapshot del database](https://msdn.microsoft.com/library/ms175876.aspx).

Per eseguire la migrazione con tempi di inattività minimi, usare la [replica di tipo transazionale di SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) a condizione che il database soddisfi i requisiti per la replica di tipo transazionale. Se si è disposti a tollerare un certo tempo di inattività o se si esegue una migrazione di prova del database di produzione per una migrazione successiva, considerare uno dei tre metodi seguenti:

- [Migrazione guidata di SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): per i database di piccole e medie dimensioni, la migrazione di database compatibili con SQL Server 2005 o versioni successive è semplice quanto l'esecuzione della [distribuzione guidata del database al database SQL di Microsoft Azure](sql-database-cloud-migrate-compatible-using-migration-wizard.md) in SQL Server Management Studio. 
- [Esportare in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e quindi [Importare da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): in caso di problemi di connettività (nessuna connettività, larghezza di banda ridotta o problemi di timeout) e per database di medie e grandi dimensioni, usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con questo metodo, si esporta lo schema di SQL Server e i dati in un file BACPAC e quindi si importa il file BACPAC nel database SQL mediante la configurazione guidata applicazione per l'esportazione del livello dati in SQL Server Management Studio o l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Usare insieme file BACPAC e BCP: usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per database molto grandi per ottenere una parallelizzazione maggiore per prestazioni migliori, sebbene ciò comporti maggiore complessità. Con questo metodo la migrazione dei dati e dello schema viene eseguita separatamente. 
 - [Esportare lo schema solo in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importare lo schema solo da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) nel database SQL. 
 - Usare [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per estrarre i dati in file flat e quindi eseguire un [caricamento parallelo](https://technet.microsoft.com/library/dd425070.aspx) di questi file nel database SQL di Azure.

	 ![Diagramma di migrazione di SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_1223_2015-->