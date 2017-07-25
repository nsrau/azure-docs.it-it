---
title: 'Convertire i dischi di una macchina virtuale Windows da non gestiti a gestiti: Azure | Microsoft Docs'
description: Come convertire i dischi non gestiti di una macchina virtuale Windows per Azure Managed Disks usando PowerShell nel modello di distribuzione Resource Manager
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Convertire i dischi non gestiti di una macchina virtuale Windows per Azure Managed Disks

Se sono presenti macchine virtuali (VM) Windows che usano dischi non gestiti, è possibile convertire le VM per l'uso di [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Questo articolo illustra come convertire le VM con Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Prima di iniziare


* Vedere [Pianificare la migrazione a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le VM sono in un set di disponibilità, vedere la sezione successiva. 

1. Deallocare la VM con il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`: 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertire i dischi della VM in gestiti con il cmdlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Il processo seguente converte la macchina virtuale precedente, incluso il disco del sistema operativo ed eventuali dischi dati:

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Avviare la macchina virtuale dopo la conversione in dischi gestiti con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). L'esempio seguente riavvia la VM precedente:

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

1. Convertire il set di disponibilità con il cmdlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L'esempio seguente aggiorna il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Se l'area in cui si trova il set di disponibilità ha solo 2 domini di errore gestiti, ma il numero di domini di errore non gestiti è 3, questo comando visualizza un errore simile a "Il conteggio del dominio di errore specificato 3 deve essere compreso nell'intervallo 1-2". Per correggere l'errore, impostare il dominio di errore su 2 e impostare `Sku` su `Aligned` come segue:

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Deallocare e convertire le VM nel set di disponibilità. Lo script seguente dealloca ogni VM con il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la converte con [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) e la riavvia con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Convertire i dischi gestiti da standard a Premium
Dopo avere convertito i dischi della VM in gestiti, è anche possibile passare da un tipo di archiviazione a un altro. È inoltre possibile avere una combinazione di dischi che usi l'archiviazione Premium e quella standard. Nell'esempio seguente viene illustrato come passare all'archiviazione Premium da quella standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio passa anche a dimensioni che supportano l'archiviazione Premium.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la conversione o se una VM si trova in uno stato di errore a causa di problemi in una conversione precedente, eseguire di nuovo il cmdlet `ConvertTo-AzureRmVMManagedDisk`. In genere è sufficiente riprovare per sbloccare la situazione.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Dischi gestiti e Crittografia del servizio di archiviazione di Azure

Non è possibile usare i passaggi precedenti per convertire un disco non gestito in un disco gestito se il disco non gestito si trova in un account di archiviazione che è stato crittografato con [Crittografia del servizio di archiviazione di Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). La procedura seguente illustra come copiare e usare dischi non gestiti che sono stati in un account di archiviazione crittografato:

1. Copiare il disco rigido virtuale con [AzCopy](../../storage/storage-use-azcopy.md) in un account di archiviazione che non è mai stato abilitato per la crittografia del servizio di archiviazione di Azure.

2. Usare la VM copiata in uno dei modi seguenti:

  * Creare una macchina virtuale che usi dischi gestiti e specificare il file del disco rigido virtuale durante la creazione con `New-AzureRmVm`

  * Collegare il disco rigido virtuale copiato con `Add-AzureRmVmDataDisk` a una macchina virtuale in esecuzione con dischi gestiti


## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando [snapshot](snapshot-copy-managed-disk.md).


