---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di una macchina virtuale di Azure da usare come backup o per la risoluzione dei problemi.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: 9bc230846714264d514b7e15962a2c99c838c7de
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465106"
---
# <a name="create-a-snapshot"></a>Creare uno snapshot

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. È possibile fare uno snapshot di un disco rigido virtuale del sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale.

Se si prevede di usare lo snapshot per creare una nuova macchina virtuale, è consigliabile arrestare correttamente la macchina virtuale prima di acquisire uno snapshot in modo da cancellare gli eventuali processi in corso.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Crea una risorsa**e quindi cercare e selezionare **snapshot**.
3. Nella finestra **Snapshot** selezionare **Crea**. Viene visualizzata la finestra **Crea snapshot**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [Gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una **località** per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. Selezionare **Standard_HDD** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Selezionare **Create**.

## <a name="use-powershell"></a>Usare PowerShell

La procedura seguente mostra come copiare il disco rigido virtuale, creare la configurazione di snapshot e fare uno snapshot del disco tramite il cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Prima di iniziare, verificare di avere la versione più recente del modulo AzureRM.Compute di PowerShell, che deve essere la versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, eseguire [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) per creare una connessione con Azure.

1. Impostare alcuni parametri: 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

2. Ottenere la macchina virtuale:

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
```

3. Creare la configurazione dello snapshot. Per questo esempio, lo snapshot è del disco del sistema operativo:

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
```
   
   > [!NOTE]
   > Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-SkuName Standard_ZRS`.   
   
4. Fare lo snapshot:

 ```azurepowershell-interactive
New-AzureRmSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
```


## <a name="next-steps"></a>Passaggi successivi

Crea una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere l'esempio disponibile in [Creare una macchina virtuale da uno snapshot con PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
