---
title: Ripristinare un database SQL di Azure da un backup con ridondanza geografica (PowerShell) | Microsoft Docs
description: Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4fe8bc103856f891d3b29fa77f5c43dd0c403e8f


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-recovery-using-backups.md)
> * [Ripristino geografico: portale di Azure](sql-database-geo-restore-portal.md)
> 
> 

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico. Questa operazione può essere eseguita tramite PowerShell.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Eseguire il ripristino geografico del database in un database autonomo
1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Eseguire il ripristino geografico del database in un pool di database elastici
1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Specificare il nome del pool in cui si vuole ripristinare il database.
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md).
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md).
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md).  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md).




<!--HONumber=Nov16_HO3-->


