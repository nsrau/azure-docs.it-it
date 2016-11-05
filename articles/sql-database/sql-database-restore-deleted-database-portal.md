---
title: Ripristinare un database SQL di Azure eliminato (Portale di Azure) | Microsoft Docs
description: Ripristinare un database SQL di Azure (Portale di Azure)
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Ripristinare un database SQL di Azure con il portale di Azure
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-recovery-using-backups.md)
> * [Ripristino di un database eliminato: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## Selezionare il database da ripristinare
Per ripristinare un database nel portale di Azure, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata fare clic su **SFOGLIA** > **Server SQL**.
3. Passare al server con il database eliminato che si vuole ripristinare e selezionare il server
4. Scorrere verso il basso fino alla sezione **Operazioni** nel pannello del server selezionare **Database eliminati**: ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. Fare clic sul database eliminato che si desidera ripristinare.
6. Specificare un nome di database e fare clic su OK:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->