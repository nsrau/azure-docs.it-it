---
title: Convertire l'archiviazione di Azure Managed Disks da Standard a Premium e viceversa | Microsoft Docs
description: Come convertire Azure Managed Disks da Standard a Premium e viceversa usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 18258bf8ac9d241fd8a01957d903b1db882c2d36
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326875"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aggiornare il tipo di archiviazione di un disco gestito

Azure Managed Disks offre quattro tipi di archiviazione: unità SSD Ultra, SSD Premium, SSD Standard e unità disco rigido (HDD) standard. È possibile spostare un disco gestito tra i vari tipi di archiviazione con un tempo di inattività minimo, a seconda delle esigenze di prestazioni. Questa operazione non è supportata per i dischi non gestiti. È tuttavia possibile [convertire facilmente un disco non gestito in un disco gestito](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Prerequisiti

* La conversione richiede un riavvio della macchina virtuale ed è quindi necessario pianificare la migrazione dell'archiviazione su dischi durante una finestra di manutenzione preesistente. 
* Se si usa un disco non gestito, per prima cosa [convertirlo in un disco gestito](convert-unmanaged-to-managed-disks.md) per consentirne lo spostamento tra i tipi di archiviazione. 
* Gli esempi di questo articolo richiedono la versione 6.0.0 o successiva del modulo di Azure PowerShell. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Eseguire [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) per creare una connessione con Azure.

* Gli esempi di questo articolo richiedono la versione 6.0.0 o successiva del modulo di Azure PowerShell. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Eseguire [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) per creare una connessione con Azure.

## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Convertire tutte le istanze di Managed Disks di una macchina virtuale da Standard a Premium

L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium per tutti i dischi di una macchina virtuale. Per poter usare dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. In questo esempio si passa anche a una dimensione che supporta l'archiviazione Premium:

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
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
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

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Convertire un disco gestito da Standard a Premium

Per il carico di lavoro di sviluppo/test, potrebbe essere necessaria una combinazione di dischi Standard e Premium, per ridurre i costi. A tale scopo, è possibile eseguire l'aggiornamento ad Archiviazione Premium solo per i dischi che richiedono prestazioni migliori. L'esempio seguente illustra come passare dall'archiviazione Standard all'archiviazione Premium e viceversa per un singolo disco di una macchina virtuale. Per poter usare dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](sizes.md) da supportare l'archiviazione Premium. Questo esempio illustra anche come passare a una dimensione che supporta l'archiviazione Premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Convertire un disco gestito da Standard HDD a Standard SSD

L'esempio seguente illustra come passare da Standard HDD a Standard SSD e viceversa per un singolo disco di una macchina virtuale:

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

