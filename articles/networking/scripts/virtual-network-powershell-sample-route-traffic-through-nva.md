---
title: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete | Microsoft Docs
description: Esempio di script di Azure PowerShell - Instradare il traffico attraverso un'appliance virtuale di rete di tipo firewall.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: b596431a8d492316c7bacf65c9c0d5805030e621
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650854"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Crea anche una VM con inoltro IP attivato per instradare il traffico tra le due subnet. Dopo aver eseguito lo script è possibile distribuire il software di rete, ad esempio un'applicazione firewall, nella VM.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Consente di creare le subnet back-end e di rete perimetrale. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla VM da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Consente di creare un'interfaccia di rete virtuale e attiva l'inoltro IP. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Consente di creare un gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Consente di creare regole del gruppo di sicurezza di rete per le porte HTTP e HTTPS in ingresso alla VM. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Consente di associare il gruppo di sicurezza di rete e le tabelle di route alle subnet. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Consente di creare una tabella di route per tutte le route. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Consente di creare le route per instradare il traffico tra subnet e Internet attraverso la VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Consente di creare una macchina virtuale e vi connette la NIC. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
