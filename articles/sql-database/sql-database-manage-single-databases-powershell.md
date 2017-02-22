---
title: 'PowerShell: creare e gestire database SQL singoli di Azure | Documentazione Microsoft'
description: Informazioni di riferimento rapido sulla creazione e gestione di database SQL singoli di Azure con il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>Creare e gestire database SQL singoli di Azure con PowerShell

È possibile creare e gestire database SQL singoli di Azure con il [portale di Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l'API REST o C#. Questo argomento illustra l'uso di PowerShell. Per il portale di Azure, vedere [Creare e gestire database singoli con il portale di Azure](sql-database-manage-single-databases-powershell.md). Per Transact-SQL, vedere [Creare e gestire database singoli con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>Creare un database SQL di Azure usando PowerShell

Per creare un database SQL, usare il cmdlet [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). Il gruppo di risorse e il server devono già esistere nella sottoscrizione. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> Per uno script di esempio, vedere [Creare uno script di PowerShell per database SQL](sql-database-get-started-powershell.md).
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modificare il livello di servizio e il livello delle prestazioni di un database singolo

Eseguire il cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) e impostare **-RequestedServiceObjectiveName** sul livello di prestazioni del piano tariffario desiderato, ad esempio *S0*, *S1*, *S2*, *S3*, *P1*, *P2* e così via.

Sostituire ```{variables}``` con i valori (non includere le parentesi graffe).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>Passaggi successivi
* Per una panoramica degli strumenti di gestione, vedere [Panoramica degli strumenti di gestione](sql-database-manage-overview.md).
* Per informazioni su come eseguire le attività di gestione usando il portale di Azure, vedere [Gestire database SQL di Azure mediante il portale di Azure](sql-database-manage-portal.md).
* Per informazioni su come eseguire le attività di gestione usando PowerShell, vedere [Manage Azure SQL Databases using PowerShell](sql-database-manage-powershell.md) (Gestire database SQL di Azure usando PowerShell).
* Per informazioni su come eseguire le attività di gestione usando SQL Server Management Studio, vedere [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Per informazioni sul servizio di database SQL, vedere [Informazioni sul database SQL](sql-database-technical-overview.md). 
* Per informazioni sui server di database di Azure e sulle funzionalità di database, vedere [Funzionalità](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


