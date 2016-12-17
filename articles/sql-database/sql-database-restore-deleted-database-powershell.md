---
title: Ripristinare un database SQL di Azure eliminato (PowerShell) | Documentazione Microsoft
description: Ripristinare un database SQL di Azure (PowerShell).
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: ce7f0f3f-47a6-42af-b8a9-4a34bbbd8966
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fcaa796a9108700e34222eb12cc3c45d4d1b275a


---
# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>Ripristinare un database SQL di Azure con PowerShell
> [!div class="op_single_selector"]
> * [Panoramica](sql-database-recovery-using-backups.md)
> * [Ripristinare un database eliminato: portale](sql-database-restore-deleted-database-portal.md)
> * [**Ripristinare un database eliminato: PowerShell**](sql-database-restore-deleted-database-powershell.md)
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="get-a-list-of-deleted-databases"></a>Ottenere un elenco dei database eliminati
```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-standalone-database"></a>Ripristinare il database in un database autonomo
Ottenere il backup del database eliminato che si vuole ripristinare usando il cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) . Dopodiché avviare il ripristino dal backup del database eliminato usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-database-pool"></a>Ripristinare il database eliminato in un pool di database elastici
Ottenere il backup del database eliminato che si vuole ripristinare usando il cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) . Dopodiché avviare il ripristino dal backup del database eliminato usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) .

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>Passaggi successivi
* Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)
* Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
* Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
* Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)  
* Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


