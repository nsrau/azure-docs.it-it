---
title: 'Portale di Azure: ripristinare un database SQL di Azure | Microsoft Docs'
description: Ripristinare un database SQL di Azure usando il portale di Azure.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Ripristinare un database SQL di Azure usando il portale di Azure

La procedura seguente illustra come ripristinare un database SQL di Azure rispetto a un momento specifico, da un database eliminato e da un backup con ridondanza geografica.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Ripristinare un database SQL di Azure a un momento precedente 

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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>Ripristinare da un backup un database SQL di Azure eliminato
Per ripristinare un database eliminato con il Portale di Azure:

1. Nel [Portale di Azure](https://portal.azure.com) fare clic su **More services** (Altri servizi) > **SQL Server**.
2. Selezionare il server contenente il database da ripristinare.
3. Scorrere verso il basso fino alla sezione **Operazioni** nel pannello del server selezionare **Database eliminati**: ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Selezionare il database che si desidera ripristinare.
5. Specificare un nome di database e fare clic su **OK**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica

Per eseguire il ripristino geografico di un database nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata selezionare **+Nuovo** > **Database** > **Database SQL**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selezionare **Backup** come origine, quindi selezionare il backup da ripristinare. Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su **Crea**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitorare lo stato dell'operazione di ripristino facendo clic sull'icona di notifica nell'angolo superiore destro della pagina.

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)


