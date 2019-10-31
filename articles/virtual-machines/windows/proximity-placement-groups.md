---
title: Usare i gruppi di posizionamento di prossimità per le macchine virtuali Windows
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per le macchine virtuali Windows in Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 6d0c35737151b060dcffba8944f4a1361d36dc14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171213"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando PowerShell


Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](co-location.md#proximity-placement-groups).

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.


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


## <a name="create-a-vm"></a>Creare una VM

Creare una macchina virtuale nel gruppo di posizionamento di prossimità usando `-ProximityPlacementGroup $ppg.Id` per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si usa [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) per creare la macchina virtuale.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

È possibile visualizzare la macchina virtuale nel gruppo di posizionamento usando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso parametro di `-ProximityPlacementGroup` con il cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) per creare un set di disponibilità e tutte le macchine virtuali create nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso parametro `-ProximityPlacementGroup` con [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare l' [interfaccia](../linux/proximity-placement-groups.md) della riga di comando di Azure per creare gruppi di posizionamento di prossimità.
