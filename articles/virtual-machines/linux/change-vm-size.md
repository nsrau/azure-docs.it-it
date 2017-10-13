---
title: Come ridimensionare una VM Linux con l'interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Come ridimensionare una macchina virtuale di Linux, modificando le dimensioni della VM.
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 23fc9f7f34732079682857d4ee685fe811751698
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>Ridimensionare una macchina virtuale Linux con l'interfaccia della riga di comando 2.0

Dopo aver eseguito il provisioning di una macchina virtuale (VM), è possibile scalare la macchina virtuale in verticale o orizzontale modificando le [dimensioni della VM][vm-sizes]. In alcuni casi, è necessario prima deallocare la macchina virtuale. Se le dimensioni desiderate non sono disponibili nel cluster hardware che ospita la VM, è necessario deallocare la VM. Questo articolo illustra come ridimensionare una VM Linux con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="resize-a-vm"></a>Ridimensionare una VM
Per ridimensionare la VM, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0 ](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure con il comando [az login](/cli/azure/#login).

1. Per consultare l'elenco delle dimensioni delle VM disponibili nel cluster hardware in cui è ospitata la macchina virtuale, usare il comando [az vm list-vm-resize-options](/cli/azure/vm#list-vm-resize-options). L'esempio seguente elenca le dimensioni di macchina virtuale disponibili per la VM denominata `myVM` nell'area `myResourceGroup` del gruppo di risorse:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se le dimensioni di VM desiderate sono presenti nell'elenco, ridimensionare la VM con il comando [az vm resize](/cli/azure/vm#resize). Nell'esempio seguente viene ridimensionata la VM denominata `myVM` alle dimensioni `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Durante questo processo, la VM viene riavviata. Dopo il riavvio, viene eseguito un nuovo mappaggio del sistema operativo esistente e dei dischi dati. Qualsiasi elemento presente nel disco temporaneo andrà perso.

3. Se le dimensioni della VM desiderate non sono presenti nell'elenco, è necessario innanzitutto deallocare la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm#deallocate). Questo processo consente il ridimensionamento della macchina virtuale a qualsiasi dimensione disponibile supportata dall'area e l'avvio della stessa. I passaggi seguenti consentono di deallocare, ridimensionare e quindi avviare la VM denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > La deallocazione della VM rilascia anche degli indirizzi IP dinamici assegnati alla VM. I dischi del sistema operativo e dei dati non sono coinvolti.

## <a name="next-steps"></a>Passaggi successivi
Per una maggiore scalabilità, eseguire più istanze di VM e la scalabilità orizzontale. Per altre informazioni, vedere [Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
