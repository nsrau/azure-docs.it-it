<properties 
    pageTitle="Ripristinare un database SQL di Azure da un backup con ridondanza geografica (PowerShell) | Microsoft Azure" 
    description="Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica" 
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

# Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell


> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-restore.md)
- [Portale di Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite PowerShell.

[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Eseguire il ripristino geografico del database in un database autonomo

1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## Eseguire il ripristino geografico del database in un pool di database elastici

1. Ottenere il backup con ridondanza geografica del database da ripristinare usando il cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino dal backup con ridondanza geografica usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx). Specificare il nome del pool in cui si vuole ripristinare il database.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## Passaggi successivi

- Per informazioni dettagliate su come ripristinare un database SQL di Azure mediante il portale di Azure da un backup con ridondanza geografica, vedere l'articolo sul [ripristino geografico nel portale di Azure](sql-database-geo-restore-portal.md).
- Per informazioni dettagliate su come ripristinare un database SQL di Azure da un backup con ridondanza geografica, vedere l'articolo sul [ripristino geografico tramite PowerShell](sql-database-geo-restore.md).
- Per una spiegazione completa su come eseguire il ripristino dopo un'interruzione del servizio, vedere l'articolo sul [ripristino dopo un'interruzione](sql-database-disaster-recovery.md).


## Risorse aggiuntive

- [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->