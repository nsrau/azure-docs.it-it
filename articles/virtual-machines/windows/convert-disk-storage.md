---
title: Convertire lo spazio di archiviazione dei dischi gestiti tra SSD standard e premiumConvert managed disks storage between standard and premium SSD
description: Come convertire i dischi gestiti di Azure da Standard a Premium o Premium a Standard tramite Azure PowerShell.How to convert Azure managed disks from Standard or Premium to Standard by using Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720946"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aggiornare il tipo di archiviazione di un disco gestito

Esistono quattro tipi di dischi di dischi gestiti da Azure: Azure ultra SSD (anteprima), SSD premium, SSD standard e HDD standard. Puoi passare da un tipo di disco GA all'altro (SSD premium, SSD standard e HDD standard) in base alle tue esigenze di prestazioni. Non è ancora possibile passare da o verso un ultra SSD, è necessario distribuirne uno nuovo.

Questa funzionalità non è supportata per i dischi non gestiti. Tuttavia, è possibile convertire facilmente [un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) per poter passare da un tipo di disco all'altro.

 

## <a name="prerequisites"></a>Prerequisiti

* Poiché la conversione richiede il riavvio della macchina virtuale (VM), è necessario pianificare la migrazione dello spazio di archiviazione su disco durante una finestra di manutenzione preesistente.
* Se il disco non è gestito, [convertilo](convert-unmanaged-to-managed-disks.md) in un disco gestito in modo da poter passare da un'opzione di archiviazione all'altra.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passare tutti i dischi gestiti di una macchina virtuale tra Premium e StandardSwitch all managed disks of a VM between Premium and Standard

Questo esempio mostra come convertire tutti i dischi di una macchina virtuale dall'archiviazione Standard a Premium o da Premium all'archiviazione Standard.This example shows how to convert all of a VM's disks from Standard to Premium storage or from Premium to Standard storage. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium:

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passare da un dischi gestiti all'altro tra Standard e Premium

Per il carico di lavoro di sviluppo/test, è possibile che si desideri una combinazione di dischi Standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che richiedono prestazioni migliori. Questo esempio mostra come convertire un singolo disco VM dall'archiviazione Standard a Premium o da Premium all'archiviazione Standard.This example shows how to convert a single VM disk from Standard to Premium storage or from Premium to Standard storage. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio viene inoltre illustrato come passare a una dimensione che supporta l'archiviazione Premium:This example also shows how to switch to a size that supports Premium storage:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Convertire i dischi gestiti da Standard a Premium nel portale di AzureConvert managed disks from Standard to Premium in the Azure portal

A tale scopo, seguire questa procedura:

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare la macchina virtuale dall'elenco delle **macchine virtuali** nel portale.
3. Se la macchina virtuale non è arrestata, selezionare **Interrompi** nella parte superiore del riquadro **Panoramica** macchina virtuale e attendere l'arresto della macchina virtuale.
3. Nel riquadro della macchina virtuale selezionare **Dischi** dal menu.
4. Selezionare il disco che si desidera convertire.
5. Selezionare **Configurazione** dal menu.
6. Modificare il **tipo di account** da **HDD standard** a **SSD Premium**.
7. Fare clic su **Salva**e chiudere il riquadro del disco.

La conversione del tipo di disco è istantanea. È possibile avviare la macchina virtuale dopo la conversione.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Commutazione di dischi gestiti tra HDD standard e SSD standard 

Questo esempio mostra come convertire un singolo disco VM da HDD standard a SSD standard o da SSD standard a HDD standard:This example shows how to convert a single VM disk from Standard HDD to Standard SSD or from Standard SSD to Standard HDD:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando [snapshot](snapshot-copy-managed-disk.md).
