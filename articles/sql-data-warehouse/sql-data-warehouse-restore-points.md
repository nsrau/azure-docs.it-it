---
title: Punti di ripristino definiti dall'utente
description: Come creare un punto di ripristino Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 61ceb2f1271d085321215aff1c6d138feb95d743
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692505"
---
# <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

In questo articolo si apprenderà come creare un nuovo punto di ripristino definito dall'utente per Azure SQL Data Warehouse usando PowerShell e portale di Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Creare punti di ripristino definiti dall'utente tramite PowerShell

Per creare un punto di ripristino definito dall'utente, usare il cmdlet [di PowerShell New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] .

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell][Install Azure PowerShell].
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Selezionare la sottoscrizione che contiene il database da ripristinare.
5. Creare un punto di ripristino per una copia immediata del data warehouse.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Vedere l'elenco di tutti i punti di ripristino esistenti.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Creare punti di ripristino definiti dall'utente tramite il portale di Azure

I punti di ripristino definiti dall'utente possono essere creati anche tramite portale di Azure.

1. Accedere al proprio account di [portale di Azure][Azure portal] .

2. Passare alla SQL Data Warehouse per la quale si desidera creare un punto di ripristino.

3. Selezionare **Panoramica** nel riquadro a sinistra e selezionare **+ nuovo punto di ripristino**. Se il pulsante nuovo punto di ripristino non è abilitato, assicurarsi che il data warehouse non sia sospeso.

    ![Nuovo punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Specificare un nome per il punto di ripristino definito dall'utente e fare clic su **applica**. I punti di ripristino definiti dall'utente hanno un periodo di conservazione predefinito di sette giorni.

    ![Nome del punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un data warehouse esistente][Restore an existing data warehouse]
- [Ripristinare un data warehouse eliminato][Restore a deleted data warehouse]
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
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
