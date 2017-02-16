---
title: Ripristinare un database SQL di Azure da un backup con ridondanza geografica | Documentazione Microsoft
description: Ripristinare un database SQL di Azure in un nuovo server da un backup con ridondanza geografica usando il portale di Azure o PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica

Questo articolo illustra come ripristinare un database in un nuovo server con il ripristino geografico. Questa operazione può essere eseguita tramite il portale di Azure o con PowerShell.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Ripristinare un database SQL di Azure da un backup con ridondanza geografica con il portale di Azure

Per eseguire il ripristino geografico di un database nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Sul lato sinistro della schermata selezionare **+Nuovo** > **Database** > **Database SQL**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selezionare **Backup** come origine, quindi selezionare il backup da ripristinare. Specificare un nome di database, un server in cui si vuole ripristinare il database e quindi fare clic su **Crea**:
   
   ![Ripristinare un database SQL di Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitorare lo stato dell'operazione di ripristino facendo clic sull'icona di notifica nell'angolo superiore destro della pagina.

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




<!--HONumber=Dec16_HO3-->


