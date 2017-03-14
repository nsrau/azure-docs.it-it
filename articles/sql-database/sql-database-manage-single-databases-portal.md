---
title: 'Portale di Azure: creare e gestire database SQL singoli di Azure | Documentazione Microsoft'
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
ms.sourcegitcommit: 5e01db2676e3515f41bf98d23595e6509c0d6805
ms.openlocfilehash: c5f50213be9cd20c82acf8dd94463e7dce0a0195
ms.lasthandoff: 02/27/2017


---
# <a name="create-and-manage-single-azure-sql-databases-with-the-azure-portal"></a>Creare e gestire database SQL singoli di Azure con il portale di Azure

È possibile creare e gestire database SQL singoli di Azure usando il [portale di Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l'API REST o C#. Questo argomento illustra l'uso del portale di Azure. Per PowerShell, vedere [Creare e gestire database singoli con PowerShell](sql-database-manage-single-databases-powershell.md). Per Transact-SQL, vedere [Creare e gestire database singoli con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

## <a name="create-a-single-azure-sql-database-with-the-azure-portal"></a>Creare un database SQL singolo di Azure con il portale di Azure

1. Aprire il pannello **Database SQL** nel [portale di Azure](https://portal.azure.com/). 

    ![database sql](./media/sql-database-get-started/sql-databases.png)
2. Nel pannello dei database SQL fare clic su **Aggiungi**.

    ![aggiungi database sql](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Per un'esercitazione sulla creazione di un database con il portale di Azure, vedere [Creare un database - Portale di Azure](sql-database-get-started.md).
>    

## <a name="view-and-update-sql-database-settings-using-the-azure-portal"></a>Visualizzare e aggiornare le impostazioni del database SQL tramite il portale di Azure

1. Aprire il pannello **Database SQL** nel [portale di Azure](https://portal.azure.com/). 

    ![database sql](./media/sql-database-get-started/sql-databases.png)

2. Fare clic sul database da usare e quindi scegliere l'impostazione desiderata nel pannello di database SQL.

    ![nuovo pannello del database di esempio](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modificare il livello di servizio e il livello delle prestazioni di un database singolo

Aprire il pannello del Database SQL per il database che si desidera scalare verso l’alto o verso il basso:

1. Nel [Portale di Azure](https://portal.azure.com) fare clic su **More services** (Altri servizi) > **Database SQL**.
2. Fare clic sul database che si desidera modificare.
3. Nel pannello **Database SQL** fare clic su **Piano tariffario (piano DTU)**:
   
   ![piano tariffario](./media/sql-database-manage-single-database-portal/new-tier.png)

4. Scegliere un nuovo piano tariffario e fare clic su **Seleziona**:
   
   Facendo clic su **Seleziona** viene inviata una richiesta di ridimensionamento per modificare il piano tariffario. A seconda delle dimensioni del database, l'operazione di ridimensionamento potrebbe richiedere diverso tempo (vedere [Livelli di servizio](sql-database-service-tiers.md)).
   
   > [!NOTE]
   > La modifica del piano tariffario del database non modifica le dimensioni massime del database. Per modificare le dimensioni massime del database usare [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   >  
   
   ![selezionare un livello di prezzo](./media/sql-database-manage-single-database-portal/choose-tier.png)
5. Fare clic sull'icona di notifica (a forma di campanello) in alto a destra:
   
   ![Notifiche](./media/sql-database-manage-single-database-portal/scale-notification.png)
   
6. Fare clic sul testo della notifica per aprire il riquadro dei dettagli in cui è possibile visualizzare lo stato della richiesta.

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica degli strumenti di gestione, vedere [Panoramica degli strumenti di gestione](sql-database-manage-overview.md).
* Per informazioni su come eseguire le attività di gestione usando il portale di Azure, vedere [Gestire database SQL di Azure mediante il portale di Azure](sql-database-manage-portal.md).
* Per informazioni su come eseguire le attività di gestione usando PowerShell, vedere [Manage Azure SQL Databases using PowerShell](sql-database-manage-powershell.md) (Gestire database SQL di Azure usando PowerShell).
* Per informazioni su come eseguire le attività di gestione usando SQL Server Management Studio, vedere [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Per informazioni sul servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). 
* Per informazioni sui server di database di Azure e sulle funzionalità di database, vedere [Funzionalità](sql-database-features.md).

