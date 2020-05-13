---
title: Anteprima dei gruppi di posizionamento di prossimità per i set di scalabilità di macchine virtuali
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per i set di scalabilità di macchine virtuali Windows in Azure.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 07/01/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 18cb1ae3e549995d7b4732025906329bc609f360
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124347"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Anteprima: creazione e uso di gruppi di posizionamento di prossimità con PowerShell

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuire il set di scalabilità all'interno di un [gruppo di posizionamento di prossimità](co-location.md#preview-proximity-placement-groups).

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.

> [!IMPORTANT]
> I gruppi di posizionamento di prossimità sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> I gruppi di posizionamento di prossimità non sono disponibili in queste aree durante l'anteprima: **Giappone orientale**, **Australia orientale** e **India centrale**.


## <a name="create-a-proximity-placement-group"></a>Creare un gruppo di selezione host di prossimità
Creare un gruppo di posizionamento di prossimità usando il cmdlet [New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) . 

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

## <a name="list-proximity-placement-groups"></a>Elenca gruppi di posizionamento prossimità

È possibile elencare tutti i gruppi di posizionamento vicini usando il cmdlet [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Creare un set di scalabilità

Creare una scala nel gruppo di posizionamento di prossimità usando `-ProximityPlacementGroup $ppg.Id` per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si usa [New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) per creare il set di scalabilità.

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

È anche possibile usare l' [interfaccia](../virtual-machines/linux/proximity-placement-groups.md) della riga di comando di Azure per creare gruppi di posizionamento di prossimità.
