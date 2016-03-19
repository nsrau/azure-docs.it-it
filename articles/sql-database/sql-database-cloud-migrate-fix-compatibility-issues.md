<properties
   pageTitle="Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure"
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

# Risolvere i problemi di compatibilità del database del server SQL prima di effettuare la migrazione al database SQL

Se si determina che il database del server SQL di origine non è compatibile, sarà possibile risolvere in diversi modi i problemi di compatibilità identificati in precedenza.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Utilizzare la migrazione guidata di SQL Azure

È possibile usare lo strumento CodePlex [Migrazione guidata di SQL Azure](http://sqlazuremw.codeplex.com/) per generare uno script T-SQL da un database di origine non compatibile che viene quindi trasformato dalla procedura guidata per renderlo compatibile con il database SQL e quindi effettuare la connessione al database SQL di Azure per eseguire lo script. Questo strumento analizzerà anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP. Per una documentazione aggiuntiva, incluse indicazioni dettagliate, vedere la [Migrazione guidata di SQL Azure](http://sqlazuremw.codeplex.com/) su CodePlex.

 ![Diagramma di migrazione di SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]Si noti che non tutti gli schemi incompatibili che possono essere rilevati dalla procedura guidata possono essere risolti dalla relativa funzionalità di trasformazione incorporata. Script incompatibili che non possono essere corretti verranno segnalati come errori con i commenti inseriti nello script generato. Se vengono rilevati molti errori, usare Visual Studio o SQL Server Management Studio per esaminare e risolvere ogni errore che non è stato risolto mediante la migrazione guidata di SQL Server.

## Passaggio successivo: selezionare il metodo di migrazione ed eseguire la migrazione

[Selezionare il metodo di migrazione](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->