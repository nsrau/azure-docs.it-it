---
title: Punti di ripristino definiti dall'utente
description: Come creare un punto di ripristino per il pool SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350153"
---
# <a name="user-defined-restore-points"></a>Punti di ripristino definiti dall'utente

In questo articolo imparerai a creare un nuovo punto di ripristino definito dall'utente per un pool SQL in Azure Synapse Analytics usando PowerShell e il portale di Azure.In this article, you'll learn to create a new user-defined restore point for a SQL pool in Azure Synapse Analytics by using PowerShell and the Azure portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Creare punti di ripristino definiti dall'utente tramite PowerShellCreate user-defined restore points through PowerShell

Per creare un punto di ripristino definito dall'utente, utilizzare il cmdlet [PowerShell New-AzSqlDatabaseRestorePoint.](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0)

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
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

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Creare punti di ripristino definiti dall'utente tramite il portale di AzureCreate user-defined restore points through the Azure portal

I punti di ripristino definiti dall'utente possono essere creati anche tramite il portale di Azure.User-defined restore points can also be created through Azure portal.

1. Accedere all'account del portale di Azure.Sign in to your [Azure portal](https://portal.azure.com/) account.

2. Passare al pool SQL per il quale si desidera creare un punto di ripristino.

3. Selezionare **Panoramica** dal riquadro di sinistra, selezionare **Nuovo punto di ripristino**. Se il pulsante Nuovo punto di ripristino non è abilitato, assicurarsi che il pool SQL non sia in pausa.

    ![Nuovo punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Specificare un nome per il punto di ripristino definito dall'utente e fare clic su **Applica**. I punti di ripristino definiti dall'utente hanno un periodo di conservazione predefinito di sette giorni.

    ![Nome del punto di ripristino](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL esistenteRestore an existing SQL pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un pool SQL eliminato](sql-data-warehouse-restore-deleted-dw.md)
- [Eseguire il ripristino da un pool SQL di backup geograficoRestore from a geo-backup SQL pool](sql-data-warehouse-restore-from-geo-backup.md)

