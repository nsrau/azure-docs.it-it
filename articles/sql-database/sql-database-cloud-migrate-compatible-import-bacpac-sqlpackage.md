<properties
   pageTitle="Importare nel database SQL da un file BACPAC"
   description="Database SQL di Microsoft Azure, migrazione del database, importazione del database, importazione del file BACPAC, sqlpackage"
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

# Importare nel database SQL da un file BACPAC

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portale di Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In questo articolo viene illustrato come importare in un database SQL Server da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.

> [AZURE.NOTE] I passaggi seguenti presuppongono che sia già stato effettuato il provisioning di un server del database SQL, che le informazioni di connessione siano disponibili e che sia stata verificata la compatibilità del database di origine.

## Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SqlPackage

Eseguire i passaggi seguenti in modo da usare l'utilità della riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database del server SQL compatibile o un database SQL di Azure da un file BACPAC.

> [AZURE.NOTE] I passaggi seguenti presuppongono che sia già stato eseguito il provisioning di un server del database SQL di Azure e che si disponga delle informazioni di connessione.

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server.
2. Eseguire il comando sqlpackage.exe riportato di seguito con gli argomenti seguenti per l'ambiente:

	`sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

	| Argomento | Descrizione |
	|---|---|
	| < server\_name > | nome server di destinazione |
	| < database\_name > | nome database di destinazione |
	| < user\_name > | nome utente nel server di destinazione |
	| < password > | password utente |
	| < source\_file > | nome e percorso del file BACPAC importato |

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## Risorse aggiuntive

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0831_2016-->