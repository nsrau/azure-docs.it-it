---
title: Ripristinare un Azure SQL Data Warehouse da un backup geografico | Microsoft Docs
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
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68426655"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Ripristino geografico Azure SQL Data Warehouse

Questo articolo illustra come ripristinare il data warehouse da un backup geografico tramite portale di Azure e PowerShell.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verificare la capacità in DTU.** Ogni SQL Data Warehouse è ospitata da un server SQL (ad esempio, myserver.database.windows.net) con una quota DTU predefinita. Verificare che SQL Server disponga di una quota DTU rimanente per il database da ripristinare. Per informazioni su come calcolare la DTU necessaria o per richiedere maggiore DTU, vedere come [richiedere una modifica della quota DTU][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Eseguire il ripristino da un'area geografica di Azure tramite PowerShell

Per eseguire il ripristino da un backup geografico, usare il cmdlet [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] e [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .

> [!NOTE]
> È possibile eseguire un ripristino geografico alla seconda generazione. A tale scopo, specificare ServiceObjectiveName di seconda generazione (ad es. DW1000**c**) come parametro facoltativo.
>

1. Prima di iniziare, assicurarsi di [installare Azure PowerShell][Install Azure PowerShell].
2. Aprire PowerShell.
2. Connettersi al proprio account Azure ed elencare tutte le sottoscrizioni associate all'account.
3. Consente di selezionare la sottoscrizione che contiene il data warehouse da ripristinare.
4. Ottenere la data warehouse che si desidera ripristinare.
5. Creare la richiesta di ripristino per il data warehouse.
6. Verificare lo stato del data warehouse con ripristino geografico.
7. Per configurare il data warehouse al termine del ripristino, vedere [Configurare il database dopo il ripristino][Configure your database after recovery].

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

1. Accedere al proprio account di [portale di Azure][Azure portal] .
1. Fare clic su **+ Crea una risorsa** e cercare SQL data warehouse e fare clic su **Crea**.

    ![Nuovo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Inserire le informazioni richieste nella scheda nozioni di **base** e **fare clic su Avanti: Impostazioni**aggiuntive.

    ![Generale](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Per **Usa parametro dati esistente** selezionare **backup** e selezionare il backup appropriato dalle opzioni Scorri verso il basso. Fare clic su **Verifica + crea**.
 
   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Una volta ripristinato il data warehouse, verificare che lo **stato** sia online.

## <a name="next-steps"></a>Fasi successive
- [Ripristinare un data warehouse esistente][Restore an existing data warehouse]
- [Ripristinare un data warehouse eliminato][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
