---
title: 'Portale di Azure: Ripristinare un database SQL da un backup con ridondanza geografica | Documentazione Microsoft'
description: Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica usando il portale di Azure
services: sql-database
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica con il portale di Azure

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite il portale di Azure. Questa attività può essere eseguita anche [tramite PowerShell](sql-database-geo-restore-powershell.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica con il portale di Azure

Per eseguire il ripristino geografico di un database nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata selezionare **+Nuovo** > **Database** > **Database SQL**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selezionare **Backup** come origine, quindi selezionare il backup da ripristinare. Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su **Crea**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitorare lo stato dell'operazione di ripristino facendo clic sull'icona di notifica nell'angolo superiore destro della pagina.


## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per informazioni su opzioni di ripristino più veloci, vedere l'articolo relativo alla [replica geografica attiva](sql-database-geo-replication-overview.md).  
* Per informazioni sull'uso dei backup automatici per l'archiviazione, vedere l'articolo relativo alla [copia di database](sql-database-copy.md).


