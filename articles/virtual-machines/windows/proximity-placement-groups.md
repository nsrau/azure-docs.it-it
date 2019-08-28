---
title: Usare i gruppi di posizionamento di prossimità per le macchine virtuali Windows | Microsoft Docs
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per le macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: af75b3f98232d6507fc8b0fda179bebc75828086
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088829"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>Anteprima: Distribuire macchine virtuali in gruppi di posizionamento di prossimità usando PowerShell


Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](co-location.md#preview-proximity-placement-groups).

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


## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale nel gruppo di posizionamento di `-ProximityPlacementGroup $ppg.Id` prossimità usando per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si usa [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) per creare la macchina virtuale.

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
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso `-ProximityPlacementGroup` parametro con il cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) per creare un set di disponibilità e tutte le macchine virtuali create nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso `-ProximityPlacementGroup` parametro con [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare l' [interfaccia](../linux/proximity-placement-groups.md) della riga di comando di Azure per creare gruppi di posizionamento di prossimità.
