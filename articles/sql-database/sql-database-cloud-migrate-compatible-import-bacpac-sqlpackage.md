<properties
   pageTitle="Importare nel database SQL da un file BACPAC"
   description="Database SQL di Microsoft Azure, migrazione del database, importazione del database, importazione del file BACPAC, sqlpackage"
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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Importare nel database SQL da un file BACPAC

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In questo articolo viene illustrato come importare in un database SQL Server da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizzando l’utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.

> [AZURE.NOTE] I passaggi seguenti presuppongono che sia già stato effettuato il provisioning di un server del database SQL, le informazioni di connessione siano disponibili e sia stata verificata la compatibilità del database di origine.

## Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SqlPackage

Eseguire i passaggi seguenti in modo da usare l'utilità della riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database del server SQL compatibile o un database SQL di Azure da un file BACPAC.

> [AZURE.NOTE] Il passaggio precedente presuppone che sia già stato effettuato il provisioning di un server del database SQL di Azure e che le informazioni di connessione siano disponibili.

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server.
2. Eseguire il comando sqlpackage.exe riportato di seguito con gli argomenti seguenti per l'ambiente:

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Argomento | Descrizione |
	|---|---|
	| < server_name > | nome server di destinazione |
	| < database_name > | nome database di destinazione |
	| < user_name > | nome utente nel server di destinazione |
	| < password > | password utente |
	| < source_file > | nome e percorso del file BACPAC importato |

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_0316_2016-->