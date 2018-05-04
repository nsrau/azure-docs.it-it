---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di una macchina virtuale di Azure da usare come backup o per la risoluzione dei problemi.
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: d7315d3fb7fc156beb85271d0e5aa19ec6baa7a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-snapshot"></a>Creare uno snapshot

Fare uno snapshot di un disco rigido virtuale del sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale. Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usare il portale di Azure per creare uno snapshot 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Crea una risorsa** e cercare **Snapshot**.
3. Nel pannello Snapshot, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una località per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. È consigliabile usare il tipo **Standard_LRS** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Fare clic su **Crea**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utilizzare PowerShell per creare uno snapshot

La procedura seguente mostra come ottenere il disco rigido virtuale da copiare, creare configurazioni di snapshot e fare uno snapshot del disco tramite il cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Prima di iniziare, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Questo articolo richiede il modulo AzureRM 5.7.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

Impostare alcuni parametri. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  
```

Ottenere la VM.

 ```azurepowershell-interactive
$vm = get-azurermvm `
   -ResourceGroupName $resourceGroupName `
   -Name $vmName
```

Creare la configurazione dello snapshot. In questo esempio verrà eseguito uno snapshot del disco del sistema operativo.

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig `
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
   -Location $location `
   -CreateOption copy
```
   
> [!NOTE]
> Se si vuole archiviare lo snapshot in una risorsa di archiviazione resiliente nella zona, è necessario crearlo in un'area che supporta le [zone di disponibilità](../../availability-zones/az-overview.md) e includere il parametro `-SkuName Standard_ZRS`.   

   
Ottenere lo snapshot.

```azurepowershell-interactive
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```




## <a name="next-steps"></a>Passaggi successivi

Crea una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere l'esempio [Creare una macchina virtuale da uno snapshot](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
