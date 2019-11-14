---
title: Scollegare un disco dati da una macchina virtuale Windows-Azure
description: Scollegare un disco dati da una macchina virtuale in Azure usando il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 93db2935fdc41787bb1820d1f8ce85ac05ef0863
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033351"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale di Windows

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio.

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni, consultare [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](disks-types.md#billing).

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

 

## <a name="detach-a-data-disk-using-powershell"></a>Scollegare un disco dati tramite PowerShell

Non è possibile rimuovere *a caldo* un disco dati tramite PowerShell, ma è possibile accertarsi che nessuno stia usando il disco prima di scollegarlo dalla macchina virtuale.

In questo esempio verrà rimosso il disco denominato **myDisk** dalla macchina virtuale **myVM** nel gruppo di risorse **myResourceGroup**. Prima di tutto, rimuovere il disco usando il cmdlet [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk). Aggiornare quindi lo stato della macchina virtuale usando il cmdlet [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) per completare il processo di rimozione del disco dati.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale

1. Dal menu a sinistra selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale con il disco dati che si vuole scollegare e fare clic su **Arresta** per deallocare la macchina virtuale.
3. Nel riquadro delle macchine virtuali selezionare **Dischi**.
4. Nella parte superiore del riquadro **Dischi** selezionare **Modifica**.
5. Nel riquadro **Dischi** fare clic sul pulsante per scollegare il disco ![Immagine del pulsante Scollega](./media/detach-disk/detach.png) nella parte più a destra del disco dati.
5. Dopo aver rimosso il disco, fare clic su **Salva** nella parte superiore del riquadro.
6. Nel riquadro delle macchine virtuali fare clic su **Panoramica** e quindi fare clic su **Avvia** nella parte superiore del riquadro per riavviare la macchina virtuale.

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera riutilizzare il disco dati, è sufficiente [collegarlo a un'altra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)