---
title: Convertire Azure managed archiviazione su disco da Standard a Premium a Standard o Premium | Microsoft Docs
description: Come convertire Azure managed disks da Standard a Premium o Premium a Standard utilizzando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417143"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aggiornare il tipo di archiviazione di un disco gestito

Esistono quattro tipi di dischi di Azure i dischi gestiti: SSDs Ultra-Azure (anteprima), premium SSD standard unità SSD e unità disco rigido standard. È possibile spostarsi tra i tre tipi di dischi a livello generale (premium SSD standard unità SSD e HDD standard) in base alle esigenze di prestazioni. Non si è ancora in grado di passare da o in un'unità SSD extra, è necessario distribuire uno nuovo.

Questa funzionalità non è supportata per dischi non gestiti. È possibile eseguire facilmente [convertire un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md) sia in grado di passare tra i tipi di disco.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

* Poiché la conversione richiede un riavvio della macchina virtuale (VM), è opportuno pianificare la migrazione delle risorse di archiviazione del disco durante una finestra di manutenzione preesistente.
* Se il disco non è gestito, innanzitutto [convertirlo in un disco gestito](convert-unmanaged-to-managed-disks.md) in modo che è possibile spostarsi tra le opzioni di archiviazione.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Passa tutti i dischi gestiti di una macchina virtuale tra Standard e Premium

In questo esempio viene illustrato come convertire tutti i dischi della VM da Standard ad archiviazione Premium o Premium per archiviazione Standard. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium:

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Passa i singoli dischi gestiti tra Standard e Premium

Per il carico di lavoro di sviluppo/test, è possibile utilizzare una combinazione di dischi Standard e Premium per ridurre i costi. È possibile scegliere di aggiornare solo i dischi che richiedono prestazioni migliori. In questo esempio viene illustrato come convertire un singolo disco della macchina virtuale standard all'archiviazione Premium o Premium in archiviazione Standard di. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio mostra anche come passare a una dimensione che supporta archiviazione Premium:

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Convertire i dischi gestiti da Standard a Premium nel portale di Azure

A tale scopo, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco dei **macchine virtuali** nel portale.
3. Se non viene arrestata la macchina virtuale, selezionare **arrestare** nella parte superiore della VM **Panoramica** riquadro e attesa arrestare la macchina virtuale.
3. Nel riquadro per la macchina virtuale, selezionare **dischi** dal menu di scelta.
4. Selezionare il disco che si desidera convertire.
5. Selezionare **configurazione** dal menu di scelta.
6. Modifica il **tipo di Account** dalla **Standard HDD** al **unità SSD Premium**.
7. Fare clic su **salvare**e chiudere il riquadro del disco.

La conversione del tipo di disco è istantanea. È possibile riavviare la macchina virtuale dopo la conversione.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Cambiare i dischi gestiti tra Standard HDD e SSD Standard 

In questo esempio mostra come convertire un singolo disco della macchina virtuale da Standard HDD a unità SSD Standard o da Standard SSD alle unità disco rigido Standard:

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
