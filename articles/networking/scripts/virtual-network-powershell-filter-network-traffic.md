---
title: 'Esempio di script di Azure PowerShell: filtrare il traffico di rete della VM | Microsoft Docs'
description: 'Esempio di script di Azure PowerShell: filtrare il traffico di rete della VM in ingresso e in uscita.'
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
ms.openlocfilehash: 168d374a070160eff158d6464d6e214880f486f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156896"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrare il traffico della VM in ingresso e in uscita

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP e HTTPS, mentre il traffico in uscita verso Internet dalla subnet back-end non è consentito. Dopo aver eseguito lo script sarà presente una macchina virtuale con due NIC. Ogni NIC è collegata a una subnet diversa.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/filter-network-traffic/filter-network-traffic.ps1  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea un oggetto di configurazione di subnet |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Crea regole di sicurezza da assegnare a un gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Consente di creare regole del gruppo di sicurezza di rete che consentono o bloccano porte specifiche su subnet specifiche. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Consente di associare i gruppi di risorse di rete alla subnet. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Consente di creare un indirizzo IP pubblico per accedere alla VM da Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Consente di creare interfacce di rete virtuale e di associarle alle subnet front-end e back-end della rete virtuale. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
