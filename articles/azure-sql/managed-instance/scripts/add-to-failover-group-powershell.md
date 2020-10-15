---
title: "PowerShell: Aggiungere un'istanza gestita a un gruppo di failover automatico"
titleSuffix: Azure SQL Managed Instance
description: Script di esempio di Azure PowerShell per creare un'istanza gestita, aggiungerla a un gruppo di failover automatico e testare il failover.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: cd954f07d733e4d80054d6f429a748ca59b5747d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273212"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>Usare PowerShell per aggiungere un'istanza gestita a un gruppo di failover 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Questo script di esempio di PowerShell crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria all'istanza gestita secondaria. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario Azure PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate. Sarà necessario rimuovere due volte il gruppo di risorse. Quando si rimuove il gruppo di risorse la prima volta, verranno rimossi l'istanza gestita e i cluster virtuali, ma l'operazione non verrà completata e verrà visualizzato il messaggio di errore `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'`. Eseguire una seconda volta il comando Remove-AzResourceGroup per rimuovere tutte le risorse residue e il gruppo di risorse.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse di Azure.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Aggiunge una configurazione di subnet a una rete virtuale. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ottiene una rete virtuale in un gruppo di risorse. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene una subnet in una rete virtuale. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Crea una tabella di route. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Aggiorna una configurazione di subnet per una rete virtuale.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Aggiorna una rete virtuale.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Ottiene un gruppo di sicurezza di rete. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Aggiunge la configurazione di una regola di sicurezza di rete a un gruppo di sicurezza di rete. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Aggiorna un gruppo di sicurezza di rete.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Aggiunge una route a una tabella di route. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Aggiorna una tabella di route.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Restituisce informazioni sull'istanza gestita di SQL di Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita di SQL di Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita di SQL.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita di SQL. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di script di PowerShell per l'istanza gestita di SQL sono disponibili in [Script di PowerShell per Istanza gestita di SQL di Azure](../../database/powershell-script-content-guide.md).
