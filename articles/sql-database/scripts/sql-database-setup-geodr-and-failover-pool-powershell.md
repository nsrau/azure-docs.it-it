---
title: 'Esempio di PowerShell: Configurare la replica geografica attiva per un database in pool nel database SQL di Azure'
description: Esempio di script di Azure PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure ed eseguirne il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3ff5d41c8d37745cbb06dbb98b5ce6e9b6a19ed4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691494"
---
# <a name="use-powershell-to-configure-active-geo-replication-for-a-pooled-database-in-azure-sql-database"></a>Usare PowerShell per configurare la replica geografica attiva per un database in pool nel database SQL di Azure

Questo esempio di script di PowerShell configura la replica geografica attiva per un database in pool nel database SQL di Azure e ne esegue il failover su una replica secondaria.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-elastic-pool.ps1?highlight=17-20 "Set up active geo-replication for elastic pool")]

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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Crea un server di database SQL che ospita database singoli e pool elastici. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Consente di creare un pool elastico. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Crea un database singolo o in pool. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Aggiorna le proprietà del database o sposta un database all'interno, all'esterno o tra pool elastici. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Crea un database secondario per un database esistente e avvia la replica dei dati. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Ottiene uno o più database. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Un database secondario diventa il database primario per avviare il failover.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Ottiene i collegamenti di replica geografica tra un database SQL di Azure e un gruppo di risorse o SQL Server. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
