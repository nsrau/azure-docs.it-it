---
title: Scollegare un disco dati da una macchina virtuale Windows - Azure
description: Scollegare un disco dati da una macchina virtuale in Azure usando il modello di distribuzione Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 361ed04a6448bec18fac94ad90a33fe01a49e595
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974159"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale di Windows

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio.

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni, consultare [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../disks-types.md#billing).

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

 

## <a name="detach-a-data-disk-using-powershell"></a>Scollegare un disco dati tramite PowerShell

Non è possibile rimuovere *a caldo* un disco dati tramite PowerShell, ma è possibile accertarsi che nessuno stia usando il disco prima di scollegarlo dalla macchina virtuale.

In questo esempio verrà rimosso il disco denominato **myDisk** dalla macchina virtuale **myVM** nel gruppo di risorse **myResourceGroup**. Prima di tutto, rimuovere il disco usando il cmdlet [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk). Aggiornare quindi lo stato della macchina virtuale usando il cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) per completare il processo di rimozione del disco dati.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale

Non è possibile rimuovere *a caldo* un disco dati, ma è possibile accertarsi che nessuno stia usando il disco prima di scollegarlo dalla macchina virtuale.

1. Dal menu a sinistra selezionare **Macchine virtuali**.
1. Selezionare la macchina virtuale con il disco dati da scollegare.
1. In **Impostazioni** selezionare **Dischi**.
1. Nella parte superiore del riquadro **Dischi** selezionare **Modifica**.
1. Nel riquadro **Dischi** selezionare **Scollega** nella parte più a destra del disco dati da scollegare.
1. Fare clic su **Salva** nella parte superiore di questa pagina per salvare le modifiche.

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera riutilizzare il disco dati, è sufficiente [collegarlo a un'altra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)