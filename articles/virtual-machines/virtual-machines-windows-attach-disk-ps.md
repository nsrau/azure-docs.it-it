---
title: Collegare un disco dati a una macchina virtuale Windows in Azure con PowerShell | Documentazione Microsoft
description: Come collegare un disco dati nuovo o esistente a una macchina virtuale Windows con PowerShell tramite il modello di distribuzione di Resource Manager.
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
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c0f96dfadb41aa05f922629820f29454d49d847a
ms.openlocfilehash: 0d78c93e90af0fa4c813ee53e4003c1f09442157


---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Collegare un disco dati a una macchina virtuale Windows con PowerShell

Questo articolo illustra come collegare dischi nuovi o esistenti a una macchina virtuale Windows tramite PowerShell. Se la macchina virtuale usa dischi gestiti, è possibile collegare dischi dati gestiti aggiuntivi. È anche possibile collegare dischi dati non gestiti in una macchina virtuale che usa dischi non gestiti in un account di archiviazione.

Prima di procedere, rivedere i suggerimenti seguenti:
* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per usare l'Archiviazione Premium, è necessario usare una macchina virtuale abilitata per l'Archiviazione Premium di dimensioni simili a una macchina virtuale della serie DS o GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di disporre della versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Aggiungere un disco dati vuoto a una macchina virtuale

Questo esempio illustra come aggiungere un disco dati vuoto a una macchina virtuale esistente.

### <a name="using-managed-disks"></a>Uso di Managed Disks

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128

$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-unmanaged-disks-in-a-storage-account"></a>Uso di dischi non gestiti in un account di archiviazione

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```


### <a name="initialize-the-disk"></a>Inizializzare il disco

Dopo aver aggiunto un disco vuoto, è necessario inizializzarlo. Per inizializzare il disco, è possibile accedere alla macchina virtuale e usare la gestione del disco. Se è stata abilitata l'installazione di WinRM e di un certificato durante la creazione della macchina virtuale, è possibile usare PowerShell remoto per inizializzare il disco. È anche possibile usare un'estensione di script personalizzata: 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Il file di script può contenere codice analogo al seguente per inizializzare i dischi:

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Collegare un disco dati esistente a una macchina virtuale

È anche possibile collegare un disco rigido virtuale esistente come disco dati gestito a una macchina virtuale. 

### <a name="using-managed-disks"></a>Uso di Managed Disks

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.windows.net/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Passaggi successivi

Creare uno [snapshot](virtual-machines-windows-snapshot-copy-managed-disk.md).



<!--HONumber=Feb17_HO2-->


