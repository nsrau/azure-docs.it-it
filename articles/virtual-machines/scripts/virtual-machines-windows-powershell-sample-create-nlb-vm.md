---
title: Esempio di script di Azure PowerShell - Creare una VM Windows NLB | Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare una VM Windows NLB
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 18bf494ceb212de07b3e7ee1e0fa0e4de89cb07f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976753"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Bilanciare il carico del traffico tra macchine virtuali a disponibilità elevata

Questo script di esempio crea tutti gli elementi necessari per eseguire più macchine virtuali Windows Server 2016 configurate in una configurazione a disponibilità elevata e con bilanciamento del carico. Dopo aver eseguito lo script, si disporrà di tre macchine virtuali, aggiunte a un set di disponibilità di Azure e accessibili tramite Azure Load Balancer.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | Crea una rete virtuale. |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Crea un indirizzo IP pubblico. |
| [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Crea una configurazione IP front-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Crea una configurazione del pool di indirizzi back-end per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) | Crea la configurazione di una porta probe per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) | Crea la configurazione di una regola per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Crea la configurazione di una regola NAT in ingresso per un servizio di bilanciamento del carico. |
| [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) | Crea un servizio di bilanciamento del carico. |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) | Crea una configurazione di regola del gruppo di sicurezza di rete. Questa configurazione viene usata per creare una regola NSG quando viene creato il gruppo di sicurezza di rete. |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) | Crea un gruppo di sicurezza di rete. |
| [Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Ottiene informazioni sulla subnet. Queste informazioni vengono usate durante la creazione di un'interfaccia di rete. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Crea un'interfaccia di rete. |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Creare una macchina virtuale. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di script PowerShell della macchina virtuale sono reperibili nella [documentazione della VM Windows di Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
