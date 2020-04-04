---
title: Ripristinare un data warehouse da un backup geograficoRestore a data warehouse from a geo-backup
description: Guida alle procedure per il ripristino geografico di un pool SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 68d53d2a33b7ab705dfa88f03618a5d5a3d1bced
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633033"
---
# <a name="geo-restore-for-sql-pool"></a>Ripristino geografico per il pool SQLGeo-restore for SQL pool

In this article, you learn to restore your SQL pool from a geo-backup through Azure portal and PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool SQL è ospitato da un server SQL (ad esempio, myserver.database.windows.net) che ha una quota DTU predefinita. Verificare che il server SQL disponga di una quota DTU rimanente sufficiente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Eseguire il ripristino da un'area geografica di Azure tramite PowerShellRestore from an Azure geographical region through PowerShell

Per eseguire il ripristino da un backup geografico, utilizzare il cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) e [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000**c**) come parametro facoltativo.
>

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
2. Aprire PowerShell.
3. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
4. Selezionare la sottoscrizione che contiene il data warehouse da ripristinare.
5. Ottenere il data warehouse che si desidera ripristinare.
6. Creare la richiesta di ripristino per il data warehouse.
7. Verificare lo stato del data warehouse con ripristino geografico.
8. Per configurare il data warehouse al termine del ripristino, vedere [Configurare il database dopo il ripristino]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Eseguire il ripristino da un'area geografica di Azure tramite il portale di AzureRestore from an Azure geographical region through Azure portal

Seguire i passaggi descritti di seguito per ripristinare un pool SQL da un backup geografico:

1. Accedere all'account del portale di Azure.Sign in to your [Azure portal](https://portal.azure.com/) account.
2. Selezionare **+ Crea una risorsa**.

   ![Nuovo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Fare clic su **Database** e quindi su Analisi synapse di Azure (in precedenza SQL DW).

   ![Nuovo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Compilare le informazioni richieste nella scheda **Nozioni di base** e fare clic su **Avanti: Impostazioni aggiuntive**.

   ![Nozioni di base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Per Usa parametro **dati esistente,** selezionare **Backup** e selezionare il backup appropriato dalle opzioni di scorrimento verso il basso. Fare clic su **Revisione e creazione**.

   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Una volta ripristinato il data warehouse, verificare che **lo stato** sia online.

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL esistenteRestore an existing SQL pool](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un pool SQL eliminato](sql-data-warehouse-restore-deleted-dw.md)
