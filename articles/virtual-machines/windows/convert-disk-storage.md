---
title: Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa | Microsoft Docs
description: Come convertire Azure Managed Disks da Standard a Premium e viceversa usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 6cfbc458a4bd751b8b871501d19db641e3980767
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719325"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa

Managed Disks offre tre opzioni di archiviazione: [Premium SSD](../windows/premium-storage.md), Standard SSD (anteprima) e [Standard HDD](../windows/standard-storage.md). È possibile passare facilmente tra le opzioni con un tempo di inattività minimo a seconda delle esigenze di prestazioni. Non è supportato per dischi non gestiti. È possibile eseguire facilmente la [conversione a Managed Disks](convert-unmanaged-to-managed-disks.md) per passare facilmente tra i tipi di dischi.

Questo articolo illustra come convertire la versione di Managed Disks da Standard a Premium e viceversa usando Azure PowerShell. Se è necessario eseguirne l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="before-you-begin"></a>Prima di iniziare

* La conversione richiede un riavvio della VM, quindi pianificare la migrazione dell'archiviazione su dischi durante una finestra di manutenzione preesistente. 
* Se si usano dischi non gestiti, prima di tutto eseguire la [conversione a Managed Disks](convert-unmanaged-to-managed-disks.md) per passare tra un'opzione di archiviazione e l'altra, come indicato in questo articolo. 
* Questo articolo richiede il modulo Azure PowerShell 6.0.0 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). È anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Convertire tutte le istanze di Managed Disks di una VM da Standard a Premium e viceversa

L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium per tutti i dischi di una macchina virtuale. Per usare i dischi gestiti Premium, la VM deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Convertire un disco gestito da Standard a Premium e viceversa

Per il carico di lavoro di sviluppo/test, potrebbe essere necessaria una combinazione di dischi Standard e Premium, per ridurre i costi. A tale scopo, è possibile eseguire l'aggiornamento ad Archiviazione Premium solo per i dischi che richiedono prestazioni migliori. L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium e viceversa per un singolo disco di una macchina virtuale. Per usare i dischi gestiti Premium, la VM deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Convertire un disco gestito da Standard HDD a Standard SSD e viceversa

L'esempio seguente illustra come passare da Standard HDD a Standard SSD e viceversa per un singolo disco di una macchina virtuale.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una VM usando [snapshot](snapshot-copy-managed-disk.md).

