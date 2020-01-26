---
title: Ripristinare un data warehouse esistente
description: Guida alle procedure per il ripristino di un Azure SQL Data Warehouse esistente.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759687"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Ripristinare un Azure SQL Data Warehouse esistente

Questo articolo illustra come ripristinare un SQL Data Warehouse esistente tramite portale di Azure e PowerShell:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitata da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Prima di iniziare

1. Assicurarsi di [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Disporre di un punto di ripristino esistente da cui si desidera eseguire il ripristino. Se si desidera creare un nuovo ripristino, vedere [l'esercitazione per la creazione di un nuovo punto di ripristino definito dall'utente](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Ripristinare un data warehouse esistente tramite PowerShell

Per ripristinare un data warehouse esistente da un punto di ripristino, usare il cmdlet di PowerShell [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

1. Aprire PowerShell.

2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.

3. Selezionare la sottoscrizione che contiene il database da ripristinare.

4. Elencare i punti di ripristino per la data warehouse.

5. Selezionare il punto di ripristino desiderato utilizzando RestorePointCreationDate.

6. Ripristinare il data warehouse al punto di ripristino desiderato usando il cmdlet [di PowerShell Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .
        1. Per ripristinare il SQL Data Warehouse a un server logico diverso, assicurarsi di specificare l'altro nome del server logico.  Questo server logico può trovarsi anche in un gruppo di risorse e in un'area diversi.
        2. Per eseguire il ripristino in una sottoscrizione diversa, usare il pulsante "Sposta" per spostare il server logico in un'altra sottoscrizione.

7. Verificare che il data warehouse ripristinato sia online.

8. Al termine del ripristino, è possibile configurare il data warehouse ripristinato seguendo la procedura di [configurazione del database dopo il ripristino](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Ripristinare un data warehouse esistente tramite l'portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al SQL Data Warehouse da cui si desidera eseguire il ripristino.
3. Nella parte superiore del pannello della panoramica, selezionare **Ripristina**.

    ![ Panoramica del servizio di ripristino](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selezionare **Punto di ripristino automatico** o **Punti di ripristino definiti dall'utente**. Se il data warehouse non dispone di punti di ripristino automatici, attendere alcune ore o creare un punto di ripristino definito dall'utente prima del ripristino. Per i punti di ripristino definiti dall'utente, selezionarne uno esistente o crearne uno nuovo. Per **Server**è possibile selezionare un server logico in un gruppo di risorse e un'area diversi o crearne uno nuovo. Dopo aver fornito tutti i parametri, fare clic su **Verifica + Ripristina**.

    ![Punti di ripristino automatici](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Fasi successive
- [Ripristinare un data warehouse eliminato](sql-data-warehouse-restore-deleted-dw.md)
- [Eseguire il ripristino da un data warehouse di backup geografico](sql-data-warehouse-restore-from-geo-backup.md)

 