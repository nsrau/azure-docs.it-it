---
title: 'PowerShell: Usare gruppi di posizionamento di prossimitàPowerShell: Use proximity placement groups'
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità tramite Azure PowerShell.Learn about creating and using proximity placement groups using Azure PowerShell.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208526"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>Distribuire macchine virtuali a gruppi di posizionamento di prossimità tramite PowerShellDeploy VMs to proximity placement groups using PowerShell


Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuirle all'interno di un gruppo di [posizionamento di prossimità.](co-location.md#proximity-placement-groups)

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine l'una all'altra. I gruppi di posizionamento di prossimità sono utili per i carichi di lavoro in cui una bassa latenza è un requisito.


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


## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale nel `-ProximityPlacementGroup $ppg.Id` gruppo di posizionamento di prossimità usando per fare riferimento all'ID del gruppo di posizionamento di prossimità quando si usa [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) per creare la macchina virtuale.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

È possibile visualizzare la macchina virtuale nel gruppo di posizionamento utilizzando [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>Spostare una macchina virtuale esistente in un gruppo di posizionamento di prossimitàMove an existing VM into a proximity placement group

È anche possibile aggiungere una macchina virtuale esistente a un gruppo di posizionamento di prossimità. È necessario arrestare e deallocare prima la macchina virtuale, quindi aggiornare la macchina virtuale e riavviarla.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>Spostare una macchina virtuale esistente da un gruppo di posizionamento di prossimitàMove an existing VM out of a proximity placement group

Per rimuovere una macchina virtuale da un gruppo di posizionamento di prossimità, è necessario arrestare e deallocare prima la macchina virtuale, quindi aggiornare la macchina virtuale e riavviarla.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo `-ProximityPlacementGroup` stesso parametro con il cmdlet [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) per creare un set di disponibilità e tutte le macchine virtuali create nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

Per aggiungere o rimuovere un set di disponibilità esistente a un gruppo di posizionamento di prossimità, è innanzitutto necessario arrestare tutte le macchine virtuali nel set di disponibilità. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>Spostare un set di disponibilità esistente in un gruppo di posizionamento di prossimitàMove an existing availability set into a proximity placement group

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>Spostare un set di disponibilità esistente da un gruppo di posizionamento di prossimitàMove an existing availability set out of a proximity placement group

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>Set di scalabilità

Puoi anche creare un set di scalabilità nel tuo gruppo di posizionamento di prossimità. Usare lo `-ProximityPlacementGroup` stesso parametro con [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.


Per aggiungere o rimuovere un set di scalabilità esistente a un gruppo di posizionamento di prossimità, è innanzitutto necessario arrestare il set di scalabilità. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>Spostare un set di scalabilità esistente in un gruppo di posizionamento di prossimità

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>Spostare una scala esistente impostata da un gruppo di posizionamento di prossimità

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile usare [l'interfaccia della riga di](../linux/proximity-placement-groups.md) comando di Azure per creare gruppi di posizionamento di prossimità.
