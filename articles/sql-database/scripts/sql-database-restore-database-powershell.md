---
title: 'Script di Azure PowerShell: ripristinare un database SQL | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: ripristinare un database SQL usando PowerShell'
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 81e7d2390f9872b7b3e01f763d29275e3ec32667
ms.lasthandoff: 03/30/2017

---

# <a name="restore-a-sql-database-using-powershell"></a>Ripristinare un database SQL usando PowerShell

Questo script di esempio di PowerShell ripristina un database SQL di Azure da un backup con ridondanza geografica e ripristina un database eliminato in base al backup più recente.  

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1 "Creare database SQL")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. | [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Crea un server logico che ospita un database o un pool elastico. | 
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Crea un database in un server logico come database singolo o in pool. |
[Get-AzureRmSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldatabasegeobackup) | Ottiene una copia di backup con ridondanza geografica di un database. |
| [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/restore-azurermsqldatabase) | Ripristina un database SQL. |
|[Remove-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/remove-azurermsqldatabase) | Rimuove un database SQL di Azure. |
| [Get-AzureRmSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/get-azurermsqldeleteddatabasebackup) | Ottiene un database eliminato che è possibile ripristinare. |
| [Remove-AzureRmResourceGroup]() | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).

