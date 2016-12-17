---
title: Ripristinare un database SQL di Azure da un backup automatico (Portale di Azure) | Microsoft Docs
description: Ripristinare un database SQL di Azure da un backup automatico (portale di Azure).
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Ripristinare un database SQL di Azure da un backup automatico usando il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-recovery-using-backups.md#geo-restore)
> * [Ripristino geografico: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

Questo articolo illustra come ripristinare un database da un [backup automatico](sql-database-automated-backups.md) in un nuovo server con il [ripristino geografico](sql-database-recovery-using-backups.md#geo-restore) usando il portale di Azure.

## <a name="select-a-database-to-restore"></a>Selezionare un database da ripristinare
Per ripristinare un database nel portale di Azure, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata selezionare **+Nuovo** > **Database** > **Database SQL**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selezionare **Backup** come origine, quindi selezionare il backup da ripristinare. Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su **Crea**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Monitorare lo stato dell'operazione di ripristino facendo clic sull'icona di notifica nell'angolo superiore destro della pagina.

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


