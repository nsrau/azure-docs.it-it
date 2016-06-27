<properties 
    pageTitle="Ripristinare un database SQL di Azure a un momento precedente (PowerShell) | Microsoft Azure" 
    description="Ripristinare un database SQL di Azure a un momento precedente" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# Ripristinare un database SQL di Azure a un momento precedente con PowerShell

Questo articolo illustra come ripristinare un database a un momento precedente da [Backup automatici del database SQL](sql-database-automated-backups.md) tramite PowerShell.

[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Ripristinare il database a un momento specifico come database autonomo

1. Ottenere il database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Ripristinare il database a un momento specifico in un pool di database elastici
   
1. Ottenere il database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Passaggi successivi

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Ripristinare un database SQL di Azure a un momento precedente con il portale di Azure](sql-database-point-in-time-restore-portal.md)
- [Ripristino temporizzato tramite l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backup automatici del database SQL](sql-database-automated-backups.md)

## Risorse aggiuntive

- [Ripristino di un database eliminato](sql-database-restore-deleted-database.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->