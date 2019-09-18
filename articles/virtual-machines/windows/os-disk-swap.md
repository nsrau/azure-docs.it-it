---
title: Scambiare un disco del sistema operativo per una macchina virtuale di Azure con PowerShell | Microsoft Docs
description: Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 28aad5c3ff80042d1b9de5a8a92137e19c8871fd
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058539"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Modificare il disco del sistema operativo usato da una macchina virtuale di Azure usando PowerShell

Se è disponibile una macchina virtuale esistente, ma si vuole scambiare il disco con un disco di backup o un altro disco del sistema operativo, è possibile usare Azure PowerShell per scambiare i dischi del sistema operativo. Non è necessario eliminare e ricreare la macchina virtuale. È anche possibile usare un disco gestito in un altro gruppo di risorse, purché non sia già in uso.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

La macchina virtuale deve essere arrestata\deallocata, quindi l'ID risorsa del disco gestito può essere sostituito con l'ID risorsa di un altro disco gestito.

Assicurarsi che il tipo di archiviazione e le dimensioni della macchina virtuale siano compatibili con il disco che si intende collegare. Ad esempio, se il disco che si vuole usare si trova in Archiviazione Premium, la macchina virtuale deve essere idonea per Archiviazione Premium (ad esempio con le dimensioni della serie DS). Entrambi i dischi devono avere anche le stesse dimensioni.

Ottenere un elenco di dischi in un gruppo di risorse usando [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando si conosce il nome del disco che si vuole usare, impostarlo come disco del sistema operativo per la VM. Questo esempio arresta\dealloca la VM denominata *myVM* e assegna il disco denominato *newDisk* come nuovo disco del sistema operativo. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Passaggi successivi**

Per creare una copia di un disco, vedere [Snapshot di un disco](snapshot-copy-managed-disk.md).
