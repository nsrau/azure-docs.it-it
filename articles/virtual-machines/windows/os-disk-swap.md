---
title: Scambiare un disco del sistema operativo per una macchina virtuale di Azure con PowerShell | Microsoft Docs
description: Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195954"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando PowerShell

Se è disponibile una macchina virtuale esistente, ma si vuole scambiare il disco con un disco di backup o un altro disco del sistema operativo, è possibile usare Azure PowerShell per scambiare i dischi del sistema operativo. Non è necessario eliminare e ricreare la macchina virtuale. È anche possibile usare un disco gestito in un altro gruppo di risorse, purché non sia già in uso.

La macchina virtuale deve essere arrestata\deallocata, quindi l'ID risorsa del disco gestito può essere sostituito con l'ID risorsa di un altro disco gestito.

Assicurarsi che il tipo di archiviazione e le dimensioni della macchina virtuale siano compatibili con il disco che si intende collegare. Ad esempio, se il disco che si vuole usare si trova in Archiviazione Premium, la macchina virtuale deve essere idonea per Archiviazione Premium (ad esempio con le dimensioni della serie DS). 

Ottenere un elenco di dischi in un gruppo di risorse usando [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando si conosce il nome del disco che si vuole usare, impostarlo come disco del sistema operativo per la VM. Questo esempio arresta\dealloca la VM denominata *myVM* e assegna il disco denominato *newDisk* come nuovo disco del sistema operativo. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Passaggi successivi**

Per creare una copia di un disco, vedere [Snapshot di un disco](snapshot-copy-managed-disk.md).