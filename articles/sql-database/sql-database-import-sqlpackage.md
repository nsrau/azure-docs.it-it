---
title: SqlPackage - Importazione in un database SQL di Azure da un file BACPAC | Documentazione Microsoft
description: "Questo articolo illustra come importare dati in un database SQL da un file BACPAC usando l&quot;utilità da riga di comando SqlPackage."
keywords: Database SQL di Microsoft Azure, migrazione del database, importazione del database, importazione del file BACPAC, sqlpackage
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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>Importare un database da un file BACPAC nel database SQL di Azure mediante SqlPackage

Questo articolo illustra come importare dati in un database SQL Server da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando l'utilità della riga di comando [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Questa utilità viene fornita con le versioni più recenti di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools per Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), oppure è possibile scaricare la versione più recente di [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) direttamente dall'area download Microsoft.

> [!NOTE]
> I passaggi seguenti presuppongono che sia già stato effettuato il provisioning di un server del database SQL, che le informazioni di connessione siano disponibili e che sia stata verificata la compatibilità del database di origine.
> 
> 

## <a name="import-the-database"></a>Importare il database
Usare l'utilità della riga di comando [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) per importare un database SQL Server compatibile o un database SQL di Azure da un file BACPAC.

> [!NOTE]
> I passaggi seguenti presuppongono che sia già stato eseguito il provisioning di un server del database SQL di Azure e che si disponga delle informazioni di connessione.
>  

Al prompt dei comandi nella directory contenente la versione più recente dell'utilità della riga di comando sqlpackage.exe, eseguire un comando simile all'esempio seguente che importa un file BACPAC in un database di SQL Azure come Premium P11.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione dell'intero processo di migrazione del database SQL Server, tra cui raccomandazioni sulle prestazioni, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
* Per contenuti di riferimento su SQLPackage, vedere [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).
* Per scaricare la versione più recente di SQLPackage, vedere [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876).
* Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.




<!--HONumber=Feb17_HO2-->


