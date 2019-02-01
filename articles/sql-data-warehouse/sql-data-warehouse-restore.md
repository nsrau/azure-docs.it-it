---
title: Ripristino di Azure SQL Data Warehouse | Microsoft Docs
description: Indicazioni per il ripristino di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5de9c674ceeab6d45211b46e2f03d91b943751ac
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246212"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Eseguire il ripristino di Azure SQL Data Warehouse 
Questo articolo illustra come effettuare le operazioni seguenti nel portale di Azure e in PowerShell:

- Creare un punto di ripristino
- Eseguire il ripristino da un punto di ripristino automatico o da un punto di ripristino definito dall'utente
- Eseguire il ripristino da un database eliminato
- Eseguire il ripristino da un backup geografico
- Creare una copia del data warehouse da un punto di ripristino definito dall'utente

## <a name="before-you-begin"></a>Prima di iniziare
**Verificare la capacità in DTU.**  Ogni SQL Data Warehouse è ospitato in un server SQL (ad esempio mioserver.database.windows.net), che ha una quota DTU predefinita.  Per poter ripristinare un database SQL, verificare che la quota DTU rimanente nell'istanza del server SQL sia sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere altre DTU, vedere come [richiedere una modifica della quota DTU][Request a DTU quota change].

## <a name="restore-through-powershell"></a>Eseguire il ripristino tramite PowerShell

## <a name="install-powershell"></a>Installare PowerShell
Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0 o successiva.  Per controllare la versione usata, eseguire **Get-Module -ListAvailable -Name AzureRM**.  È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][Microsoft Web Platform Installer].  Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database-using-powershell"></a>Ripristinare un database attivo o sospeso con PowerShell
Per ripristinare un database da un punto di ripristino, usare il cmdlet di PowerShell [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Aprire Windows PowerShell.

2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.

3. Selezionare la sottoscrizione che contiene il database da ripristinare.

4. Specificare i punti di ripristino per il database.

5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.

   > [!NOTE]
   > Durante il ripristino, è possibile specificare un parametro ServiceObjectiveName diversi (DWU) o un altro server che si trova in un'area diversa.

6. Ripristinare il database al punto di ripristino desiderato.

7. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
>

## <a name="copy-your-data-warehouse-with-user-defined-restore-points-using-powershell"></a>Copiare il data warehouse con i punti di ripristino definiti dall'utente con PowerShell
Per ripristinare un database da un punto di ripristino definito dall'utente, usare il cmdlet di PowerShell [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Creare un punto di ripristino per una copia immediata del database
5. Rinominare il database con un nome temporaneo.
6. Recuperare il punto di ripristino più recente tramite il RestorePointLabel specificato.
7. Ottenere l'id della risorsa del database per avviare il ripristino
8. Ripristinare il database al punto di ripristino desiderato.
9. Verificare che il database ripristinato sia online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

## <a name="restore-a-deleted-database-using-powershell"></a>Ripristinare un database eliminato con PowerShell
Per ripristinare un database eliminato, usare il cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

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

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Al termine del ripristino sarà possibile configurare il database ripristinato seguendo le istruzioni disponibili in [Configurare il database dopo il ripristino][Configure your database after recovery].
>

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Eseguire il ripristino da un'area geografica di Azure con PowerShell
Per ripristinare un database, usare il cmdlet [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase].

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000**c**) come parametro facoltativo.
>

1. Aprire Windows PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Selezionare la sottoscrizione che contiene il database da ripristinare.
4. Selezionare il database che si desidera ripristinare.
5. Creare la richiesta di ripristino per il database.
6. Verificare lo stato del database recuperato con il ripristino geografico.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Per configurare il database al termine del ripristino, vedere [Configurare il database dopo il ripristino][Configure your database after recovery].
>

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.

## <a name="restore-through-the-azure-portal"></a>Eseguire il ripristino tramite il portale di Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Creare un il punto di ripristino definito dall'utente usando il portale di Azure
1. Accedere al [portale di Azure][Azure portal].

2. Passare al data warehouse SQL per cui si desidera creare un punto di ripristino.

3. Nella parte superiore del pannello della panoramica, selezionare **+ Nuovo punto di ripristino**.

    ![Nuovo punto di ripristino](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Specificare un nome per il punto di ripristino.

    ![Nome del punto di ripristino](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Ripristinare un database attivo o sospeso con il portale di Azure
1. Accedere al [portale di Azure][Azure portal].
2. Passare al data warehouse SQL da ripristinare e selezionarlo.
3. Nella parte superiore del pannello della panoramica, selezionare **Ripristina**.

    ![ Panoramica del servizio di ripristino](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**.

    ![Punti di ripristino automatici](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Per i punti di ripristino definiti dall'utente, **selezionare un punto di ripristino** oppure **creare un nuovo punto di ripristino definito dall'utente**.

    ![Punti di ripristino definiti dall'utente](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Ripristinare un database eliminato con il portale di Azure
1. Accedere al [portale di Azure][Azure portal].
2. Passare al server SQL che ospitava il database eliminato.
3. Selezionare l'icona dei database eliminati nel sommario.

    ![Database eliminati](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)

4. Selezionare il database eliminato che si vuole ripristinare.

    ![Selezionare i database eliminati](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)

5. Specificare un nuovo nome del database.

    ![Specificare il nome del database](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

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
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
