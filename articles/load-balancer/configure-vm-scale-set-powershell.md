---
title: Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente-Azure PowerShell
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510063"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando Azure PowerShell

Questo articolo illustra come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Un servizio di bilanciamento del carico SKU standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità di macchine virtuali.
- Una rete virtuale di Azure per il set di scalabilità di macchine virtuali.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere ad Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuire un set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico esistente

Sostituire i valori tra parentesi quadre con i nomi delle risorse nella configurazione.

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

L'esempio seguente distribuisce un set di scalabilità di macchine virtuali con:

- Set di scalabilità di macchine virtuali denominato **myVMSS**
- Azure Load Balancer denominato **myLoadBalancer**
- Pool back-end del servizio di bilanciamento del carico denominato **myBackendPool**
- Rete virtuale di Azure denominata **myVnet**
- **Subnet denominata subnet**
- Gruppo di risorse denominato **myResourceGroup**

```azurepowershell-interactive

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
> Dopo la creazione del set di scalabilità, la porta back-end non può essere modificata per una regola di bilanciamento del carico usata da un probe di integrità del servizio di bilanciamento del carico. Per modificare la porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali di Azure, aggiornare la porta e quindi configurare di nuovo il probe di integrità.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.  Per altre informazioni sui set di scalabilità di macchine virtuali e sul servizio di bilanciamento del carico, vedere:

- [Informazioni su Azure Load Balancer](load-balancer-overview.md)
- [Informazioni sui set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
