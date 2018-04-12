---
title: Scollegare un disco dati da una macchina virtuale Linux - Azure | Microsoft Docs
description: Informazioni su come scollegare un disco dati da una macchina virtuale in Azure tramite l'interfaccia della riga di comando 2.0 o il portale di Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
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
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.


## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale
1. Dal menu a sinistra selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale con il disco dati che si vuole scollegare e fare clic su **Arresta** per deallocare la macchina virtuale.
3. Nel riquadro delle macchine virtuali selezionare **Dischi**.
4. Nella parte superiore del riquadro **Dischi** selezionare **Modifica**.
5. Nel riquadro **Dischi** fare clic sul pulsante per scollegare il disco ![Immagine del pulsante Scollega](./media/detach-disk/detach.png) nella parte più a destra del disco dati.
5. Dopo aver rimosso il disco, fare clic su Salva nella parte superiore del riquadro.
6. Nel riquadro delle macchine virtuali fare clic su **Panoramica** e quindi fare clic su **Avvia** nella parte superiore del riquadro per riavviare la macchina virtuale.

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.


## <a name="next-steps"></a>Passaggi successivi
Se si intende usare nuovamente il disco dati, è sufficiente [collegarlo a un'altra macchina virtuale](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

