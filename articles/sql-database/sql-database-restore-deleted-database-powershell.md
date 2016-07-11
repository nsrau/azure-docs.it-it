<properties
	pageTitle="Ripristinare un database SQL di Azure eliminato (PowerShell) | Microsoft Azure"
	description="Ripristinare un database SQL di Azure (PowerShell)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ripristinare un database SQL di Azure con PowerShell

> [AZURE.SELECTOR]
- [Panoramica](sql-database-recovery-using-backups.md)
- [Ripristino di un database eliminato: portale di Azure](sql-database-restore-deleted-database-portal.md)

[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]


## Ripristinare il database in un database autonomo

1. Ottenere il backup del database eliminato che si vuole ripristinare usando il cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino dal backup del database eliminato usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## Ripristinare il database eliminato in un pool di database elastici

1. Ottenere il backup del database eliminato che si vuole ripristinare usando il cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Avviare il ripristino dal backup del database eliminato usando il cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## Passaggi successivi

- Per una panoramica sulla continuità aziendale, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- Per informazioni sui backup automatici del database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere [Scenari di continuità aziendale](sql-database-business-continuity-scenarios.md)
- Per altre informazioni sull'uso di backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso di backup automatici per l'archiviazione, vedere [Copia del database](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->