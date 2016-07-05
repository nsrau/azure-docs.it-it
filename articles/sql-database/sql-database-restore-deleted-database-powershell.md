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
- [Panoramica](sql-database-restore-deleted-database.md)
- [Portale di Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

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

- Per informazioni dettagliate su come ripristinare un database eliminato tramite il portale di Azure, vedere l'articolo su come [ripristinare un database eliminato tramite il portale di Azure](sql-database-restore-deleted-database-portal.md).
- Per informazioni su come ripristinare un database eliminato, vedere [ripristinare un database eliminato utilizzando l'API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Per informazioni dettagliate su come ripristinare un database eliminato, vedere l'articolo su come [ripristinare un database eliminato](sql-database-restore-deleted-database.md).
- Per informazioni dettagliate sul backup automatico dei database SQL di Azure, vedere [Backup automatici del database SQL](sql-database-automated-backups.md).

## Risorse aggiuntive

- [Ripristino temporizzato](sql-database-point-in-time-restore.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->