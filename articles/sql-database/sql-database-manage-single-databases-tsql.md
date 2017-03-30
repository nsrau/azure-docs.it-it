---
title: 'T-SQL: creare e gestire database SQL singoli di Azure | Documentazione Microsoft'
description: Informazioni di riferimento rapido sulla creazione e gestione di database SQL singoli di Azure con il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 951c88124219a6962f655b91275aec64c716b1cd
ms.lasthandoff: 03/28/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-transact-sql"></a>Creare e gestire database SQL singoli di Azure con Transact-SQL

È possibile creare e gestire database SQL singoli di Azure usando il [portale di Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l'API REST o C#. Questo argomento illustra l'uso del portale di Azure. Per PowerShell, vedere [Creare e gestire database singoli con PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). Per Transact-SQL, vedere [Creare e gestire database singoli con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>Creare un database SQL di Azure usando Transact-SQL in SQL Server Management Studio

Per creare un database SQL usando Transact-SQL in SQL Server Management Studio:

1. Da SQL Server Management Studio connettersi al server di database di Azure usando l'account di accesso dell'entità di sicurezza a livello di server o un account di accesso membro del ruolo **dbmanager**. Per altre informazioni sugli account di accesso, vedere [Gestire gli account di accesso](sql-database-manage-logins.md).
2. In Esplora oggetti aprire il nodo Database, espandere la cartella **Database di sistema**, fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.
3. Per creare un database, usare l'istruzione **CREATE DATABASE** . Per altre informazioni, vedere [CREATE DATABASE (database SQL)](https://msdn.microsoft.com/library/dn268335.aspx). La seguente istruzione consente di creare un database denominato **myTestDB** specificando che si tratta di un database Standard S0 Edition con una dimensione massima di 250 GB.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. Fare clic su **Execute** per eseguire la query.
5. In Esplora oggetti fare clic con il pulsante destro del mouse sul nodo Database e scegliere **Aggiorna** per visualizzare il nuovo database in Esplora oggetti. 


## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modificare il livello di servizio e il livello delle prestazioni di un database singolo
Modificare le dimensioni massime del database tramite [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx)

> [!TIP]
> Per un'esercitazione sulla creazione di un database con Transact-SQL, vedere [Creare un database - Portale di Azure](sql-database-get-started.md).
>

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica degli strumenti di gestione, vedere [Panoramica degli strumenti di gestione](sql-database-manage-overview.md).
* Per informazioni su come eseguire le attività di gestione usando il portale di Azure, vedere [Gestire database SQL di Azure mediante il portale di Azure](sql-database-manage-portal.md).
* Per informazioni su come eseguire le attività di gestione usando PowerShell, vedere [Manage Azure SQL Databases using PowerShell](sql-database-manage-powershell.md) (Gestire database SQL di Azure usando PowerShell).
* Per informazioni su come eseguire le attività di gestione usando SQL Server Management Studio, vedere [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Per informazioni sul servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). 
* Per informazioni sui server di database di Azure e sulle funzionalità di database, vedere [Funzionalità](sql-database-features.md).

