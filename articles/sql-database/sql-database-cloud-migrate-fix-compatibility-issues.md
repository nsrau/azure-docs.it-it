<properties
   pageTitle="Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
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

# Usare la Migrazione guidata database SQL di Azure per risolvere i problemi di compatibilità dei database di SQL Server prima di eseguire la migrazione al database SQL di Azure

> [AZURE.SELECTOR]
- Usare [Migrazione guidata SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Usare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Usare [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Questo articolo illustra come individuare e risolvere i problemi di compatibilità dei database di SQL Server usando la Migrazione guidata database SQL di Azure prima di eseguire la migrazione al database SQL di Azure.

## Utilizzare la migrazione guidata di SQL Azure

È possibile usare lo strumento CodePlex [Migrazione guidata di SQL Azure](http://sqlazuremw.codeplex.com/) per generare uno script T-SQL da un database di origine non compatibile che viene quindi trasformato dalla procedura guidata per renderlo compatibile con il database SQL e quindi effettuare la connessione al database SQL di Azure per eseguire lo script. Questo strumento analizzerà anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP. Per una documentazione aggiuntiva, incluse indicazioni dettagliate, vedere la [Migrazione guidata di SQL Azure](http://sqlazuremw.codeplex.com/) su CodePlex.

 ![Diagramma di migrazione di SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Si noti che non tutti gli schemi incompatibili che possono essere rilevati dalla procedura guidata possono essere risolti dalla relativa funzionalità di trasformazione incorporata. Script incompatibili che non possono essere corretti verranno segnalati come errori con i commenti inseriti nello script generato. Se vengono rilevati molti errori, usare Visual Studio o SQL Server Management Studio per esaminare e risolvere ogni errore che non è stato risolto mediante la migrazione guidata di SQL Server.

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Altre informazioni

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->