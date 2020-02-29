---
title: Ripristinare un pool SQL eliminato
description: Guida per il ripristino di un pool SQL eliminato.
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
ms.openlocfilehash: 34851203432b7e2daf44e840e45275de76bc3b3a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196630"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Ripristinare un pool SQL eliminato usando Azure sinapsi Analytics

In questo articolo si apprenderà come ripristinare un'operazione SQL usando il portale di Azure o PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool SQL è ospitato da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita.  Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Ripristinare un data warehouse eliminato tramite PowerShell

Per ripristinare un pool SQL eliminato, usare il cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) . Se anche il server logico corrispondente è stato eliminato, non è possibile ripristinare tale data warehouse.

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Consente di selezionare la sottoscrizione che contiene il data warehouse eliminato da ripristinare.
5. Ottenere il data warehouse eliminato specifico.
6. Ripristinare il data warehouse eliminato
    1. Per ripristinare il SQL Data Warehouse eliminato in un server logico diverso, assicurarsi di specificare l'altro nome del server logico.  Questo server logico può trovarsi anche in un gruppo di risorse e in un'area diversi.
    1. Per eseguire il ripristino in un'altra sottoscrizione, utilizzare il pulsante [Sposta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal) per spostare il server logico in un'altra sottoscrizione.
1. Verificare che il data warehouse ripristinato sia online.
1. Al termine del ripristino, è possibile configurare il data warehouse ripristinato seguendo la procedura di [configurazione del database dopo il ripristino](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Ripristinare un database eliminato con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al server SQL in cui è ospitato il data warehouse eliminato.
3. Selezionare l'icona **database eliminati** nel sommario.

    ![Database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selezionare il SQL Data Warehouse eliminato che si desidera ripristinare.

    ![Selezionare i database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Specificare un nuovo **nome di database** e fare clic su **OK**

    ![Specificare il nome del database](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Passaggi successivi
- [Ripristinare un pool SQL esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare da un pool SQL di backup geografico](sql-data-warehouse-restore-from-geo-backup.md)