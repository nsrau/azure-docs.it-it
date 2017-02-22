---
title: Ripristinare un database SQL di Azure eliminato (Portale di Azure) | Microsoft Docs
description: Ripristinare un database SQL di Azure (Portale di Azure).
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
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 9ea5287884cd7f1eb7314002b3ae98c9259686c3


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Ripristinare un database SQL di Azure con il portale di Azure

## <a name="select-the-database-to-restore"></a>Selezionare il database da ripristinare
Per ripristinare un database eliminato con il Portale di Azure:

1. Nel [Portale di Azure](https://portal.azure.com) fare clic su **More services** (Altri servizi) > **SQL Server**.
2. Selezionare il server contenente il database da ripristinare.
3. Scorrere verso il basso fino alla sezione **Operazioni** nel pannello del server selezionare **Database eliminati**: ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Selezionare il database che si desidera ripristinare.
5. Specificare un nome di database e fare clic su **OK**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)




<!--HONumber=Feb17_HO3-->


