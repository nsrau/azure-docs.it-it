---
title: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete | Microsoft Docs
description: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete di tipo firewall.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 21ee52f0a9c3b5a6cc64bdaf92c05db4a18903ca
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717523"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Esempio di script per instradare il traffico attraverso un'appliance virtuale di rete

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Crea anche una VM con inoltro IP attivato per instradare il traffico tra le due subnet. Dopo aver eseguito lo script è possibile distribuire il software di rete, ad esempio un'applicazione firewall, nella VM.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/powershell) o da un'installazione di PowerShell locale. Se si usa PowerShell in locale, per questo script è necessario il modulo Azure PowerShell versione 5.4.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Consente di creare le subnet back-end e di rete perimetrale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Consente di creare un'interfaccia di rete virtuale e attiva l'inoltro IP. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Consente di creare un gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Consente di creare regole del gruppo di sicurezza di rete per le porte HTTP e HTTPS in ingresso alla VM. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Consente di associare il gruppo di sicurezza di rete e le tabelle di route alle subnet. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Consente di creare una tabella di route per tutte le route. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Consente di creare le route per instradare il traffico tra subnet e Internet attraverso la macchina virtuale. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Consente di creare una macchina virtuale e vi connette la NIC. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell della rete virtuale sono disponibili in [Virtual network PowerShell samples](../powershell-samples.md) (Esempi di PowerShell della rete virtuale).