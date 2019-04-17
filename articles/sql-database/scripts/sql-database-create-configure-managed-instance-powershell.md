---
title: Esempio di PowerShell - Creare un'istanza gestita nel database SQL di Azure | Microsoft Docs
description: Script di esempio di Azure PowerShell per creare un'istanza gestita nel database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: c85b967615e866635cb4dd93be5ddeb78a8c7129
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357000"
---
# <a name="use-powershell-to-create-an-azure-sql-database-managed-instance"></a>Usare PowerShell per creare un'istanza gestita di database SQL di Azure

Questo esempio di script di PowerShell crea un'istanza gestita di database SQL di Azure in una subnet dedicata all'interno di una nuova rete virtuale. Configura anche una tabella di route e un gruppo di sicurezza di rete per la rete virtuale. Al termine dell'esecuzione dello script, l'istanza gestita è accessibile dalla rete virtuale o da un ambiente locale. Vedere [Configurare una macchina virtuale di Azure per la connessione a un'istanza gestita di database SQL di Azure](../sql-database-managed-instance-configure-vm.md) e [Configurare una connessione da punto a sito a un'istanza gestita di database SQL di Azure da un computer locale](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Per le limitazioni, vedere le [aree supportate](../sql-database-managed-instance-resource-limits.md#supported-regions) e i [tipi di sottoscrizione](../sql-database-managed-instance-resource-limits.md#supported-subscription-types) supportati.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse.
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | Aggiunge una configurazione di subnet a una rete virtuale |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | Ottiene una rete virtuale in un gruppo di risorse |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | Imposta lo stato dell'obiettivo per una rete virtuale |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | Ottiene una subnet in una rete virtuale |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | Configura lo stato dell'obiettivo per una configurazione di subnet in una rete virtuale |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | Crea una tabella di route |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | Ottiene le tabelle di route |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | Imposta lo stato dell'obiettivo per una tabella di route |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | Crea un'istanza gestita di database SQL di Azure |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
