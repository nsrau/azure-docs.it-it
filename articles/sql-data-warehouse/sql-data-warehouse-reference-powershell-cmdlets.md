---
title: API REST di & PowerShell
description: Trovare i principali cmdlet di PowerShell per Azure sinapsi Analytics pool SQL, inclusa la modalità di sospensione e ripresa di un database.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198457"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>API REST di & PowerShell per il pool SQL di Azure sinapsi Analytics

Molte attività amministrative del pool SQL di Azure sinapsi Analytics possono essere gestite usando cmdlet di Azure PowerShell o API REST.  Di seguito sono riportati alcuni esempi di come usare i comandi di PowerShell per automatizzare le attività comuni nel pool SQL.  Per altri esempi, vedere [Gestire le operazioni di scalabilità con REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Una variante, questo esempio invia tramite pipe l'oggetto recuperato a [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Il database viene pertanto sospeso. Il comando finale mostra i risultati.

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

Come variazione, questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". Invia tramite pipe l'oggetto recuperato a [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Altri cmdlet di PowerShell supportati
Questi cmdlet di PowerShell sono supportati con Azure sinapsi Analytics data warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi di PowerShell, vedere:

* [Creare una data warehouse usando PowerShell](create-data-warehouse-powershell.md)
* [Ripristino del database](sql-data-warehouse-restore-database-powershell.md)

Per altre attività che possono essere automatizzate con PowerShell, vedere [cmdlet del database SQL di Azure](https://docs.microsoft.com/powershell/module/az.sql). Non tutti i cmdlet del database SQL di Azure sono supportati per l'analisi delle sinapsi di Azure data warehouse.  Per un elenco delle attività che possono essere automatizzate con REST, vedere [operazioni per il database SQL di Azure](/rest/api/sql/).
