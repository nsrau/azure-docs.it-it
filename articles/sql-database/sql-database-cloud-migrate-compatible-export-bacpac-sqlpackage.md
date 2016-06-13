<properties
   pageTitle="Esportare un database SQL Server in un file BACPAC mediante SqlPackage"
   description="Database SQL di Microsoft Azure, migrazione del database, esportazione del database, esportazione del file BACPAC, sqlpackage"
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
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Esportare un database SQL Server in un file BACPAC mediante SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

In questo articolo viene illustrato come esportare un database SQL Server in un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizzando l’utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità è disponibile sia in Visual Studio che in SQL Server. È inoltre possibile [scaricare](https://msdn.microsoft.com/library/mt204009.aspx) la versione più recente di SQL Server Data Tools per ottenere questa utilità.

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server. Utilizzare la ricerca sul computer per trovare il percorso nell'ambiente in uso.
2. Eseguire il comando sqlpackage.exe riportato di seguito con gli argomenti seguenti per l'ambiente:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argomento | Descrizione |
	|---|---|
	| < server_name > | nome server di origine |
	| < database_name > | nome database di origine |
	| < target_file > | nome e percorso del file BACPAC |

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importare un file BACPAC nel database SQL di Azure tramite SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importare un file BACPAC nel database SQL di Azure tramite SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importare un file BACPAC nel database SQL di Azure tramite il portale di Azure](sql-database-import.md)
- [Importare un file BACPAC nel database SQL di Azure tramite PowerShell](sql-database-import-powershell.md)

## Altre informazioni

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)

<!---HONumber=AcomDC_0601_2016-->