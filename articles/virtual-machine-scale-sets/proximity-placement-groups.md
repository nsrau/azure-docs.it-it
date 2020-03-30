---
title: Anteprima dei gruppi di posizionamento di prossimità per i set di scalabilità di macchine virtualiProximity placement groups preview for virtual machine scale sets
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per i set di scalabilità di macchine virtuali Windows in Azure.Learn about creating and using proximity placement groups for Windows virtual machine scale sets in Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 4fa2949e2a7e1b99ac26caa35f967e9dc9cf359a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273611"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Anteprima: Creazione e utilizzo di gruppi di posizionamento di prossimità tramite PowerShellPreview: Creating and using proximity placement groups using PowerShell

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuire il set di scalabilità all'interno di un gruppo di posizionamento di [prossimità.](co-location.md#preview-proximity-placement-groups)

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine l'una all'altra. I gruppi di posizionamento di prossimità sono utili per i carichi di lavoro in cui una bassa latenza è un requisito.

> [!IMPORTANT]
> I gruppi di posizionamento di prossimità sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> I gruppi di posizionamento di prossimità non sono disponibili in queste aree geografiche durante l'anteprima: **Giappone orientale,** **Australia orientale** e India **centrale**.


## <a name="create-a-proximity-placement-group"></a>Creare un gruppo di selezione host di prossimità
Creare un gruppo di posizionamento di prossimità utilizzando il cmdlet [New-AzProximityPlacementGroup.Create](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) a proximity placement group using the New-AzProximityPlacementGroup cmdlet. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Elenca gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento di prossimità utilizzando il cmdlet [Get-AzProximityPlacementGroup.](/powershell/module/az.compute/get-azproximityplacementgroup)

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Creare un set di scalabilità

Creare una scala nel gruppo `-ProximityPlacementGroup $ppg.Id` di posizionamento di prossimità utilizzando per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si utilizza [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) per creare il set di scalabilità.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

È possibile visualizzare l'istanza nel gruppo di posizionamento utilizzando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [l'interfaccia della riga di](../virtual-machines/linux/proximity-placement-groups.md) comando di Azure per creare gruppi di posizionamento di prossimità.
