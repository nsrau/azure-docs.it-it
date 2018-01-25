---
title: Creare uno snapshot di un disco rigido virtuale in Azure | Microsoft Docs
description: Informazioni su come creare una copia di una macchina virtuale di Azure da usare come backup o per la risoluzione dei problemi.
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: 10b5eb0062e4a029b0f233ee8af17d590d59c8d4
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="create-a-snapshot"></a>Creare uno snapshot

Fare uno snapshot di un disco rigido virtuale del sistema operativo o di un disco dati per il backup o per risolvere i problemi della macchina virtuale. Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. 

## <a name="use-azure-portal-to-take-a-snapshot"></a>Usare il portale di Azure per creare uno snapshot 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. In alto a sinistra fare clic su **Nuovo** e cercare **Snapshot**.
3. Nel pannello Snapshot, fare clic su **Crea**.
4. Immettere un **Nome** per lo snapshot.
5. Selezionare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#resource-groups) esistente o specificare il nome di un nuovo gruppo. 
6. Selezionare una località per il data center di Azure.  
7. Per **Disco di origine**, selezionare il disco gestito di cui creare lo snapshot.
8. Selezionare il **tipo di account** da usare per archiviare lo snapshot. È consigliabile usare il tipo **Standard_LRS** a meno che non sia necessario archiviare lo snapshot su un disco a prestazioni elevate.
9. Fare clic su **Crea**.

## <a name="use-powershell-to-take-a-snapshot"></a>Utilizzare PowerShell per creare uno snapshot
La procedura seguente mostra come ottenere il disco rigido virtuale da copiare, creare configurazioni di snapshot e fare uno snapshot del disco tramite il cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Verificare di aver installato la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```
Install-Module AzureRM.Compute -MinimumVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](/powershell/azure/overview).


1. Impostare alcuni parametri. 

 ```azurepowershell-interactive
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$dataDiskName = 'myDisk' 
$snapshotName = 'mySnapshot'  
```

2. Ottenere il disco rigido virtuale da copiare.

 ```azurepowershell-interactive
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName -DiskName $dataDiskName 
```
3. Creare le configurazioni di snapshot. 

 ```azurepowershell-interactive
$snapshot =  New-AzureRmSnapshotConfig -SourceUri $disk.Id -CreateOption Copy -Location $location 
```
4. Ottenere lo snapshot.

 ```azurepowershell-interactive
New-AzureRmSnapshot -Snapshot $snapshot -SnapshotName $snapshotName -ResourceGroupName $resourceGroupName 
```
Se si prevede di usare lo snapshot per creare un disco gestito e associarlo a una macchina virtuale a prestazioni elevate, usare il parametro `-AccountType Premium_LRS` con il comando New-AzureRmSnapshot. Il parametro crea lo snapshot in modo tale che venga archiviato come un disco gestito Premium. I dischi gestiti Premium sono più costosi di quelli Standard. Pertanto, assicurarsi che l'opzione Premium sia realmente necessaria prima di usare tale parametro.

## <a name="next-steps"></a>Passaggi successivi

Crea una macchina virtuale da uno snapshot creando un disco gestito dallo snapshot e quindi collegando il nuovo disco gestito come disco del sistema operativo. Per altre informazioni, vedere l'esempio [Creare una macchina virtuale da uno snapshot](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json).
