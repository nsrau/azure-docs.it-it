---
title: Ripristinare un data warehouse da un backup geografico
description: Guida alle procedure per il ripristino geografico di un Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 69ba3ed981a27dfff41ea9ea52e1da769a9366c4
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759619"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Ripristino geografico Azure SQL Data Warehouse

Questo articolo illustra come ripristinare il data warehouse da un backup geografico tramite portale di Azure e PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitata da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Eseguire il ripristino da un'area geografica di Azure tramite PowerShell

Per eseguire il ripristino da un backup geografico, usare il cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) e [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000**c**) come parametro facoltativo.
>

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Aprire PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Consente di selezionare la sottoscrizione che contiene il data warehouse da ripristinare.
4. Ottenere la data warehouse che si desidera ripristinare.
5. Creare la richiesta di ripristino per il data warehouse.
6. Verificare lo stato del data warehouse con ripristino geografico.
7. Per configurare il data warehouse al termine del ripristino, vedere [Configurare il database dopo il ripristino]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Il database ripristinato sarà abilitato TDE se il database di origine è abilitato per questa tecnologia.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Eseguire il ripristino da un'area geografica di Azure tramite portale di Azure

Attenersi alla procedura descritta di seguito per ripristinare un Azure SQL Data Warehouse da un backup geografico:

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .
1. Fare clic su **+ Crea una risorsa** e cercare SQL data warehouse e fare clic su **Crea**.

    ![Nuovo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Inserire le informazioni richieste nella scheda **nozioni di base** e fare clic su **Avanti: impostazioni aggiuntive**.

    ![Nozioni di base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Per **Usa parametro dati esistente** selezionare **backup** e selezionare il backup appropriato dalle opzioni Scorri verso il basso. Fare clic su **Verifica + crea**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Una volta ripristinato il data warehouse, verificare che lo **stato** sia online.

## <a name="next-steps"></a>Fasi successive
- [Ripristinare un data warehouse esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un data warehouse eliminato](sql-data-warehouse-restore-deleted-dw.md)