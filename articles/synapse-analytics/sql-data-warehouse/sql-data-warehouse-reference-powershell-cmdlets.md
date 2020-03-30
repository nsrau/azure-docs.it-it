---
title: API REST & di PowerShellPowerShell & REST APIs
description: Trovare i cmdlet principali di PowerShell per il pool SQL di Azure Synapse Analytics, incluso come sospendere e riprendere un database.
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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351380"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>API REST di PowerShell & per il pool SQL di Azure Synapse Analytics

Molte attività amministrative del pool SQL di Azure Synapse Analytics possono essere gestite usando i cmdlet di Azure PowerShell o le API REST.  Di seguito sono riportati alcuni esempi di come usare i comandi di PowerShell per automatizzare le attività comuni nel pool SQL.  Per altri esempi di REST, vedere [Gestire la scalabilità con REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introduzione ai cmdlet di Azure PowerShell

1. Aprire Windows PowerShell.
2. Al prompt di PowerShell, eseguire questi comandi per accedere ad Azure Resource Manager e selezionare la sottoscrizione.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Esempio di data warehouse In pausa
Sospende un database denominato "Database02" ospitato su un server denominato "Server01".  Il server si trova in un gruppo di risorse di Azure denominato "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Variazione, in questo esempio, l'oggetto recuperato viene reindirizzato a [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Il database viene pertanto sospeso. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Esempio di data warehouse avviatoStart data warehouse example

Fa riprendere le operazioni di un database denominato "Database02" ospitato su un server denominato "Server01". Il server è incluso in un gruppo di risorse denominato "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Come variazione, questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". L'oggetto recuperato viene reindirizzato a [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Altri cmdlet di PowerShell supportati
Questi cmdlet di PowerShell sono supportati con il data warehouse di Azure Synapse Analytics.These PowerShell cmdlets are supported with Azure Synapse Analytics data warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Nuovo-AzSqlDatabaseNew-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Passaggi successivi
Per altri esempi di PowerShell, vedere:

* [Creare un data warehouse tramite PowerShellCreate a data warehouse using PowerShell](create-data-warehouse-powershell.md)
* [Ripristino del database](sql-data-warehouse-restore-points.md)

Per altre attività che possono essere automatizzate con PowerShell, vedere Cmdlet per il database SQL di [Azure.](https://docs.microsoft.com/powershell/module/az.sql) Non tutti i cmdlet del database SQL di Azure sono supportati per il data warehouse di Azure Synapse Analytics.Not all Azure SQL Database cmdlets are supported for Azure Synapse Analytics data warehouse.  Per un elenco delle attività che possono essere automatizzate con REST, vedere Operazioni per il database SQL di Azure.For a list of tasks that can be automated with REST, see [Operations for Azure SQL Database](/rest/api/sql/).
