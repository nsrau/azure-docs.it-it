---
title: Esempio di PowerShell - Ripristinare un backup del database SQL di Azure | Microsoft Docs
description: Script di esempio di Azure PowerShell per ripristinare un singolo database SQL di Azure da backup con ridondanza geografica
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3bfefa704fdd819b3841dcc58866c310353bfdc3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883611"
---
# <a name="use-powershell-to-restore-an-azure-sql-single-database-from-backups"></a>Usare PowerShell per ripristinare un singolo database SQL di Azure dai backup

Questo esempio di script di PowerShell ripristina un database SQL di Azure da un backup con ridondanza geografica, ripristina un database SQL di Azure eliminato in base al backup più recente ed esegue un ripristino temporizzato di un database SQL di Azure.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. | 
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita un database singolo o un pool elastico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un database nel server di database SQL come database autonomo o in pool. |
[Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) | Ottiene una copia di backup con ridondanza geografica di un database autonomo o in pool. |
| [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) | Ripristina un database SQL autonomo o in pool. |
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) | Rimuove un database SQL di Azure autonomo o in pool. |
| [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Ottiene un database autonomo o in pool eliminato che è possibile ripristinare. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
