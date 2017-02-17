---
title: Usare PowerShell per ridimensionare una macchina virtuale Windows in Azure | Documentazione Microsoft
description: Ridimensionare una macchina virtuale Windows creata nel modello di distribuzione Resource Manager usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 6b80fa78fea505cd22bae2925ce1affbc1e48ab9
ms.openlocfilehash: 3112be84acd3f3e11252270c850955068d35029e


---
# <a name="resize-a-windows-vm"></a>Ridimensionare una VM Windows
Questo articolo illustra come ridimensionare una VM Windows creata nel modello di distribuzione Resource Manager usando Azure PowerShell.

Dopo aver creato una macchina virtuale (VM), è possibile scalarla in verticale o in orizzontale modificandone le dimensioni. In alcuni casi, è necessario prima deallocare la macchina virtuale. Questa situazione può verificarsi se le nuove dimensioni non sono disponibili nel cluster hardware che attualmente ospita la VM.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ridimensionare una VM Windows non inclusa in un set di disponibilità
1. Elencare le dimensioni di VM disponibili nel cluster hardware in cui la VM è ospitata. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Se la dimensione desiderata è inclusa nell'elenco, per ridimensionare la VM eseguire i comandi seguenti. Se la dimensione desiderata non è inclusa nell'elenco, andare al passaggio 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Se la dimensione desiderata non è nell'elenco, eseguire i comandi seguenti per deallocare la VM, ridimensionarla e quindi riavviare la VM.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> La deallocazione della VM rilascia qualsiasi indirizzo IP dinamico assegnato alla VM. I dischi del sistema operativo e dei dati non sono coinvolti. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ridimensionare una VM Windows inclusa in un set di disponibilità
Se la nuova dimensione di una VM inclusa in un set di disponibilità non è disponibile nel cluster hardware che attualmente ospita la VM in questione, per ridimensionare tale VM sarà necessario deallocare tutte le VM incluse nel set di disponibilità. Dopo il ridimensionamento di una VM, può inoltre essere necessario aggiornare le dimensioni delle altre VM incluse nel gruppo di disponibilità. Per ridimensionare una VM inclusa in un gruppo di disponibilità, seguire questa procedura.

1. Elencare le dimensioni di VM disponibili nel cluster hardware in cui la VM è ospitata.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Se la dimensione desiderata è inclusa nell'elenco, per ridimensionare la VM eseguire i comandi seguenti. Se non è inclusa nell'elenco, andare al passaggio 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Se la dimensione desiderata non è elencata, seguire questa procedura per deallocare tutte le VM incluse nel set di disponibilità, ridimensionare le VM e quindi riavviarle.
4. Arrestare tutte le VM nel set di disponibilità.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Ridimensionare e riavviare tutte le VM nel set di disponibilità.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Passaggi successivi
* Per una maggiore scalabilità, eseguire più istanze di VM e la scalabilità orizzontale. Per altre informazioni, vedere [Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).




<!--HONumber=Jan17_HO4-->


