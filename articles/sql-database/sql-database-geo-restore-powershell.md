---
title: 'PowerShell: Ripristinare un database SQL di Azure da un backup con ridondanza geografica | Documentazione Microsoft'
description: Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica usando PowerShell
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
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 65ae5784e2d4cadc9e5dc348d9bb0d696a5f4908
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-powershell"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell

Questo articolo illustra come ripristinare un database in un nuovo server tramite ripristino geografico con PowerShell. Tuttavia, questa attività può anche essere eseguita [tramite il portale di Azure](sql-database-geo-restore-portal.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>Eseguire il ripristino geografico del database in un database autonomo

1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>Eseguire il ripristino geografico del database in un pool elastico

1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Specificare il nome del pool in cui si vuole ripristinare il database.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per informazioni su opzioni di ripristino più veloci, vedere l'articolo relativo alla [replica geografica attiva](sql-database-geo-replication-overview.md).  
* Per informazioni sull'uso dei backup automatici per l'archiviazione, vedere l'articolo relativo alla [copia di database](sql-database-copy.md).


