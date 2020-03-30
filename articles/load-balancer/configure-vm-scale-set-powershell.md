---
title: Configurare il set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente - Azure PowerShellConfigure virtual machine scale set with an existing Azure Load Balancer - Azure PowerShell
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/26/2020
ms.openlocfilehash: 0db09083a2197ce72e6d6eed2381b0308239586e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350001"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente usando Azure PowerShellConfigure a virtual machine scale set with an existing Azure Load Balancer using Azure PowerShell

In questo articolo verrà illustrato come configurare un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Un servizio di bilanciamento del carico sku standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità della macchina virtuale.
- Una rete virtuale di Azure per il set di scalabilità della macchina virtuale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere ad Azure.Sign into Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuire un set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico esistenteDeploy a virtual machine scale set with existing load balancer

Sostituire i valori tra parentesi con i nomi delle risorse nella configurazione.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

L'esempio seguente distribuisce un set di scalabilità di macchine virtuali con:The below example deploys a virtual machine scale set with:

- Set di scalabilità delle macchine virtuali denominato **myVMSSVirtual** machine scale set named myVMSS
- Servizio di bilanciamento del carico di Azure denominato **myLoadBalancerAzure** Load Balancer named myLoadBalancer
- Pool back-end del servizio di bilanciamento del carico denominato **myBackendPool**
- Rete virtuale di Azure denominata **myVnetAzure** Virtual Network named myVnet
- Subnet denominata **mySubnet**
- Gruppo di risorse denominato **myResourceGroup**

```azureppowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> Dopo la creazione del set di scalabilità, la porta back-end non può essere modificata per una regola di bilanciamento del carico usata da un probe di integrità del servizio di bilanciamento del carico. Per modificare la porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità della macchina virtuale di Azure, aggiornare la porta e quindi configurare nuovamente il probe di integrità.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un set di scalabilità di macchine virtuali con un servizio di bilanciamento del carico di Azure esistente.  Per altre informazioni sui set di scalabilità delle macchine virtuali e sul servizio di bilanciamento del carico, vedere:To learn more about virtual machine scale sets and load balancer, see:

- [Informazioni su Azure Load Balancer](load-balancer-overview.md)
- [Informazioni sui set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
                                