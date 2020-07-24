---
title: Anteprima dei gruppi di posizionamento di prossimità per set di scalabilità di macchine virtuali
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per i set di scalabilità di macchine virtuali Windows in Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: 1525ef7c1e19aa9821df3f111fdce3518d37afd5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011057"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Anteprima: Creazione e uso di gruppi di posizionamento di prossimità con PowerShell

Se le macchine virtuali sono molto vicine, è possibile mantenere molto bassa la latenza. Per farlo, distribuire il set di scalabilità in un [gruppo di posizionamento di prossimità](co-location.md#preview-proximity-placement-groups).

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino una vicino all'altra. I gruppo di posizionamento di prossimità sono utili per i carichi di lavoro che richiedono una latenza ridotta.

> [!IMPORTANT]
> L'impostazione per Gruppi di posizionamento di prossimità è attualmente Anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> I gruppi di posizionamento di prossimità non sono disponibili in queste aree durante l'anteprima: **Giappone orientale**, **Australia orientale** e **India centrale**.


## <a name="create-a-proximity-placement-group"></a>Creare un gruppo di selezione host di prossimità
Creare un gruppo di posizionamento di prossimità usando il cmdlet [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup). 

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

## <a name="list-proximity-placement-groups"></a>Elencare i gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento di prossimità usando il cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Creare un set di scalabilità

Quando si usa [New-AzVMSS](/powershell/module/az.compute/new-azvmss) per creare un set di scalabilità nel gruppo di posizionamento di prossimità, usare `-ProximityPlacementGroup $ppg.Id` come riferimento all'ID del gruppo.

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

È possibile visualizzare l'istanza nel gruppo di posizionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Passaggi successivi

Per creare gruppo di posizionamento di prossimità, è anche possibile usare l'[interfaccia della riga di comando di Azure](../virtual-machines/linux/proximity-placement-groups.md).
