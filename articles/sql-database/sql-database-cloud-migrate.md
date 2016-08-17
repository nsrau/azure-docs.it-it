<properties
   pageTitle="Migrazione di un database SQL Server al database SQL | Microsoft Azure"
   description="Informazioni sulla migrazione del database SQL Server locale al database SQL di Azure nel cloud. Usare gli strumenti di migrazione del database per verificare la compatibilità prima della migrazione del database."
   keywords="migrazione di database, migrazione di database sql server, strumenti di migrazione del database, eseguire la migrazione di database, eseguire la migrazione di database sql"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Migrazione di un database SQL Server al database SQL nel cloud

Questo articolo illustra come eseguire la migrazione di un database locale di SQL Server 2005 o versione successiva a un database SQL di Azure. In questo processo di migrazione del database si esegue la migrazione dello schema e dei dati dal database SQL Server nell'ambiente corrente al database SQL, a condizione che il database esistente superi i test di compatibilità. Con la [versione 12 del database SQL](sql-database-v12-whats-new.md) esistono pochissimi problemi di compatibilità rimanenti, a eccezione delle operazioni di livello server e tra database. I database e le applicazioni basate su [funzionalità non supportate o supportate parzialmente](sql-database-transact-sql-information.md) dovranno essere riprogettate per risolvere tali incompatibilità prima della migrazione del database di SQL Server.

I passaggi necessari alla migrazione sono i seguenti:

- **Test per la compatibilità**: la prima cosa da fare è convalidare la compatibilità del database con la [versione 12 del database SQL](sql-database-v12-whats-new.md).
- **Risolvere gli eventuali problemi di compatibilità**: se la convalida non riesce, è necessario correggere gli errori.
- **Eseguire la migrazione**: una volta che il database è compatibile, è possibile utilizzare uno o più metodi per eseguire la migrazione.

SQL Server fornisce diversi metodi per eseguire ognuna di queste attività. Questo articolo illustra una panoramica dei metodi disponibili per queste attività. Il diagramma seguente illustra passaggi e metodi.

  ![Diagramma di migrazione di VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] Per eseguire la migrazione di database diversi dai database di SQL Server, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 nel database SQL di Azure, vedere il post di blog su [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).

## Verificare la compatibilità del database SQL Server con il database SQL mediante gli strumenti di migrazione del database

Per verificare i problemi di compatibilità del database SQL prima di iniziare il processo di migrazione del database, usare uno dei metodi seguenti:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Preparazione aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SSDT (SQL Server Data Tools) per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa le regole di compatibilità più aggiornate per rilevare le incompatibilità con la versione 12 del database SQL. Se rileva problemi di compatibilità, questo strumento è in grado di risolverli direttamente. Attualmente questo è il metodo consigliato per testare e risolvere i problemi di compatibilità con la versione 12 del database SQL.
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage è un'utilità della riga di comando che esegue test per rilevare i problemi di compatibilità e genera un report contenente i problemi rilevati. Se si utilizza questo strumento, assicurarsi di utilizzare la versione più recente per utilizzare le regole di compatibilità più aggiornate. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [Procedura guidata dell'applicazione livello dati in SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): la procedura guidata rileva gli errori e genera un report a schermo. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [Anteprima di Preparazione aggiornamento di Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119): questo strumento autonomo, attualmente in anteprima, rileva e genera un report degli errori di compatibilità con la versione 12 del database SQL. Questo strumento non dispone ancora delle regole di compatibilità più recenti. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [SAMW (SQL Azure Migration Wizard, Migrazione guidata database SQL di Microsoft Azure)](sql-database-cloud-migrate-fix-compatibility-issues.md): si tratta di uno strumento codeplex che usa le regole di compatibilità della versione 11 del database SQL di Microsoft Azure per rilevare errori di compatibilità con la versione 12 del database SQL di Microsoft Azure. Nel caso in cui vengano rilevati errori di compatibilità, alcuni problemi possono essere risolti direttamente da questo strumento. È possibile che questo strumento rilevi errori di compatibilità che non è necessario correggere ma è stato il primo strumento di supporto alla migrazione del database SQL di Microsoft Azure, quindi dispone di molto materiale di supporto nella community di SQL Server. Inoltre, è possibile completare la migrazione direttamente da questo strumento.

## Risolvere i problemi di compatibilità della migrazione del database

Se si rilevano problemi di compatibilità, è necessario risolverli prima di procedere con la migrazione del database SQL Server. Esiste una vasta gamma di problemi di compatibilità che possono verificarsi, a seconda della versione di SQL Server del database di origine e della complessità del database verso cui si esegue la migrazione. Una versione di SQL Server molto datata per il database di origine creerà potenzialmente più problemi di compatibilità rispetto alle versioni più recenti. Oltre a una ricerca mirata su Internet tramite i propri motori di ricerca preferiti, si consiglia di usare le risorse seguenti:

- [Funzionalità del database di SQL Server non supportate nel database SQL di Microsoft Azure](sql-database-transact-sql-information.md)
- [Funzionalità del motore di database sospese in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funzionalità del motore di database sospese in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funzionalità del motore di database sospese in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funzionalità del motore di database sospese in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funzionalità del motore di database sospese in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Per risolvere al meglio eventuali problemi di compatibilità, oltre a eseguire ricerche su Internet e usare queste risorse, consultare il [forum della community di MSDN SQL Server ](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

Per correggere gli errori rilevati, usare uno dei seguenti strumenti di migrazione di database:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Usare [SSDT (SQL Server Data Tools) per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): per usare SSDT, importare lo schema del database in SQL Server Data Tools per Visual Studio, compilare il progetto per una distribuzione della versione 12 del database SQL, risolvere tutti i problemi di compatibilità rilevati in SSDT e sincronizzare quindi le modifiche con il database di origine o con una sua copia. Attualmente questo è il metodo consigliato per testare e risolvere i problemi di compatibilità con la versione 12 del database SQL. Fare clic sul collegamento relativo alla [procedura dettagliata per l'uso di SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Usare [SSMS (SQL Server Management Studio)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): per usare SSMS, è necessario eseguire i comandi di Transact-SQL per correggere gli errori rilevati con un altro strumento. Questo metodo è destinato principalmente agli utenti avanzati che possono modificare lo schema del database direttamente nel database di origine.
- Usare [SAMW (SQL Azure Migration Wizard, Migrazione guidata database SQL di Microsoft Azure)](sql-database-cloud-migrate-fix-compatibility-issues.md): per usare SAMW, generare uno script Transact-SQL dal database di origine. Quando possibile, tale script viene trasformato dalla procedura guidata per rendere lo schema compatibile con la versione 12 del database SQL. Una volta completata questa operazione, SAMW si connette alla versione 12 del database SQL per eseguire lo script. Questo strumento analizzerà anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP.

## Eseguire la migrazione di un database di SQL Server compatibile nel database SQL

Per eseguire la migrazione di un database di SQL Server compatibile, Microsoft fornisce diversi metodi di migrazione per i vari scenari. Il metodo scelto dipende dalla tolleranza per i tempi di inattività, dalle dimensioni e dalla complessità del database di SQL Server e dalla connettività al cloud di Microsoft Azure.

> [AZURE.SELECTOR]
- [Migrazione guidata in SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in un file BACPAC.](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importare da file BACPAC.](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Per scegliere il metodo di migrazione, è necessario stabilire innanzitutto se è possibile portare il database fuori produzione durante la migrazione. La migrazione di un database mentre si verificano transazioni attive può causare problemi di coerenza e un possibile danneggiamento del database. Per disattivare un database sono disponibili vari metodi, dalla disabilitazione della connettività client alla creazione di uno [snapshot del database](https://msdn.microsoft.com/library/ms175876.aspx).

Per eseguire la migrazione con tempi di inattività minimi, usare la [replica di tipo transazionale di SQL Server](sql-database-cloud-migrate-compatible-using-transactional-replication.md) a condizione che il database soddisfi i requisiti per la replica di tipo transazionale. Se si è disposti a tollerare un certo tempo di inattività o se si esegue una migrazione di prova del database di produzione per una migrazione successiva, considerare uno dei tre metodi seguenti:

- [Migrazione guidata di SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): per i database di piccole e medie dimensioni, la migrazione di database compatibili con SQL Server 2005 o versioni successive è semplice quanto l'esecuzione della [distribuzione guidata del database al database SQL di Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) in SQL Server Management Studio.
- [Esportare in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e quindi [Importare da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): in caso di problemi di connettività (nessuna connettività, larghezza di banda ridotta o problemi di timeout) e per database di medie e grandi dimensioni, usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con questo metodo, si esporta lo schema di SQL Server e i dati in un file BACPAC e quindi si importa il file BACPAC nel database SQL mediante la configurazione guidata applicazione per l'esportazione del livello dati in SQL Server Management Studio o l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Usare insieme file BACPAC e BCP: usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per database molto grandi per ottenere una parallelizzazione maggiore per prestazioni migliori, sebbene ciò comporti maggiore complessità. Con questo metodo la migrazione dei dati e dello schema viene eseguita separatamente.
 - [Esportare lo schema solo in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importare lo schema solo da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) nel database SQL.
 - Usare [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per estrarre i dati in file flat e quindi eseguire un [caricamento parallelo](https://technet.microsoft.com/library/dd425070.aspx) di questi file nel database SQL di Azure.

	 ![Migrazione di database SQL Server - Migrazione di database SQL nel cloud.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## Passaggi successivi

- [Anteprima di Preparazione aggiornamento di Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119)
- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

##Risorse aggiuntive

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md) [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->