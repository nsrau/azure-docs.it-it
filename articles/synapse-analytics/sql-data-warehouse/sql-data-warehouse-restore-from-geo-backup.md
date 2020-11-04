---
title: Ripristinare un pool SQL dedicato da un backup geografico
description: Guida alle procedure per il ripristino geografico di un pool SQL dedicato in Azure sinapsi Analytics
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7496cedd127182482bccf97909cc0a0a4a78253f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313411"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Ripristino geografico di un pool SQL dedicato in Azure sinapsi Analytics

Questo articolo illustra come ripristinare il pool SQL dedicato da un backup geografico tramite portale di Azure e PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool SQL dedicato è ospitato da un [server SQL logico](../../azure-sql/database/logical-servers.md) (ad esempio, myserver.database.Windows.NET) con una quota di DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Eseguire il ripristino da un'area geografica di Azure tramite PowerShell

Per eseguire il ripristino da un backup geografico, usare il cmdlet [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000 **c** ) come parametro facoltativo.
>

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Consente di selezionare la sottoscrizione che contiene il data warehouse da ripristinare.
5. Ottenere la data warehouse che si desidera ripristinare.
6. Creare la richiesta di ripristino per il data warehouse.
7. Verificare lo stato del data warehouse con ripristino geografico.
8. Per configurare il data warehouse al termine del ripristino, vedere [Configurare il database dopo il ripristino]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
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

Attenersi alla procedura descritta di seguito per ripristinare un pool SQL dedicato da un backup geografico:

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .
2. Fare clic su **Crea una risorsa**.

   ![Nuovo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Fare clic su **database** e quindi su **Azure sinapsi Analytics (in precedenza SQL DW)**.

   ![Nuovo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Inserire le informazioni richieste nella scheda **nozioni di base** e fare clic su **Avanti: impostazioni aggiuntive**.

   ![Operazioni di base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Per **Usa parametro dati esistente** selezionare **backup** e selezionare il backup appropriato dalle opzioni Scorri verso il basso. Fare clic su **Rivedi e crea**.

   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Una volta ripristinato il data warehouse, verificare che lo **stato** sia online.

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL dedicato esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un pool SQL dedicato eliminato](sql-data-warehouse-restore-deleted-dw.md)
