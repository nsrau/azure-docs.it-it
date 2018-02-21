---
title: Ripristinare Azure SQL Data Warehouse (portale di Azure) | Documentazione Microsoft
description: "Attività del portale di Azure per il ripristino di Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 6be187784a68174b63f7fc068c772826a0db52c6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Ripristinare Azure SQL Data Warehouse (portale)
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Portale][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
Questo articolo illustra come ripristinare Azure SQL Data Warehouse usando il portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.** Ogni istanza di SQL Data Warehouse è ospitata in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota di unità elaborate di dati (DTU) predefinita. Per poter ripristinare SQL Data Warehouse, verificare che la quota DTU rimanente nel server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la quota DTU o per richiedere altre DTU, vedere [Richiedere una modifica della quota DTU][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso
Per ripristinare un database:

1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro sinistro fare clic su **Esplora** e quindi su **Server SQL**.

    ![Selezionare Esplora > SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Trovare il server e selezionarlo.

    ![Selezionare il server](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Trovare l'istanza di SQL Data Warehouse da ripristinare e selezionarla.

    ![Selezionare l'istanza di SQL Data Warehouse da ripristinare](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Nella parte superiore del pannello del data warehouse selezionare **Ripristina**.

    ![Selezionare Ripristina](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Specificare un nuovo **nome database**.
7. Selezionare il **punto di ripristino** più recente.

   Assicurarsi di selezionare il punto di ripristino più recente. Poiché i punti di ripristino vengono visualizzati in formato UTC (Coordinated Universal Time), l'opzione predefinita potrebbe non corrispondere al punto di ripristino più recente.

      ![Selezionare un punto di ripristino](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Selezionare **OK**.
9. Viene avviato il processo di ripristino del database che sarà possibile monitorare usando le **NOTIFICHE**.

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Per ripristinare un database eliminato:

1. Accedere al [portale di Azure][Azure portal].
2. Nel riquadro sinistro fare clic su **Esplora** e quindi su **Server SQL**.

    ![Selezionare Esplora > SQL Server](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Trovare il server e selezionarlo.

    ![Selezionare il server](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Scorrere fino alla sezione **Operazioni** nel pannello del server.
5. Selezionare il riquadro **Database eliminati**.

    ![Selezionare il riquadro Database eliminati](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Selezionare il database eliminato che si vuole ripristinare.

    ![Selezionare un database da ripristinare](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Specificare un nuovo **nome database**.

    ![Aggiungere un nome per il database](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Selezionare **OK**.
9. Viene avviato il processo di ripristino del database che sarà possibile monitorare usando le **NOTIFICHE**.

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, vedere [Panoramica sulla continuità aziendale del database SQL][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
