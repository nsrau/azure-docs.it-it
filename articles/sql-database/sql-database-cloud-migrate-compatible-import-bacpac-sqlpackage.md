---
title: Importare nel database SQL da un file BACPAC
description: Database SQL di Microsoft Azure, migrazione del database, importazione del database, importazione del file BACPAC, sqlpackage
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: d1f701bc5e4028db4d97ac2e7097afedf46b1f1f


---
# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importare nel database SQL da un file BACPAC
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portale di Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

Questo articolo illustra come importare dati in un database SQL Server da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

> [!NOTE]
> I passaggi seguenti presuppongono che sia già stato effettuato il provisioning di un server del database SQL, che le informazioni di connessione siano disponibili e che sia stata verificata la compatibilità del database di origine.
> 
> 

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Eseguire l'importazione da un file BACPAC al database SQL di Azure mediante SqlPackage
Eseguire i passaggi seguenti in modo da usare l'utilità della riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database del server SQL compatibile o un database SQL di Azure da un file BACPAC.

> [!NOTE]
> I passaggi seguenti presuppongono che sia già stato eseguito il provisioning di un server del database SQL di Azure e che si disponga delle informazioni di connessione.
> 
> 

1. Aprire un prompt dei comandi e passare a una directory contenente l'utilità da riga di comando sqlpackage.exe, disponibile in Visual Studio e SQL Server.
2. Eseguire il comando sqlpackage.exe riportato di seguito con gli argomenti seguenti per l'ambiente:
   
    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`
   
   | Argomento | Descrizione |
   | --- | --- |
   | < server_name > |nome server di destinazione |
   | < database_name > |nome database di destinazione |
   | < user_name > |nome utente nel server di destinazione |
   | < password > |password utente |
   | < source_file > |nome e percorso del file BACPAC importato |
   
    ![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


