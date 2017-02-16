---
title: "SSMS - Risoluzione dei problemi di compatibilità durante la migrazione del database SQL di Azure | Documentazione Microsoft"
description: "Questo articolo illustra come risolvere i problemi di incompatibilità del database SQL Server usando SQL Server Management Studio prima della migrazione al database SQL di Azure."
keywords: "Database SQL di Microsoft Azure, migrazione del database, compatibilità, migrazione guidata di SQL Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5f7d3544-b07e-415a-a2ae-96e49bf5d756
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 841c4d644c6b096251e1ecefac17a8254b5ca712


---
# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Risolvere i problemi di compatibilità del database del server SQL, usando SQL Server Management Studio, prima di effettuare la migrazione al database SQL
> [!div class="op_single_selector"]
> * Usare [MIGRAZIONE GUIDATA DATABASE SQL DI AZURE](sql-database-cloud-migrate-fix-compatibility-issues.md)
> * Usare [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * Usare [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
>  

Gli utenti esperti possono risolvere i problemi di compatibilità del database di SQL Server usando SQL Server Management Studio prima di eseguire la migrazione al database SQL di Azure.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="using-sql-server-management-studio"></a>Utilizzare SQL Server Management Studio
Utilizzare SQL Server Management Studio per risolvere i problemi di compatibilità usando alcuni comandi Transact-SQL, ad esempio **ALTER DATABASE**. Questo metodo è destinato principalmente a utenti esperti che non hanno difficoltà a utilizzare Transact-SQL in un database attivo. In caso contrario, è consigliabile usare SSDT. 

## <a name="next-steps"></a>Passaggi successivi
* [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
* [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
* [Eseguire la migrazione di un database di SQL Server compatibile nel database SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Funzionalità di database SQL](sql-database-features.md)
* [Transact-SQL partially or unsupported functions (Funzionalità di Transact-SQL parzialmente supportate o non supportate)](sql-database-transact-sql-information.md)
* [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


