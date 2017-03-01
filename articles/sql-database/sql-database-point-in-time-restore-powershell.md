---
title: 'PowerShell: Ripristinare un database SQL di Azure a un momento precedente | Documentazione Microsoft'
description: Ripristinare un database SQL di Azure a un momento precedente con PowerShell
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
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 5e5f0a474b6a34581be804b5d18d6ae03c99c14d
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Ripristinare un database SQL di Azure a un momento precedente con PowerShell

Questo articolo illustra come ripristinare un database a un momento precedente usando i [backup automatici del database SQL](sql-database-automated-backups.md) tramite PowerShell. Questa attività può anche essere eseguita [tramite il portale di Azure](sql-database-point-in-time-restore-portal.md).  

## <a name="restore-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente 

> [!TIP]
> Per un'esercitazione, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino](sql-database-get-started-backup-recovery-powershell.md)
>


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
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)


