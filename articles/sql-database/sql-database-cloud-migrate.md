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
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# Migrazione di un database SQL Server al database SQL nel cloud

Questo articolo illustra come eseguire la migrazione di un database locale di SQL Server 2005 o versione successiva a un database SQL di Azure. In questo processo di migrazione del database si esegue la migrazione dello schema e dei dati dal database SQL Server nell'ambiente corrente al database SQL. Per avere esito positivo, il database esistente deve prima superare un test di compatibilità. Con la [versione 12 del database SQL](sql-database-v12-whats-new.md) esistono pochissimi problemi di compatibilità rimanenti, a eccezione del problema collegato alle operazioni di livello server e tra database. I database e le applicazioni basati su [funzionalità non supportate o supportate parzialmente](sql-database-transact-sql-information.md) devono essere riprogettati per risolvere tali incompatibilità prima della migrazione del database di SQL Server.

Di seguito sono riportati i passaggi da intraprendere per eseguire la migrazione:

- **Test per la compatibilità**: convalidare la compatibilità del database con la [versione 12 del database SQL](sql-database-v12-whats-new.md).
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

- [SSDT (SQL Server Data Tools) per Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT usa le regole di compatibilità più aggiornate per rilevare le incompatibilità con la versione 12 del database SQL. Se rileva problemi di compatibilità, questo strumento è in grado di risolverli direttamente. Questo è il metodo consigliato per testare e risolvere i problemi di compatibilità con la versione 12 del database SQL.
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage è un'utilità della riga di comando che esegue test per rilevare i problemi di compatibilità e genera un report contenente i problemi rilevati. Se si usa questo strumento, assicurarsi di usare la versione più recente per fare in modo che le regole di compatibilità siano le più aggiornate. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [Procedura guidata di esportazione dell'applicazione livello dati in SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): la procedura guidata rileva gli errori e genera un report a schermo. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [Anteprima di Preparazione aggiornamento di Microsoft SQL Server 2016](http://www.microsoft.com/download/details.aspx?id=48119): questo strumento autonomo, attualmente in anteprima, rileva e genera un report degli errori di compatibilità con la versione 12 del database SQL. Questo strumento non dispone ancora delle regole di compatibilità più recenti. Se non vengono rilevati errori, è possibile continuare e completare la migrazione al database SQL. Se vengono rilevati errori, è necessario utilizzare un altro strumento per risolvere i problemi di compatibilità rilevati, in particolare si consiglia di usare SSDT.
- [SAMW (SQL Azure Migration Wizard, Migrazione guidata database SQL di Microsoft Azure)](sql-database-cloud-migrate-fix-compatibility-issues.md): si tratta di uno strumento codeplex che usa le regole di compatibilità della versione 11 del database SQL di Microsoft Azure per rilevare errori di compatibilità con la versione 12 del database SQL di Microsoft Azure. Nel caso in cui vengano rilevati errori di compatibilità, alcuni problemi possono essere risolti direttamente da questo strumento. Questo strumento può rilevare incompatibilità che non è necessario correggere. È stato il primo strumento di assistenza per la migrazione del database SQL di Azure ed è supportato attivamente dalla community di SQL Server. Inoltre, è possibile completare la migrazione direttamente da questo strumento.

## Risolvere i problemi di compatibilità della migrazione del database

Se si rilevano problemi di compatibilità, è necessario risolverli prima di procedere con la migrazione del database SQL Server. Esiste una vasta gamma di problemi di compatibilità che possono verificarsi, a seconda della versione del server SQL del database di origine e della complessità del database verso cui si esegue la migrazione. Le versioni precedenti di SQL Server presentano più problemi di compatibilità. Oltre a una ricerca mirata su Internet tramite i propri motori di ricerca preferiti, si consiglia di usare le risorse seguenti:

- [Funzionalità del database di SQL Server non supportate nel database SQL di Microsoft Azure](sql-database-transact-sql-information.md)
- [Funzionalità del motore di database sospese in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funzionalità del motore di database sospese in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funzionalità del motore di database sospese in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funzionalità del motore di database sospese in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funzionalità del motore di database sospese in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oltre a ricerche su Internet e all'uso di queste risorse, usare il [forum della community MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

Per correggere gli errori rilevati, usare uno dei seguenti strumenti di migrazione di database:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Usare [SQL Server Data Tools per Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): per usare SSDT, importare lo schema del database in SQL Server Data Tools per Visual Studio ("SSDT") e compilare il progetto per una distribuzione della versione 12 del database SQL. A questo punto è necessario correggere tutti i problemi di compatibilità rilevati in SSDT. Al termine, risincronizzare le modifiche con il database di origine (o con una copia del database di origine). SSDT è attualmente il metodo consigliato per testare e risolvere i problemi di compatibilità con la versione 12 del database SQL. Fare clic sul collegamento relativo alla [procedura dettagliata per l'uso di SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Usare [SSMS (SQL Server Management Studio)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): per usare SSMS, è necessario eseguire i comandi di Transact-SQL per correggere gli errori rilevati con un altro strumento. Questo metodo è destinato principalmente agli utenti avanzati che possono modificare lo schema del database direttamente nel database di origine.
- Usare [SQL Azure Migration Wizard ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): per usare SMAW, si genera uno script Transact-SQL dal database di origine. La procedura guidata consente di trasformare lo script, laddove possibile, per rendere compatibile lo schema con la versione 12 del database SQL. Una volta completata questa operazione, SAMW si connette alla versione 12 del database SQL per eseguire lo script. Questo strumento analizza anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP.

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
- [Esportare in un file BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) e quindi [Importare da un file BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): in caso di problemi di connettività (nessuna connettività, larghezza di banda ridotta o problemi di timeout) e per database di medie e grandi dimensioni, usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4). Con questo metodo, i dati e lo schema di SQL Server vengono esportati in un file BACPAC. A questo punto si importa il file BACPAC nel database SQL mediante la procedura guidata di esportazione dell'applicazione livello dati in SQL Server Management Studio o l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).
- Usare file BACPAC e BCP insieme: usare un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) e un file [BCP](https://msdn.microsoft.com/library/ms162802.aspx) per database molto più grandi per ottenere una parallelizzazione maggiore per prestazioni migliori, sebbene ciò comporti maggiore complessità. Con questo metodo la migrazione dei dati e dello schema viene eseguita separatamente.
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

<!---HONumber=AcomDC_0831_2016-->