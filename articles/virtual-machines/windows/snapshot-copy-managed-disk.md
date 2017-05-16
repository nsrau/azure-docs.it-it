---
title: Copiare un disco gestito di Azure per il backup | Microsoft Docs
description: Informazioni su come creare una copia di un disco gestito di Azure da usare per il backup o sulla risoluzione dei problemi relativi al disco.
documentationcenter: 
author: cwatsonMSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 2/9/2017
ms.author: cwatson
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: bb913050fd3388d4632e6f75b36415006f370cbd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>Creare una copia di un disco rigido virtuale archiviato come disco gestito di Azure usando snapshot gestiti
Creare uno snapshot di un disco gestito per il backup o creare un disco gestito dallo snapshot e collegarlo a una macchina virtuale di prova per risolvere i problemi. Uno snapshot gestito è una copia temporizzata completa di un disco gestito di macchina virtuale. Uno snapshot crea una copia di sola lettura del disco rigido virtuale che, per impostazione predefinita, viene memorizzato come disco gestito Standard. Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Panoramica di Azure Managed Disks).

Per informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/managed-disks/). 

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Azure PowerShell Versioning](/powershell/azure/overview) (Controllo delle versioni di Azure PowerShell).

## <a name="copy-the-vhd-with-a-snapshot"></a>Copiare il disco rigido virtuale con uno snapshot
Usare il portale di Azure o PowerShell per creare uno snapshot del disco gestito.

### <a name="use-azure-portal-to-take-a-snapshot"></a>Usare il portale di Azure per creare uno snapshot 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Nuovo** e cercare **Snapshot**.
3. Nel pannello Snapshot, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una località per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. È consigliabile usare il tipo **Standard_LRS** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Fare clic su **Crea**.

### <a name="use-powershell-to-take-a-snapshot"></a>Utilizzare PowerShell per creare uno snapshot
La procedura seguente mostra come ottenere il disco rigido virtuale da copiare, creare le configurazioni di snapshot e ottenere uno snapshot del disco tramite il cmdlet New-AzureRmSnapshot<!--Add link to cmdlet when available-->. 

1. Impostare alcuni parametri. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'southeastasia' 
$dataDiskName = 'ContosoMD_datadisk1' 
$snapshotName = 'ContosoMD_datadisk1_snapshot1'  
```
  Sostituire i valori dei parametri:
  -  "myResourceGroup" con il gruppo di risorse della macchina virtuale.
  -  "southeastasia" con la posizione geografica in cui si desidera archiviare lo snapshot gestito. <!---How do you look these up? -->
  -  "ContosoMD_datadisk1" con il nome del disco del disco rigido virtuale da copiare.
  -  "ContosoMD_datadisk1_snapshot1" con il nome da utilizzare per il nuovo snapshot.

2. Ottenere il disco rigido virtuale da copiare.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Creare le configurazioni di snapshot. 

 ```powershell
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Ottenere lo snapshot.

 ```powershell
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Se si prevede di usare lo snapshot per creare un disco gestito e associarlo a una macchina virtuale a prestazioni elevate, usare il parametro `-AccountType Premium_LRS` con il comando New-AzureRmSnapshot. Il parametro crea lo snapshot in modo tale che venga archiviato come un disco gestito Premium. I dischi gestiti Premium sono più costosi di quelli Standard. Pertanto, assicurarsi che l'opzione Premium sia realmente necessaria prima di usare tale parametro.



