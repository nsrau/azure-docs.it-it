---
title: "MIGRAZIONE GUIDATA DATABASE SQL DI AZURE - Risoluzione dei problemi di compatibilità durante la migrazione del database SQL di Azure | Documentazione Microsoft"
description: "Questo articolo illustra i modi in cui individuare e risolvere i problemi di compatibilità dei database di SQL Server usando la Migrazione guidata database SQL di Azure prima di eseguire la migrazione al database SQL di Azure."
keywords: "Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: bba0893a-3247-406c-b155-acb4b3ef479c
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 08/24/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 006d2d5cd24bc42655bf0b3c0325531144743524


---
# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Usare la Migrazione guidata database SQL di Azure per risolvere i problemi di compatibilità dei database di SQL Server prima di eseguire la migrazione al database SQL di Azure
> [!div class="op_single_selector"]
> * Usare [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Usare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Usare [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

Questo articolo illustra come individuare e risolvere i problemi di compatibilità dei database di SQL Server usando la Migrazione guidata database SQL di Azure prima di eseguire la migrazione al database SQL di Azure.

## <a name="using-sql-azure-migration-wizard"></a>Utilizzare la migrazione guidata di SQL Azure
Usare lo strumento CodePlex [Migrazione guidata SQL Azure](http://sqlazuremw.codeplex.com/) per generare uno script T-SQL da un database di origine non compatibile. Questo script viene quindi trasformato dalla procedura guidata in modo da renderlo compatibile con il database SQL. È quindi possibile connettersi al database SQL di Azure per eseguire lo script. Questo strumento analizza anche i file di traccia per determinare i problemi di compatibilità. Lo script può essere generato solo con lo schema o può includere dati in formato BCP. Per una documentazione aggiuntiva, incluse indicazioni dettagliate, vedere la [Migrazione guidata di SQL Azure](http://sqlazuremw.codeplex.com/)su CodePlex.  

 ![Diagramma di migrazione di SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

> [!NOTE]
> Si noti che non tutti gli schemi incompatibili rilevati dalla procedura guidata possono essere risolti dalla relativa funzionalità di trasformazione incorporata. Gli script incompatibili che non possono essere corretti verranno segnalati come errori con i commenti inseriti nello script generato. Se vengono rilevati molti errori, usare Visual Studio o SQL Server Management Studio per esaminare e risolvere ogni errore che non è stato risolto mediante la migrazione guidata di SQL Server.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


