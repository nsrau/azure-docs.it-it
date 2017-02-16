---
title: Scollegare un disco dati da una VM di Windows | Microsoft Docs
description: Informazioni su come scollegare un disco dati da una macchina virtuale in Azure usando il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 730672a8b52ef37c759b5c92bc1e36d8477da3f3


---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale di Windows
Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. 

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni fare riferimento a [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md#pricing-and-billing). 
> 
> 

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  

## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale
1. Nell'hub del portale selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale con il disco dati da scollegare e fare clic su **Tutte le impostazioni**.
3. Nel pannello **Impostazioni** selezionare **Dischi**.
4. Nel pannello **Dischi** selezionare un disco dati che si desidera scollegare.
5. Nel pannello per il disco dati fare clic su **Scollega**.

    ![Schermata che illustra il pulsante Scollega.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

## <a name="detach-a-data-disk-using-powershell"></a>Scollegare un disco dati tramite PowerShell
In questo esempio, il primo comando consente di denominare la macchina virtuale **MyVM07** nel gruppo di risorse **RG11** usando il cmdlet Get-AzureRmVM. Il comando archivia la macchina virtuale nella variabile **$VirtualMachine** . 

Il secondo comando rimuove il disco dati denominato DataDisk3 dalla macchina virtuale. 

L'ultimo comando aggiorna lo stato della macchina virtuale per completare il processo di rimozione del disco dati.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```


Per altre informazioni, vedere [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Passaggi successivi
Se si desidera riutilizzare il disco dati, è sufficiente [collegarlo a un'altra VM](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


