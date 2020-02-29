---
title: Ripristinare un data warehouse da un backup geografico
description: Guida alle procedure per il ripristino geografico di un pool SQL.
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
ms.openlocfilehash: 624c6665e70802907be8a41015b78d36cca7df1c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198373"
---
# <a name="geo-restore-for-sql-pool"></a>Ripristino geografico per il pool SQL

Questo articolo illustra come ripristinare il pool SQL da un backup geografico tramite portale di Azure e PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni pool SQL è ospitato da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU](sql-data-warehouse-get-started-create-support-ticket.md).

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

Attenersi alla procedura descritta di seguito per ripristinare un pool SQL da un backup geografico:

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .
1. Selezionare **+ Crea una risorsa**. 

![Nuovo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Fare clic su **database** e quindi * * Azure sinapsi Analytics (in precedenza SQL DW) * *.

![Nuovo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Inserire le informazioni richieste nella scheda **nozioni di base** e fare clic su **Avanti: impostazioni aggiuntive**.

![Nozioni di base](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Per **Usa parametro dati esistente** selezionare **backup** e selezionare il backup appropriato dalle opzioni Scorri verso il basso. Fare clic su **Verifica + crea**.
 
![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Una volta ripristinato il data warehouse, verificare che lo **stato** sia online.

## <a name="next-steps"></a>Passaggi successivi
- [Ripristinare un pool SQL esistente](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un pool SQL eliminato](sql-data-warehouse-restore-deleted-dw.md)