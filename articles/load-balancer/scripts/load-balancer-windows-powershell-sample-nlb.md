---
title: Esempio di PowerShell - Bilanciare il carico del traffico verso le macchine virtuali per la disponibilità elevata - Azure
titlesuffix: Azure Load Balancer
description: Questo esempio di script di Azure PowerShell illustra come bilanciare il carico del traffico verso le macchine virtuali per la disponibilità elevata
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 70752f9726ac78ec32d0735dfefe37675ae7be29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66170649"
---
# <a name="azure-powershell-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Esempio di script di Azure PowerShell: Eseguire il bilanciamento del carico per il traffico verso le macchine virtuali per la disponibilità elevata

Questo script di esempio di Azure PowerShell crea tutti gli elementi necessari per eseguire più macchine virtuali Windows configurate in una configurazione a disponibilità elevata e con bilanciamento del carico. Dopo aver eseguito lo script, si disporrà di tre macchine virtuali, aggiunte a un set di disponibilità di Azure e accessibili tramite Azure Load Balancer.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Crea una configurazione di subnet. Questa configurazione viene usata con il processo di creazione della rete virtuale. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Consente di creare una rete virtuale e una subnet di Azure. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Consente di creare un indirizzo IP pubblico con un indirizzo IP statico e un nome DNS associato. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Crea un servizio di bilanciamento del carico di Azure. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Crea un probe di bilanciamento del carico. Il probe di bilanciamento del carico viene usato per monitorare tutte le macchine virtuali nel set di bilanciamento del carico. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Crea una regola del bilanciamento del carico. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al bilanciamento del carico, viene indirizzato sulla porta 80 di una delle macchine virtuali presenti nel set di bilanciamento del carico. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Crea una regola Network Address Translation (NAT) di bilanciamento del carico.  Le regole NAT mappano una porta del bilanciamento del carico su una porta in una macchina virtuale. In questo esempio viene creata una regola NAT per il traffico SSH in ogni macchina virtuale disponibile nel set di bilanciamento del carico.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Consente di creare un gruppo di sicurezza di rete, ovvero un confine di sicurezza tra Internet e la macchina virtuale. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Consente di creare una regola NSG per consentire il traffico in ingresso. In questo esempio, la porta 22 è aperta al traffico SSH. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Consente di creare una scheda di rete virtuale e la collega alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Consente di creare un set di disponibilità. I set di disponibilità garantiscono il tempo di attività dell'applicazione suddividendo le macchine virtuali in risorse fisiche in modo tale che, in caso di errore, non venga interessato l'intero set. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Crea una configurazione di VM. Questa configurazione include informazioni quali il nome della VM, il sistema operativo e le credenziali amministrative. La configurazione viene usata durante la creazione della VM. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
