---
title: Ridimensionare una macchina virtuale Windows in AzureResize a Windows VM in Azure
description: Modificare le dimensioni della macchina virtuale usate per una macchina virtuale di Azure.Change the VM size used for an Azure virtual machine.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941731"
---
# <a name="resize-a-windows-vm"></a>Ridimensionare una VM Windows

Questo articolo illustra come spostare una macchina virtuale in una [macchina virtuale](sizes.md)diversa.

Dopo aver creato una macchina virtuale (VM), è possibile scalarla in verticale o in orizzontale modificandone le dimensioni. In alcuni casi, è necessario prima deallocare la macchina virtuale. Questa situazione può verificarsi se le nuove dimensioni non sono disponibili nel cluster hardware che attualmente ospita la VM.

Se la macchina virtuale usa l'archiviazione Premium, per ottenere il supporto per questo tipo di archiviazione assicurarsi di aver scelto una versione **s** delle dimensioni, ad esempio, Standard_E4**s**_v3 anziché Standard_E4_v3.

## <a name="use-the-portal"></a>Usare il portale

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Aprire la pagina per la macchina virtuale.
1. Nel menu a sinistra, selezionare **Dimensione**.
1. Selezionare una nuova dimensione dall'elenco delle dimensioni disponibili, quindi selezionare **Ridimensiona**.


Se la macchina virtuale è attualmente in esecuzione, la modifica delle dimensioni ne causerà il riavvio. L'arresto della macchina virtuale può rivelare dimensioni aggiuntive.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Usare PowerShell per ridimensionare una macchina virtuale non in un set di disponibilitàUse PowerShell to resize a VM not in an availability set

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

Se la dimensione voluta non è nell'elenco, eseguire i comandi seguenti per deallocare la VM, ridimensionarla e quindi riavviarla. Sostituire ** \<>newVMsize** con le dimensioni desiderate.
   
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

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Usare PowerShell per ridimensionare una macchina virtuale in un set di disponibilitàUse PowerShell to resize a VM in an availability set

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

Per una scalabilità aggiuntiva, eseguire più istanze di macchine virtuali e scalare orizzontalmente. Per ulteriori informazioni, vedere Ridimensionare automaticamente i computer Windows in un set di [scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

