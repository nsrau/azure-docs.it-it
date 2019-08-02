---
title: Convertire l'archiviazione di Azure Managed disks da standard a Premium o Premium a standard | Microsoft Docs
description: Come convertire Azure Managed disks da standard a Premium o Premium a standard usando Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fa6b005be91f47f5976dace7fd1e76f6ea7e0b29
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698854"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aggiornare il tipo di archiviazione di un disco gestito

Sono disponibili quattro tipi di disco di Azure Managed disks: Azure ultra SSD (anteprima), unità SSD Premium, unità SSD standard e HDD standard. È possibile passare tra i tre tipi di disco GA (unità SSD Premium, unità SSD standard e HDD standard) in base alle esigenze di prestazioni. Non si è ancora in grado di passare da o a un SSD Ultra, è necessario distribuirne uno nuovo.

Questa funzionalità non è supportata per i dischi non gestiti. Tuttavia, è possibile [convertire facilmente un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) per poter passare da un tipo di disco all'altra.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

* Poiché la conversione richiede un riavvio della macchina virtuale (VM), è consigliabile pianificare la migrazione dell'archiviazione su disco durante una finestra di manutenzione preesistente.
* Se il disco non è gestito, convertirlo [in un disco gestito, in](convert-unmanaged-to-managed-disks.md) modo da poter passare tra le opzioni di archiviazione.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passa a tutti i dischi gestiti di una macchina virtuale tra Premium e standard

Questo esempio illustra come convertire tutti i dischi di una macchina virtuale dall'archiviazione standard all'archiviazione Premium o da Premium ad archiviazione standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passa singoli dischi gestiti tra standard e Premium

Per il carico di lavoro di sviluppo/test, potrebbe essere necessario un insieme di dischi standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che necessitano di prestazioni migliori. Questo esempio illustra come convertire un singolo disco della macchina virtuale dall'archiviazione standard all'archiviazione Premium o da Premium ad archiviazione standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio mostra anche come passare a una dimensione che supporta archiviazione Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Convertire i dischi gestiti da standard a Premium nella portale di Azure

Attenersi ai passaggi riportati di seguito.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la VM dall'elenco di **macchine virtuali** nel portale.
3. Se la macchina virtuale non viene arrestata, selezionare **Arresta** nella parte superiore del riquadro di **Panoramica** della macchina virtuale e attendere l'arresto della macchina virtuale.
3. Nel riquadro della macchina virtuale selezionare **dischi** dal menu.
4. Selezionare il disco che si desidera convertire.
5. Selezionare **configurazione** dal menu.
6. Modificare il **tipo di account** da **HDD standard** a **SSD Premium**.
7. Fare clic su **Salva**e chiudere il riquadro del disco.

La conversione del tipo di disco è immediata. È possibile riavviare la macchina virtuale dopo la conversione.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Passa a dischi gestiti tra HDD Standard e SDD Standard 

Questo esempio illustra come convertire un singolo disco della macchina virtuale da HDD Standard a SDD Standard o da SDD Standard a HDD Standard:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando [snapshot](snapshot-copy-managed-disk.md).
