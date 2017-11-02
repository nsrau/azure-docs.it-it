---
title: Scollegare un disco dati da una macchina virtuale Linux - Azure | Microsoft Docs
description: Informazioni su come scollegare un disco dati da una macchina virtuale in Azure tramite l'interfaccia della riga di comando 2.0 o il portale di Azure.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: de0222d897ed2cf94be98501c39385ac88f866fc
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale Linux

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. 

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni fare riferimento a [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  

## <a name="detach-a-data-disk-using-cli-20"></a>Scollegare un disco dati tramite l'interfaccia della riga di comando 2.0

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.


## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale
1. Nell'hub del portale selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale con il disco dati che si vuole scollegare e fare clic su **Arresta** per deallocare la macchina virtuale.
3. Nel pannello delle macchine virtuali selezionare **Dischi**.
4. Nella parte superiore del pannello **Dischi** selezionare **Modifica**.
5. Nel pannello **Dischi**, fare clic sul pulsante per scollegare il disco ![Immagine del pulsante per scollegare il disco](./media/detach-disk/detach.png) nella parte più a destra del disc dati.
5. Dopo aver rimosso il disco, fare clic su Salva nella parte superiore del pannello.
6. Nel pannello delle macchine virtuali fare clic su **Panoramica** e quindi fare clic su **Avvia** nella parte superiore del pannello per riavviare la macchina virtuale.

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.








## <a name="next-steps"></a>Passaggi successivi
Se si intende usare nuovamente il disco dati, è sufficiente [collegarlo a un'altra macchina virtuale](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

