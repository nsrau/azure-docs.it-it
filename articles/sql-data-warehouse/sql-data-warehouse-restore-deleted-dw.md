---
title: Ripristinare un Azure SQL Data Warehouse eliminato | Microsoft Docs
description: Guida per il ripristino di un Azure SQL Data Warehouse eliminato.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 376a50a79858aee34aa71d172ca5836646a6651d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426629"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>Ripristinare un Azure SQL Data Warehouse eliminato

In questo articolo si apprenderà come ripristinare un SQL Data Warehouse eliminato usando portale di Azure e PowerShell:

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitata da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita.  Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU][Request a DTU quota change].

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Ripristinare un data warehouse eliminato tramite PowerShell

Per ripristinare un SQL Data Warehouse eliminato, usare il cmdlet [Restore-AzSqlDatabase][Restore-AzSqlDatabase] . Se anche il server logico corrispondente è stato eliminato, non è possibile ripristinare tale data warehouse.

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell][Install Azure PowerShell].
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Consente di selezionare la sottoscrizione che contiene il data warehouse eliminato da ripristinare.
5. Ottenere il data warehouse eliminato specifico.
6. Ripristinare il data warehouse eliminato
    1. Per ripristinare il SQL Data Warehouse eliminato in un server logico diverso, assicurarsi di specificare l'altro nome del server logico.  Questo server logico può trovarsi anche in un gruppo di risorse e in un'area diversi.
    1. Per eseguire il ripristino in un'altra sottoscrizione, utilizzare il pulsante [Sposta][Move] per spostare il server logico in un'altra sottoscrizione.
1. Verificare che il data warehouse ripristinato sia online.
1. Al termine del ripristino, è possibile configurare il data warehouse ripristinato seguendo la procedura di [configurazione del database dopo il ripristino][Configure your database after recovery].

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

1. Accedere al [portale di Azure][Azure portal].
2. Passare al server SQL in cui è ospitato il data warehouse eliminato.
3. Selezionare l'icona **database eliminati** nel sommario.

    ![Database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selezionare il SQL Data Warehouse eliminato che si desidera ripristinare.

    ![Selezionare i database eliminati](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Specificare un nuovo **nome di database** e fare clic su **OK**

    ![Specificare il nome del database](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Fasi successive
- [Ripristinare un data warehouse esistente][Restore an existing data warehouse]
- [Eseguire il ripristino da un data warehouse di backup geografico][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
