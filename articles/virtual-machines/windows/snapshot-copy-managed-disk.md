---
title: Creare uno snapshot di un disco rigido virtuale usando il portale o PowerShell
description: Informazioni su come creare una copia di una macchina virtuale di Azure da usare come backup o per la risoluzione dei problemi usando il portale o PowerShell.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c0fddca70ca09f0a3abb3c84672e20a42551ecd5
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322314"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Creare uno snapshot usando il portale o PowerShell

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. È possibile fare uno snapshot di un disco rigido virtuale del sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale.

Se si prevede di usare lo snapshot per creare una nuova macchina virtuale, è consigliabile arrestare correttamente la macchina virtuale prima di acquisire uno snapshot in modo da cancellare gli eventuali processi in corso.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure 

Per creare uno snapshot, completare i passaggi seguenti: 
1.  Nella [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa**.
2. Cercare e selezionare **snapshot**.
3. Nella finestra **Snapshot** selezionare **Crea**. Viene visualizzata la finestra **Crea snapshot**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [Gruppo di risorse](../../azure-resource-manager/management/overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una **località** per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. Selezionare **Standard_HDD** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Selezionare **Create** (Crea).

## <a name="use-powershell"></a>Usare PowerShell

Nei passaggi seguenti viene illustrato come copiare il disco rigido virtuale e creare la configurazione dello snapshot. È quindi possibile eseguire uno snapshot del disco usando il cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Impostare alcuni parametri: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Ottenere la macchina virtuale:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Creare la configurazione dello snapshot. Per questo esempio, lo snapshot è del disco del sistema operativo:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-SkuName Standard_ZRS`.   
   
4. Fare lo snapshot:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Passaggi successivi

Crea una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere l'esempio disponibile in [Creare una macchina virtuale da uno snapshot con PowerShell](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
