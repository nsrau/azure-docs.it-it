---
title: 'PowerShell: Configurare la replica geografica attiva per pool elastici'
description: Esempio di script di Azure PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure ed eseguirne il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: e23ae783bee48af0d0f1cf8bcaea1f8f1e307c54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325267"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Usare PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script di Azure PowerShell configura la replica geografica attiva per un database in pool nel database SQL di Azure e ne esegue il failover in una replica secondaria.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario Az PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server che ospita database e pool elastici. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Consente di creare un pool elastico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un database in un server. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aggiorna le proprietà del database o sposta un database all'interno, all'esterno o tra pool elastici. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Crea un database secondario per un database esistente e avvia la replica dei dati. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Ottiene uno o più database. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Un database secondario diventa il database primario per avviare il failover.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Ottiene i collegamenti di replica geografica tra un database SQL di Azure e un gruppo di risorse o un server SQL logico. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../powershell-script-content-guide.md).
