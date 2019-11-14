---
title: "Esempio di PowerShell: Aggiungere un'istanza gestita del database SQL di Azure a un gruppo di failover"
description: Script di esempio di Azure PowerShell per creare un'istanza gestita del database SQL di Azure, aggiungerlo a un gruppo di failover e testare il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691753"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Usare PowerShell per aggiungere un'istanza gestita del database SQL di Azure a un gruppo di failover 

Questo script di esempio di PowerShell crea due istanze gestite, le aggiunge a un gruppo di failover e quindi testa il failover dall'istanza gestita primaria all'istanza gestita secondaria. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario AZ PowerShell 1.4.0 o versione successiva. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-scripts"></a>Script di esempio

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate. Sarà necessario rimuovere due volte il gruppo di risorse. Quando si rimuove il gruppo di risorse la prima volta, verranno rimossi l'istanza gestita e i cluster virtuali, ma l'operazione non verrà completata e verrà visualizzato il messaggio di errore `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Eseguire una seconda volta il comando Remove-AzResourceGroup per rimuovere tutte le risorse residue e il gruppo di risorse.

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
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Crea un'istanza gestita di database SQL di Azure.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Restituisce informazioni sul database dell'istanza gestita del database SQL di Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Crea una configurazione IP per un gateway di rete virtuale. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Crea un gateway di rete virtuale. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Crea una connessione tra due gateway di rete virtuali.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Crea un nuovo gruppo di failover dell'istanza gestita del database SQL di Azure.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Ottiene o elenca i gruppi di failover dell'istanza gestita.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Esegue un failover di un gruppo di failover dell'istanza gestita. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Per altri esempi, vedere tra gli [script di PowerShell per database SQL di Azure](../sql-database-powershell-samples.md).
