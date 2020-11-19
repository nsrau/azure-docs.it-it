---
title: 'PowerShell: Aggiungere un pool elastico a un gruppo di failover automatico'
description: Script di esempio di Azure PowerShell per creare un pool elastico del database SQL di Azure, aggiungerlo a un gruppo di failover automatico e testare il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: a8326249b48e98eccecb5f0c4953add26e167560
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594108"
---
# <a name="use-powershell-to-add-an-elastic-pool-to-a-failover-group"></a>Usare PowerShell per aggiungere un pool elastico a un gruppo di failover
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script di Azure PowerShell crea un database nel Database SQL di Azure, lo aggiunge a un pool elastico, crea un gruppo di failover e testa il failover.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario Az PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita database e pool elastici. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Crea una regola del firewall a livello di server per un server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un nuovo database. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Crea un pool di database elastici.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Crea un nuovo gruppo di failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Ottiene uno o più database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Aggiunge uno o più database a un gruppo di failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ottiene o elenca i gruppi di failover del database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Esegue il failover di un gruppo di failover del database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../powershell-script-content-guide.md).
