---
title: Creare un disco gestito da un disco rigido virtuale in Azure | Documentazione Microsoft
description: Creare un disco gestito da un disco rigido virtuale che si trova attualmente in un account di archiviazione di Azure, usando il modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0a6e034da0445e94178e04cecc3ce09d86d2d29a
ms.lasthandoff: 04/27/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>Creare dischi gestiti da dischi non gestiti in un account di archiviazione

Un disco gestito può essere creato da un disco dati esistente o da un disco del sistema operativo che si trova attualmente in un account di archiviazione di Azure. È anche possibile creare un disco vuoto da usare come nuovo disco dati per una macchina virtuale. 

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](/powershell/azure/overview).


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Creare un disco gestito da un disco rigido virtuale in un account di archiviazione di Azure

Nell'esempio viene creato un disco da un disco rigido virtuale come disco gestito e viene assegnato al parametro **$disk1** da usare in un secondo momento. 

l disco gestito verrà creato nella posizione **West-US** all'interno di un gruppo di risorse denominato **myResourceGroup**. Il disco verrà denominato **myDisk** e verrà creato da un file di disco rigido virtuale denominato **myDisk.vhd** precedentemente caricato in un account di archiviazione denominato **mystorageaccount**. Il disco gestito verrà creato nell'archiviazione con ridondanza locale Premium. StandardLRS e PremiumLRS sono le uniche opzioni del **tipo di account** disponibili per gestire i dischi. 

1.  Impostare alcuni parametri

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. Creare il disco dati. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>Creare un disco dati vuoto come disco gestito

Nell'esempio viene creato un disco dati vuoto da un disco gestito e viene assegnato al parametro **$dataDisk2** da usare in un secondo momento. Sarà necessario inizializzare un disco dati vuoto accedendo alla macchina virtuale e usando diskmgmt.msc o [con WinRM da remoto e uno script](attach-disk-ps.md#initialize-the-disk), una volta collegato il disco a una macchina virtuale in esecuzione.

l disco dati vuoto verrà creato nella posizione **Stati Uniti centro-occidentali** all'interno di un gruppo di risorse denominato **myResourceGroup**. Il disco verrà denominato **myEmptyDataDisk**. Il disco vuoto verrà creato nell'archiviazione con ridondanza locale Premium. StandardLRS e PremiumLRS sono le uniche opzioni del **tipo di account** disponibili per gestire i dischi.

Le dimensioni del disco in questo esempio sono pari a 128 GB, ma è consigliabile scegliere una dimensione che soddisfi le esigenze di tutte le applicazioni in esecuzione nella macchina virtuale.

1.  Impostare alcuni parametri

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. Creare il disco dati.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>Passaggi successivi    
- Se si dispone già di una macchina virtuale, è possibile [collegare un disco dati](attach-disk-portal.md).

