---
title: Modificare un set di disponibilità per VM | Microsoft Docs
description: Informazioni su come modificare il set di disponibilità per le macchine virtuali tramite Azure PowerShell e il modello di distribuzione Resource Manager.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: zarhoads
ms.openlocfilehash: df59b3ec7da0c12a17e10790d69e244f9a0d03f5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259531"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Modificare il set di disponibilità per una VM Windows
I passaggi seguenti descrivono come modificare il set di disponibilità di una VM tramite Azure PowerShell. Una VM può essere aggiunta a un set di disponibilità solo in fase di creazione. Per modificare il set di disponibilità, è necessario eliminare e quindi ricreare la macchina virtuale. 

## <a name="change-the-availability-set"></a>Modificare il set di disponibilità 

Lo script seguente offre un esempio di raccolta delle informazioni necessarie ed eliminazione e ricreazione della VM originale in un nuovo set di disponibilità.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzureRmVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzureRmAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzureRmVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzureRmVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s)
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {
        Add-AzureRmVMNetworkInterface `
           -VM $newVM `
           -Id $nic.Id
    }

# Recreate the VM
    New-AzureRmVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Passaggi successivi

Aumentare lo spazio di archiviazione della VM aggiungendo un ulteriore [disco dati](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

