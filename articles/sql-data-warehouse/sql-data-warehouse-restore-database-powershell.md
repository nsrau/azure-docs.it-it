---
title: Ripristinare un'istanza di Azure SQL Data Warehouse (PowerShell) | Documentazione Microsoft
description: Attività di PowerShell per il ripristino di un'istanza di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6324eb11b334fd6a00e30d6f2fc6d1bec3f7a82c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60310016"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Ripristinare un'istanza di Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Panoramica][Overview]
> * [Portale][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Questo articolo illustra come ripristinare un'istanza di Azure SQL Data Warehouse usando PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.**  Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un SQL Data Warehouse, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere altre DTU, vedere come [richiedere una modifica della quota DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Installare PowerShell
Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell.  È possibile controllare la versione in uso eseguendo **Get-Module - ListAvailable-Name Az**.  Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Ripristinare un database attivo o sospeso
Per ripristinare un database da uno snapshot, usare il [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet di PowerShell.

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Specificare i punti di ripristino per il database.
5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.
6. Ripristinare il database al punto di ripristino desiderato.
7. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato
Per ripristinare un database eliminato, usare il [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet.

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database eliminato da ripristinare.
4. Specificare il database eliminato.
5. Ripristinare il database eliminato.
6. Verificare che il database ripristinato sia online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Eseguire il ripristino da un'area geografica di Azure
Per ripristinare un database, usare il [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] cmdlet.

> [!NOTE]
> È possibile eseguire un ripristino geografico al livello di prestazioni ottimizzato per il calcolo. A tale scopo, specificare un parametro ServiceObjectiveName ottimizzato per il calcolo come parametro facoltativo. 
>
> 

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino][Configure your database after recovery].
> 
> 

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, vedere [Panoramica sulla continuità aziendale del database SQL di Azure][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
