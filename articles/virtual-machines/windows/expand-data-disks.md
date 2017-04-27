---
title: Espandere un disco dati collegato a una macchina virtuale Windows in Azure | Microsoft Docs
description: Espandere le dimensioni di un disco dati collegato a una macchina virtuale Windows con PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 620a28f4fb4421179c0ba030c10acba861760adf
ms.lasthandoff: 04/21/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Aumentare le dimensioni di un disco dati collegato a una macchina virtuale Windows

Se è necessario aumentare le dimensioni del disco dati collegato alla macchina virtuale, è possibile farlo tramite PowerShell. Dopo aver aumentato le dimensioni del disco dati nelle impostazioni della macchina virtuale di Azure, è necessario allocare il nuovo spazio su disco all'interno della macchina virtuale.


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Usare PowerShell per aumentare le dimensioni di un disco dati gestito

Per aumentare le dimensioni di un disco dati gestito, usare i seguenti cmdlet di PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                        | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                    | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |
<br>

Lo script seguente illustra come ottenere le informazioni della macchina virtuale, selezionare il disco dati e specificare le nuove dimensioni.

```powershell
# Select resource group

    $rg = Get-AzureRMReseourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru

    $rgName = $rg.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk

    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru

# Specify a larger size for the data disk

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)

    $vm.StorageProfile.DataDisks

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM

    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Usare PowerShell per aumentare le dimensioni di un disco dati non gestito

Per aumentare le dimensioni di un disco dati non gestito in un account di archiviazione, usare i seguenti cmdlet di PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                   | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |

<br>

Lo script seguente permette di ottenere le informazioni della macchina virtuale e dell'account di archiviazione, di selezionare il disco dati e di specificare le nuove dimensioni.

```powershell

# Select Azure Storage Account

    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru

    $rgName = $storageAccount.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize

    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru


# Specify a larger data disk size

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name

```

## <a name="allocate-the-unallocated-disk-space"></a>Allocare lo spazio su disco non allocato

Dopo aver aumentato le dimensioni dell'unità, è necessario allocare il nuovo spazio non allocato dall'interno della macchina virtuale. Per allocare lo spazio, è possibile connettersi alla macchina virtuale usando Gestione disco (diskmgmt.msc). In alternativa, se è stata abilitata l'installazione di WinRM e di un certificato durante la creazione della macchina virtuale, è possibile usare PowerShell remoto per inizializzare il disco. È anche possibile usare un'estensione di script personalizzata:

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Il file di script può contenere un codice simile al seguente per aumentare l'allocazione dell'unità alla dimensione massima dei dischi:

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni su dischi e dischi rigidi virtuali](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

