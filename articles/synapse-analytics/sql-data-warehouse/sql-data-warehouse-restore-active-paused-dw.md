---
title: Ripristinare un data warehouse esistente
description: Guida alle procedure per il ripristino di un pool SQL esistente.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6fa8bd42eb067124ab6ea1db77e2f3d6fba79638
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745222"
---
# <a name="restore-an-existing-sql-pool"></a>Ripristinare un pool SQL esistente

Questo articolo illustra come ripristinare un pool SQL esistente in Azure sinapsi Analytics usando portale di Azure e PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool è ospitato da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Prima di iniziare

1. Assicurarsi di [installare Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Disporre di un punto di ripristino esistente da cui si desidera eseguire il ripristino. Se si desidera creare un nuovo ripristino, vedere [l'esercitazione per la creazione di un nuovo punto di ripristino definito dall'utente](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Ripristinare un pool SQL esistente tramite PowerShell

Per ripristinare un pool SQL esistente da un punto di ripristino, usare il cmdlet di PowerShell [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

1. Aprire PowerShell.

2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.

3. Selezionare la sottoscrizione che contiene il database da ripristinare.

4. Elencare i punti di ripristino per il pool SQL.

5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.

6. Ripristinare il pool SQL al punto di ripristino desiderato usando il cmdlet [di PowerShell Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .
        1. Per ripristinare il pool SQL in un server logico diverso, assicurarsi di specificare l'altro nome del server logico.  Questo server logico può trovarsi anche in un gruppo di risorse e in un'area diversi.
        2. Per eseguire il ripristino in una sottoscrizione diversa, usare il pulsante "Sposta" per spostare il server logico in un'altra sottoscrizione.

7. Verificare che il pool SQL ripristinato sia online.

8. Al termine del ripristino, è possibile configurare il pool SQL ripristinato seguendo la procedura di [configurazione del database dopo il ripristino](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Ripristinare un pool SQL esistente tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al pool SQL da cui si desidera eseguire il ripristino.
3. Nella parte superiore del pannello della panoramica, selezionare **Ripristina**.

    ![ Panoramica del servizio di ripristino](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. Se il pool SQL non contiene punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima del ripristino. Per i punti di ripristino definiti dall'utente, selezionarne uno esistente o crearne uno nuovo. Per **Server**è possibile selezionare un server logico in un gruppo di risorse e un'area diversi o crearne uno nuovo. Dopo aver fornito tutti i parametri, fare clic su **Verifica + Ripristina**.

    ![Punti di ripristino automatici](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL eliminato](sql-data-warehouse-restore-deleted-dw.md)
- [Ripristinare da un pool SQL di backup geografico](sql-data-warehouse-restore-from-geo-backup.md)
