---
title: Come ridimensionare una macchina virtuale Linux con l'interfaccia della riga di comando di AzureHow to resize a Linux VM with the Azure CLI
description: Come ridimensionare una macchina virtuale di Linux, modificando le dimensioni della VM.
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969272"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Ridimensionare una macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure 

Dopo aver eseguito il provisioning di una macchina virtuale (VM), è possibile scalare la macchina virtuale in verticale o orizzontale modificando le [dimensioni della VM][vm-sizes]. In alcuni casi, è necessario prima deallocare la macchina virtuale. Se le dimensioni desiderate non sono disponibili nel cluster hardware che ospita la VM, è necessario deallocare la VM. Questo articolo illustra come ridimensionare una VM Linux con l'interfaccia della riga di comando di Azure. 

## <a name="resize-a-vm"></a>Ridimensionare una VM
Per ridimensionare una VM, è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure ](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure con il comando [az login](/cli/azure/reference-index).

1. Per consultare l'elenco delle dimensioni delle VM disponibili nel cluster hardware in cui è ospitata la macchina virtuale, usare il comando [az vm list-vm-resize-options](/cli/azure/vm). L'esempio seguente elenca le dimensioni di macchina virtuale disponibili per la VM denominata `myVM` nell'area `myResourceGroup` del gruppo di risorse:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Se le dimensioni di VM desiderate sono presenti nell'elenco, ridimensionare la VM con il comando [az vm resize](/cli/azure/vm). Nell'esempio seguente viene ridimensionata la VM denominata `myVM` alle dimensioni `Standard_DS3_v2`:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Durante questo processo, la VM viene riavviata. Dopo il riavvio, viene eseguito un nuovo mappaggio del sistema operativo esistente e dei dischi dati. Qualsiasi elemento presente nel disco temporaneo andrà perso.

3. Se le dimensioni della VM desiderate non sono presenti nell'elenco, è necessario innanzitutto deallocare la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm). Questo processo consente il ridimensionamento della macchina virtuale a qualsiasi dimensione disponibile supportata dall'area e l'avvio della stessa. I passaggi seguenti consentono di deallocare, ridimensionare e quindi avviare la VM denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`:
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > La deallocazione della VM rilascia anche degli indirizzi IP dinamici assegnati alla VM. I dischi del sistema operativo e dei dati non sono coinvolti.

## <a name="next-steps"></a>Passaggi successivi
Per una scalabilità aggiuntiva, eseguire più istanze di macchine virtuali e scalare orizzontalmente. Per ulteriori informazioni, consultate Ridimensionare automaticamente le macchine Linux in un set di [scalabilità][scale-set]di macchine virtuali . 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
