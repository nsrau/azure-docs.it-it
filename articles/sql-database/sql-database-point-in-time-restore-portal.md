---
title: 'Portale di Azure: Ripristinare un database SQL di Azure a un momento precedente | Documentazione Microsoft'
description: Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure

Questo articolo illustra come ripristinare un database a un momento precedente usando i [backup automatici del database SQL](sql-database-automated-backups.md). tramite PowerShell. Questa attività può essere eseguita anche [tramite PowerShell](sql-database-point-in-time-restore-powershell.md).

## <a name="restore-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente 

> [!TIP]
> Per un'esercitazione, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery-portal.md)
>

Selezionare un database da ripristinare nel portale di Azure:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata fare clic su **Altri servizi** > **Database SQL**.
3. Fare clic sul database che si desidera ripristinare.
4. Nella parte superiore della pagina del database selezionare **Ripristina**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Nella pagina **Ripristina**, selezionare la data e l'ora (in formato UTC) per ripristinare il database, quindi fare clic su **OK**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Dopo aver fatto clic su **OK** nel passaggio precedente, selezionare l'icona di notifica nella parte superiore destra della pagina e fare clic sulla notifica **Ripristino del database SQL** per i dettagli.
   
    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. Verrà visualizzata la pagina Ripristino del database SQL che offre le informazioni sullo stato dell'operazione. Per maggiori dettagli, fare clic sulla voce:
   
    ![Ripristinare un database SQL di Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)


