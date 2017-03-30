---
title: Espandere il disco del sistema operativo in una macchina virtuale Linux con l&quot;interfaccia della riga di comando 1.0 di Azure | Microsoft Docs
description: Informazioni su come espandere il disco virtuale del sistema operativo di una macchina virtuale Linux tramite l&quot;interfaccia della riga di comando 1.0 di Azure e il modello di distribuzione di Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: cb615f41704457d5cc79be7fec7b80d491058230
ms.lasthandoff: 03/21/2017


---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Espandere il disco del sistema operativo in una macchina virtuale Linux con l'interfaccia della riga di comando 1.0 di Azure
Le dimensioni predefinite del disco rigido virtuale per il sistema operativo sono in genere di 30 GB in una VM Linux in Azure. È possibile [aggiungere dischi dati](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per aumentare lo spazio di archiviazione, ma è anche possibile espandere il disco del sistema operativo. Questo articolo illustra come espandere il disco del sistema operativo di una macchina virtuale Linux usando i dischi non gestiti con l'interfaccia della riga di comando 1.0 di Azure.

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#prerequisites): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-expand-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa

## <a name="prerequisites"></a>Prerequisiti
È necessario installare l'[interfaccia della riga di comando 1.0 di Azure più recente](../cli-install-nodejs.md) e accedere a un [account di Azure](https://azure.microsoft.com/pricing/free-trial/) usando la modalità di Resource Manager nel modo seguente:

```azurecli
azure config mode arm
```

Negli esempi seguenti sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup` e `myVM`.

## <a name="expand-os-disk"></a>Espandere il disco del sistema operativo

1. Non è possibile eseguire operazioni sui dischi rigidi virtuali quando la macchina virtuale è in esecuzione. L'esempio seguente arresta e dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` non rilascia le risorse di calcolo. Per rilasciare le risorse di calcolo, usare `azure vm deallocate`. Per espandere il disco rigido virtuale è necessario deallocare la macchina virtuale.

2. Aggiornare le dimensioni del disco non gestito del sistema operativo con il comando `azure vm set`. L'esempio seguente aggiorna la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup` affinché abbia `50` GB:

    ```azurecli
    azure vm set --resource-group myResourceGroup --name myVM --new-os-disk-size 50
    ```

3. Avviare la macchina virtuale come segue:

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. Eseguire SSH nella macchina virtuale con le credenziali appropriate. Per verificare che il disco del sistema operativo sia stato ridimensionato, usare `df -h`. L'output di esempio seguente mostra che la partizione primaria (`/dev/sda1`) ha ora una dimensione di 50 GB:

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Passaggi successivi
Se è necessario altro spazio di archiviazione, è possibile [aggiungere dischi dati a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per altre informazioni sulla crittografia del disco, vedere [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

