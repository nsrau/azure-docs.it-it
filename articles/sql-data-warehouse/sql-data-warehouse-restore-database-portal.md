---
title: Ripristinare un&quot;istanza di Azure SQL Data Warehouse (portale) | Microsoft Docs
description: "Attività del portale di Azure per il ripristino di un&quot;istanza di Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/21/2016
ms.author: lakshmir;barbkess;sonyama
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cb8cb2b58df5cc209b1f966c792ca0f4082e652


---
# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Ripristinare un'istanza di Azure SQL Data Warehouse (portale)
> [!div class="op_single_selector"]
> * [Panoramica][Panoramica]
> * [Portale][Portale]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse tramite il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.**  Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la quota di DTU necessaria o per richiedere altri DTU, vedere [richiedere una modifica della quota DTU][richiedere una modifica della quota DTU].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso
Per ripristinare un database:

1. Accedere al [portale di Azure][portale di Azure]
2. Sul lato sinistro della schermata fare clic su **Sfoglia**, quindi su **Server SQL**
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Individuare e selezionare il server.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Individuare l'istanza di SQL Data Warehouse da ripristinare e selezionarla.
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Nella parte superiore del pannello del data warehouse fare clic su **Ripristina**
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Specificare un nuovo **Nome database**
7. Selezionare il **punto di ripristino**
   
   1. Assicurarsi di selezionare il punto di ripristino più recente.  Poiché i punti di ripristino sono visualizzati secondo il formato UTC, l'opzione predefinita visualizzata potrebbe non essere il punto di ripristino più recente.
      
      ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Fare clic su **OK**
9. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**

> [!NOTE]
> Dopo aver completato il ripristino, è possibile configurare il database ripristinato seguendo [Configurare il database dopo il ripristino][Configurare il database dopo il ripristino].
> 
> 

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Per ripristinare un database eliminato:

1. Accedere al [portale di Azure][portale di Azure]
2. Sul lato sinistro della schermata fare clic su **Sfoglia**, quindi su **Server SQL**
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Individuare e selezionare il server.
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Scorrere fino alla sezione Operazioni nel pannello del server.
5. Fare clic sul riquadro **Database eliminati** .
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Fare clic sul database eliminato che si desidera ripristinare.
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Specificare un nuovo **Nome database**
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Fare clic su **OK**
9. Viene avviato il processo di ripristino del database che sarà possibile monitorare tramite **NOTIFICHE**

> [!NOTE]
> Per configurare il database dopo aver completato il ripristino, vedere [Configurare il database dopo il ripristino][Configurare il database dopo il ripristino].
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulle funzionalità di continuità aziendale delle edizioni di database SQL di Azure, leggere [Panoramica sulla continuità aziendale del database SQL][Panoramica sulla continuità aziendale del database SQL].

<!--Image references-->

<!--Article references-->
[Panoramica sulla continuità aziendale del database SQL]: ../sql-database/sql-database-business-continuity.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurare il database dopo il ripristino]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[richiedere una modifica della quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[portale di Azure]: https://portal.azure.com/



<!--HONumber=Nov16_HO3-->


