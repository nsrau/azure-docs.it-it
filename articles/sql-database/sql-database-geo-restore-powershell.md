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
    ms.workload="data-management" 
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Ripristinare un database SQL di Azure da un backup con ridondanza geografica tramite PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico tramite PowerShell.

Il [ripristino geografico](sql-database-geo-restore.md) consente di ripristinare un database da un backup con ridondanza geografica per crearne uno nuovo. Il database può essere creato su qualunque server in qualsiasi area di Azure. Poiché usa un backup con ridondanza geografica come origine, è possibile usarlo per ripristinare un database anche se il database è inaccessibile a causa di un'interruzione del servizio. Il ripristino geografico è abilitato automaticamente per tutti i livelli di servizio senza costi aggiuntivi.

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

- [Finalizzare il database SQL di Azure ripristinato](sql-database-recovered-finalize.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)


## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->