---
title: Ripristinare un database SQL di Azure a un momento precedente | Documentazione Microsoft
description: Ripristinare un database SQL di Azure a un momento precedente
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
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Ripristinare un database SQL di Azure a un momento precedente 

Questo articolo sulle procedure illustra come ripristinare un database a un momento precedente usando i [backup automatici del database SQL](sql-database-automated-backups.md). 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Ripristinare uno stato precedente con il portale di Azure

> [!TIP]
> Per un'esercitazione, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery.md)
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

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Ripristinare uno stato precedente con PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Ripristinare il database a un momento specifico come database singolo
1. Ottenere il database da ripristinare tramite il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Ripristinare il database a un momento specifico in un pool elastico
1. Ottenere il database da ripristinare tramite il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per visualizzare il punto di ripristino meno recente dai backup di un database generati dal servizio, vedere [View oldest restore point](sql-database-view-oldest-restore-point.md) (Visualizzare il punto di ripristino meno recente)
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


