---
title: Ripristinare un database SQL di Azure a un momento precedente (portale di Azure) | Documentazione Microsoft
description: Ripristinare un database SQL di Azure a un momento precedente.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-recovery-using-backups.md)
> * [Ripristino temporizzato: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

Questo articolo illustra come ripristinare un database a un momento precedente usando i [backup automatici del database SQL](sql-database-automated-backups.md) tramite il portale di Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Ripristinare un database SQL a un momento precedente
Selezionare un database da ripristinare nel portale di Azure:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata fare clic su **Altri servizi** > **Database SQL**.
3. Fare clic sul database che si desidera ripristinare.
4. Nella parte superiore della pagina del database selezionare **Ripristina**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Nella pagina **Ripristina**, selezionare la data e l'ora (in formato UTC) per ripristinare il database, quindi fare clic su **OK**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Monitorare l'operazione di ripristino
1. Dopo aver fatto clic su **OK** nel passaggio precedente, selezionare l'icona di notifica nella parte superiore destra della pagina e fare clic sulla notifica **Ripristino del database SQL** per i dettagli.
   
    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. Verrà visualizzata la pagina Ripristino del database SQL che offre le informazioni sullo stato dell'operazione. Per maggiori dettagli, fare clic sulla voce:
   
    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


