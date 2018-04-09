---
title: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete | Microsoft Docs
description: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete di tipo firewall.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 766d800a7875536b729f564a3d9948e5598de6aa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Crea anche una VM con inoltro IP attivato per instradare il traffico tra le due subnet. Dopo aver eseguito lo script è possibile distribuire il software di rete, ad esempio un'applicazione firewall, nella VM.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/powershell) o da un'installazione di PowerShell locale. Se si usa PowerShell in locale, per questo script è necessario il modulo AzureRM PowerShell versione 5.4.1 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Consente di creare le subnet back-end e di rete perimetrale. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Consente di creare un'interfaccia di rete virtuale e attiva l'inoltro IP. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Consente di creare un gruppo di sicurezza di rete. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Consente di creare regole del gruppo di sicurezza di rete per le porte HTTP e HTTPS in ingresso alla VM. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Consente di associare il gruppo di sicurezza di rete e le tabelle di route alle subnet. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Consente di creare una tabella di route per tutte le route. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Consente di creare le route per instradare il traffico tra subnet e Internet attraverso la macchina virtuale. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Consente di creare una macchina virtuale e vi connette la NIC. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell della rete virtuale sono disponibili in [Virtual network PowerShell samples](../powershell-samples.md) (Esempi di PowerShell della rete virtuale).