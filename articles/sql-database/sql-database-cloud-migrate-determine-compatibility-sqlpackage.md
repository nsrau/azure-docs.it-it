<properties
   pageTitle="Determinare la compatibilità del database SQL utilizzando SqlPackage.exe | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, SqlPackage"
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

# Determinare la compatibilità del database SQL utilizzando SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Preparazione aggiornamento](http://www.microsoft.com/download/details.aspx?id=48119)
- [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)

Questo articolo illustra come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Utilizzo di SqlPackage.exe

1. Aprire un prompt dei comandi e passare a una directory contenente la versione più recente di sqlpackage.exe. Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/it-IT/download/details.aspx?id=53876) direttamente dall'area download Microsoft.
2. Eseguire il comando SqlPackage riportato di seguito con gli argomenti seguenti per l'ambiente:

	'sqlpackage.exe /Action:Export /ssn:< server\_name > /sdn:< database\_name > /tf:< target\_file > /p:TableData=< schema\_name.table\_name > > < output\_file > 2>&1'

	| Argomento | Descrizione |
	|---|---|
	| < server\_name > | nome server di origine |
	| < database\_name > | nome database di origine |
	| < target\_file > | nome e percorso del file BACPAC |
	| < schema\_name.table\_name > | tabelle per cui i dati vengono restituiti nel file di destinazione |
	| < output\_file > | nome e percorso del file di output con eventuali errori |

	L'argomento /p:TableName viene usato per indicare che si vuole testare la compatibilità del database solo per l'esportazione nella versione 12 del database SQL di Azure, invece di esportare i dati da tutte le tabelle. L'argomento export per sqlpackage.exe non supporta tuttavia l'estrazione di zero tabelle. È necessario specificare almeno una tabella, ad esempio una singola tabella di piccole dimensioni. < output\_file > contiene il report degli eventuali errori. La stringa "> 2>&1" indirizza sia l'output standard che l'errore standard risultante dall'esecuzione del comando al file di output specificato.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Aprire il file di output ed esaminare gli eventuali errori di compatibilità.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [Risolvere i problemi di compatibilità della migrazione del database](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Risorse aggiuntive

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0921_2016-->