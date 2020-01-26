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
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759602"
---
# <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

In questo articolo si apprenderà come creare un nuovo punto di ripristino definito dall'utente per Azure SQL Data Warehouse usando PowerShell e portale di Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Creare punti di ripristino definiti dall'utente tramite PowerShell

Per creare un punto di ripristino definito dall'utente, usare il cmdlet [di PowerShell New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) .

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
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

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .

2. Passare alla SQL Data Warehouse per la quale si desidera creare un punto di ripristino.

3. Selezionare **Panoramica** nel riquadro a sinistra e selezionare **+ nuovo punto di ripristino**. Se il pulsante nuovo punto di ripristino non è abilitato, assicurarsi che il data warehouse non sia sospeso.

    ![Nuovo punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Specificare un nome per il punto di ripristino definito dall'utente e fare clic su **applica**. I punti di ripristino definiti dall'utente hanno un periodo di conservazione predefinito di sette giorni.

    ![Nome del punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un data warehouse esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un data warehouse eliminato](sql-data-warehouse-restore-deleted-dw.md)
- [Eseguire il ripristino da un data warehouse di backup geografico](sql-data-warehouse-restore-from-geo-backup.md)

