---
title: Ripristinare un pool SQL eliminato
description: Guida per il ripristino di un pool SQL eliminato.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 879844efdc5c2b40f69ee5f79305d4dfa596fd27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460729"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Ripristinare un pool SQL eliminato usando Azure sinapsi Analytics

In questo articolo si apprenderà come ripristinare un'operazione SQL usando il portale di Azure o PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool SQL è ospitato da un [server SQL logico](../../azure-sql/database/logical-servers.md) (ad esempio, myserver.database.Windows.NET) con una quota DTU predefinita.  Verificare che il server disponga di una quota di DTU residua sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Ripristinare un data warehouse eliminato tramite PowerShell

Per ripristinare un pool SQL eliminato, usare il cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Se anche il server corrispondente è stato eliminato, non è possibile ripristinare tale data warehouse.

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Selezionare la sottoscrizione che contiene il pool SQL eliminato da ripristinare.
5. Ottenere il data warehouse eliminato specifico.
6. Ripristinare il pool SQL eliminato
    1. Per ripristinare il pool SQL eliminato in un server diverso, assicurarsi di specificare l'altro nome del server.  Questo server può trovarsi anche in un gruppo di risorse e in un'area diversi.
    1. Per eseguire il ripristino in una sottoscrizione diversa, usare il pulsante [Sposta](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) per spostare il server in un'altra sottoscrizione.
7. Verificare che il data warehouse ripristinato sia online.
8. Al termine del ripristino, è possibile configurare il data warehouse ripristinato seguendo la procedura di [configurazione del database dopo il ripristino](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
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

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Ripristinare un database eliminato con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare al server in cui è stata ospitata la data warehouse eliminata.
3. Selezionare l'icona **database eliminati** nel sommario.

    ![Database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selezionare il Azure sinapsi Analytics eliminato che si vuole ripristinare.

    ![Selezionare i database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Specificare un nuovo **nome di database** e fare clic su **OK**

    ![Specificare il nome del database](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare da un pool SQL di backup geografico](sql-data-warehouse-restore-from-geo-backup.md)
