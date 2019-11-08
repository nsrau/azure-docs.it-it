---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di una macchina virtuale di Azure da usare come backup o per la risoluzione dei problemi.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 74ef55fe9a0a6603bd43d68b6b8557dce1bca921
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749137"
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
9. Selezionare **Crea**.

## <a name="use-powershell"></a>Usare PowerShell

La procedura seguente mostra come copiare il disco rigido virtuale, creare la configurazione di snapshot e fare uno snapshot del disco tramite il cmdlet [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot). 

 

1. Impostare alcuni parametri: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Ottenere la macchina virtuale:

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. Creare la configurazione dello snapshot. Per questo esempio, lo snapshot è del disco del sistema operativo:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-SkuName Standard_ZRS`.   
   
4. Fare lo snapshot:

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passaggi successivi

Crea una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere l'esempio disponibile in [Creare una macchina virtuale da uno snapshot con PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
