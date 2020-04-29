---
title: API REST di & PowerShell
description: Trovare i principali cmdlet di PowerShell per Azure sinapsi Analytics pool SQL, inclusa la modalità di sospensione e ripresa di un database.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f3d6d0c1f71e2262e943998cdc08717291903365
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743813"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>API REST di & PowerShell per il pool SQL di Azure sinapsi Analytics

Molte attività amministrative del pool SQL di Azure sinapsi Analytics possono essere gestite usando cmdlet di Azure PowerShell o API REST.  Di seguito sono riportati alcuni esempi di come usare i comandi di PowerShell per automatizzare le attività comuni nel pool SQL.  Per altri esempi di REST, vedere [Gestire la scalabilità con REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introduzione ai cmdlet di Azure PowerShell

1. Aprire Windows PowerShell.
2. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Sospendi data warehouse esempio

Sospende un database denominato "Database02" ospitato su un server denominato "Server01".  Il server si trova in un gruppo di risorse di Azure denominato "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Una variante, questo esempio invia tramite pipe l'oggetto recuperato a [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Il database viene pertanto sospeso. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Esempio di avvio data warehouse

Fa riprendere le operazioni di un database denominato "Database02" ospitato su un server denominato "Server01". Il server è incluso in un gruppo di risorse denominato "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". Invia tramite pipe l'oggetto recuperato a [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Altri cmdlet di PowerShell supportati

Questi cmdlet di PowerShell sono supportati con Azure sinapsi Analytics data warehouse.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/PowerShell/Module/AZ.SQL/RESTORE-azsqldatabase? TOC =/Azure/Synapse-Analytics/SQL-data-warehouse/TOC.JSON&BC =/Azure/Synapse-Analytics/SQL-data-warehouse/breadcrumb/TOC.Json)
* [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di PowerShell, vedere:

* [Creare una data warehouse usando PowerShell](create-data-warehouse-powershell.md)
* [Ripristino del database](sql-data-warehouse-restore-points.md)

Per altre attività che possono essere automatizzate con PowerShell, vedere [cmdlet del database SQL di Azure]/PowerShell/Module/AZ.SQL? TOC =/Azure/Synapse-Analytics/SQL-data-warehouse/TOC.JSON&BC =/Azure/Synapse-Analytics/SQL-data-warehouse/breadcrumb/TOC.Json). Non tutti i cmdlet del database SQL di Azure sono supportati per l'analisi delle sinapsi di Azure data warehouse. Per un elenco delle attività che possono essere automatizzate con REST, vedere [operazioni per il database SQL di Azure](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
