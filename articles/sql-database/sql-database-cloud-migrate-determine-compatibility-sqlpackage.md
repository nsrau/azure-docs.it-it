<properties
   pageTitle="Determinare la compatibilità del database SQL utilizzando SqlPackage.exe"
   description="Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, SqlPackage"
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

# Determinare la compatibilità del database SQL utilizzando SqlPackage.exe

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

In questo articolo viene illustrato come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite l’utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Utilizzo di SqlPackage.exe

1. Aprire un prompt dei comandi e passare a una directory contenente la versione più recente di sqlpackage.exe. Questa utilità è disponibile sia in Visual Studio che in SQL Server. Scaricare la [versione più recente di SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) per ottenere la versione più aggiornata dell'utilità SqlPackage.
2. Eseguire il comando SqlPackage riportato di seguito con gli argomenti seguenti per l'ambiente:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Argomento | Descrizione |
	|---|---|
	| < server_name > | nome server di origine |
	| < database_name > | nome database di origine |
	| < target_file > | nome e percorso del file BACPAC |
	| < schema_name.table_name > | tabelle per cui i dati verranno restituiti nel file di destinazione |
	| < output_file > | nome e percorso del file di output con eventuali errori |

	L'argomento /p:TableName viene usato per indicare che si vuole testare la compatibilità del database solo per l'esportazione nella versione 12 del database SQL di Azure, invece di esportare i dati da tutte le tabelle. Sfortunatamente, l'argomento di esportazione per sqlpackage.exe non consente di evitare l'esportazione di tabelle, quindi sarà necessario specificare una singola tabella di piccole dimensioni. < output_file > includerà il report relativo a eventuali errori. La stringa "> 2>&1" indirizza sia l'output standard che l'errore standard risultante dall'esecuzione del comando al file di output specificato.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Aprire il file di output ed esaminare gli eventuali errori di compatibilità.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Passaggio successivo: risolvere i problemi di compatibilità, se presenti

[Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues.md), se presenti.

<!---HONumber=AcomDC_1223_2015-->