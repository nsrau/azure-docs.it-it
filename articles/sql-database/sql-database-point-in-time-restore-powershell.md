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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Ripristinare un database SQL di Azure a un momento precedente con PowerShell

> [AZURE.SELECTOR]
- [Panoramica](sql-database-point-in-time-restore.md)
- [Portale di Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Questo articolo illustra come ripristinare un database a un momento precedente da [Backup automatici del database SQL](sql-database-automated-backups.md) tramite PowerShell.

[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Ripristinare il database a un momento specifico come database autonomo

1. Ottenere il database da ripristinare tramite il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Ripristinare il database a un momento specifico in un pool di database elastici
   
1. Ottenere il database da ripristinare tramite il cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Ripristinare il database a un momento specifico tramite il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Passaggi successivi

- Per istruzioni dettagliate su come eseguire il ripristino temporizzato nel portale di Azure, vedere l'articolo sul [ripristino temporizzato nel portale di Azure](sql-database-point-in-time-restore-portal.md).
- Per informazioni su come eseguire il ripristino temporizzato tramite l'API REST, vedere l'articolo sul [ripristino temporizzato mediante l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Per una panoramica del ripristino temporizzato, vedere l'articolo sul [ripristino temporizzato](sql-database-point-in-time-restore.md).
- Per una spiegazione completa su come eseguire il ripristino dall'errore di un utente o di un'applicazione, vedere l'articolo sul [ripristino da errori dell'utente](sql-database-user-error-recovery.md).

## Risorse aggiuntive

- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->