---
title: "Determinare la compatibilità del database SQL mediante SqlPackage.exe | Documentazione Microsoft"
description: "Database SQL di Microsoft Azure, migrazione del database, compatibilità database SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: a3e43c6cb8e26daf7359f935816648bf498407ec


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinare la compatibilità del database SQL utilizzando SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

Questo articolo illustra come determinare se un database del server SQL è compatibile per eseguire la migrazione al database SQL tramite l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Utilizzo di SqlPackage.exe
1. Aprire un prompt dei comandi e passare a una directory contenente la versione più recente di sqlpackage.exe. Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direttamente dall'area download Microsoft.
2. Eseguire il comando SqlPackage riportato di seguito con gli argomenti seguenti per l'ambiente:

```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
```   
   | Argomento | Descrizione |
   | --- | --- |
   | < server_name > |nome server di origine |
   | < database_name > |nome database di origine |
   | < target_file > |nome e percorso del file BACPAC |
   | < schema_name.table_name > |tabelle per cui i dati vengono restituiti nel file di destinazione |
   | < output_file > |nome e percorso del file di output con eventuali errori |
   
    The reason for the /p:TableName argument is that we only want to test for database compatibility for export to Azure SQL DB V12 rather than export the data from all tables. Unfortunately, the export argument for sqlpackage.exe does not support extracting zero tables. You need to specify at least one table, such as a single small table. The < output_file > contains the report of any errors. The "> 2>&1" string pipes both the standard output and the standard error resulting from the command execution to specified output file.
   
    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)
3. Aprire il file di output ed esaminare gli eventuali errori di compatibilità. 
   
    ![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Passaggi successivi
* [La versione più recente di SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [La versione più recente di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Risolvere i problemi di compatibilità della migrazione del database](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


