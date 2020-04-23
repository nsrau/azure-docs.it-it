---
title: Ridimensionare una macchina virtuale Windows in Azure
description: Modificare le dimensioni della VM usate per una macchina virtuale di Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: sizes
ms.workload: infrastructure
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: f456af143ac6ec21bcb9b0c3ec75635c51f748ef
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099887"
---
# <a name="resize-a-windows-vm"></a>Ridimensionare una VM Windows

Questo articolo illustra come spostare una macchina virtuale in una [macchina virtuale di dimensioni](sizes.md)diverse.

Dopo aver creato una macchina virtuale (VM), è possibile scalarla in verticale o in orizzontale modificandone le dimensioni. In alcuni casi, è necessario prima deallocare la macchina virtuale. Questa situazione può verificarsi se le nuove dimensioni non sono disponibili nel cluster hardware che attualmente ospita la VM.

Se la macchina virtuale usa l'archiviazione Premium, per ottenere il supporto per questo tipo di archiviazione assicurarsi di aver scelto una versione **s** delle dimensioni, ad esempio, Standard_E4**s**_v3 anziché Standard_E4_v3.

## <a name="use-the-portal"></a>Usare il portale

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Aprire la pagina per la macchina virtuale.
1. Nel menu a sinistra selezionare **dimensioni**.
1. Scegliere una nuova dimensione dall'elenco delle dimensioni disponibili e quindi fare clic su **Ridimensiona**.


Se la macchina virtuale è attualmente in esecuzione, la modifica delle dimensioni ne determinerà il riavvio. L'arresto della macchina virtuale può rivelare dimensioni aggiuntive.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Usare PowerShell per ridimensionare una macchina virtuale non in un set di disponibilità

Impostare alcune variabili. Sostituire i valori con i propri.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Elencare le dimensioni di VM disponibili nel cluster hardware in cui la VM è ospitata. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se la dimensione voluta è inclusa nell'elenco, per ridimensionare la VM eseguire i comandi seguenti. Se la dimensione desiderata non è inclusa nell'elenco, andare al passaggio 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Se la dimensione voluta non è nell'elenco, eseguire i comandi seguenti per deallocare la VM, ridimensionarla e quindi riavviarla. Sostituire ** \<newVMsize>** con le dimensioni desiderate.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> La deallocazione della VM rilascia qualsiasi indirizzo IP dinamico assegnato alla VM. I dischi del sistema operativo e dei dati non sono coinvolti. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Usare PowerShell per ridimensionare una macchina virtuale in un set di disponibilità

Se la nuova dimensione di una VM inclusa in un set di disponibilità non è disponibile nel cluster hardware che attualmente ospita la VM in questione, per ridimensionare tale VM sarà necessario deallocare tutte le VM incluse nel set di disponibilità. Dopo il ridimensionamento di una VM, può inoltre essere necessario aggiornare le dimensioni delle altre VM incluse nel gruppo di disponibilità. Per ridimensionare una VM inclusa in un gruppo di disponibilità, seguire questa procedura.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Elencare le dimensioni di VM disponibili nel cluster hardware in cui la VM è ospitata. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Se la dimensione desiderata è inclusa nell'elenco, per ridimensionare la VM eseguire i comandi seguenti. Se non è inclusa nell'elenco, passare alla prossima sezione.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Se la dimensione voluta non è elencata, seguire la procedura seguente per deallocare tutte le VM incluse nel set di disponibilità, ridimensionare le VM e quindi riavviarle.

Arrestare tutte le VM nel set di disponibilità.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Ridimensionare e riavviare tutte le VM nel set di disponibilità.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Passaggi successivi

Per una maggiore scalabilità, eseguire più istanze di VM e scalare in orizzontale. Per altre informazioni, vedere [ridimensionare automaticamente i computer Windows in un set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

