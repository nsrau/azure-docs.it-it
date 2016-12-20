---
title: Come ridimensionare una macchina virtuale Linux | Microsoft Docs
description: Come ridimensionare una macchina virtuale di Linux, modificando le dimensioni della VM.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mikewasson
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: 0032c330288ea37508c919b50a41a5e58fc28e90


---
# <a name="how-to-resize-a-linux-vm"></a>Come ridimensionare una VM di Linux
## <a name="overview"></a>Panoramica
Dopo aver eseguito il provisioning di una macchina virtuale (VM), è possibile scalare la macchina virtuale in verticale o orizzontale modificando le [dimensioni della VM][vm-sizes]. In alcuni casi, è necessario prima deallocare la macchina virtuale. Questa situazione può verificarsi se le nuove dimensioni non sono disponibili nel cluster hardware che ospita la VM.

Questo articolo illustra come ridimensionare una VM Linux mediante l'[interfaccia della riga di comando di Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="resize-a-linux-vm"></a>Ridimensionare una VM di Linux
Per ridimensionare una VM, seguire questa procedura.

1. Eseguire il comando dell'interfaccia della riga di comando seguente. Questo comando elenca le dimensioni delle VM che sono disponibili nel cluster hardware in cui è ospitata la VM.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Se le dimensioni desiderate sono nell'elenco, eseguire il comando seguente per ridimensionare la VM.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    Durante questo processo verrà riavviata la VM. Dopo il riavvio, si rimappano il sistema operativo esistente e i dischi di dati. Qualsiasi elemento presente nel disco temporaneo andrà perso.
   
    L'uso dell'opzione `--enable-boot-diagnostics` consente alla [diagnostica di avvio][boot-diagnostics] di registrare eventuali errori correlati all'avvio.
3. In caso contrario, se la dimensione desiderata non è nell'elenco, eseguire i comandi seguenti per deallocare la VM, ridimensionarla e quindi riavviare la VM.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > La deallocazione della VM rilascia anche degli indirizzi IP dinamici assegnati alla VM. I dischi del sistema operativo e dei dati non sono coinvolti.
   > 
   > 

## <a name="next-steps"></a>Passaggi successivi
Per una maggiore scalabilità, eseguire più istanze di VM e la scalabilità orizzontale. Per altre informazioni, vedere [Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali][scale-set]. 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md



<!--HONumber=Nov16_HO3-->


