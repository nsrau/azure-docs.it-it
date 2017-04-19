---
title: Espandere i dischi rigidi virtuali in una macchina virtuale Linux in Azure | Microsoft Docs
description: Informazioni su come espandere i dischi rigidi virtuali in una macchina virtuale Linux con l&quot;interfaccia della riga di comando 2.0 di Azure
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/22/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 07d4afc5f3eedbdcd4686a64acccf40278f139c4
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli-20"></a>Procedura per espandere i dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando 2.0 di Azure
Le dimensioni predefinite del disco rigido virtuale per il sistema operativo sono in genere di 30 GB in una VM Linux in Azure. È possibile [aggiungere dischi dati](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per aumentare lo spazio di archiviazione, ma è anche possibile espandere il disco del sistema operativo o il disco dati esistente. Questo articolo illustra come espandere i dischi gestiti di una macchina virtuale Linux tramite l'interfaccia della riga di comando 2.0 di Azure. È anche possibile espandere il disco del sistema operativo non gestito con l'[interfaccia della riga di comando 1.0 di Azure](expand-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="expand-managed-disk"></a>Espandere un disco gestito
Assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e di aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/#login).

Negli esempi seguenti sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup` e `myVM`.

1. Non è possibile eseguire operazioni sui dischi rigidi virtuali quando la macchina virtuale è in esecuzione. Deallocare la macchina virtuale con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` non rilascia le risorse di calcolo. Per rilasciare le risorse di calcolo, usare `az vm deallocate`. Per espandere il disco rigido virtuale è necessario deallocare la macchina virtuale.

2. Vedere un elenco di dischi gestiti presenti nel gruppo di risorse con [az disk list](/cli/azure/disk#list). L'esempio seguente mostra un elenco di dischi gestiti nel gruppo di risorse denominato `myResourceGroup`:

    ```azurecli
    az disk list -g myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Espandere il disco richiesto con [az disk update](/cli/azure/disk#update). Nell'esempio seguente viene eseguita l'espansione del disco gestito denominato `myDataDisk` affinché abbia una dimensione di `200` Gb:

    ```azurecli
    az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 200
    ```

    > [!NOTE]
    > Quando si espande un disco gestito, si esegue il mapping delle dimensioni aggiornate per le dimensioni del disco gestito più vicino. Per consultare una tabella delle dimensioni e dei livelli dei dischi disponibili, vedere [Panoramica di Azure Managed Disks - Prezzi e fatturazione](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#pricing-and-billing).

3. Avviare la macchina virtuale con [az vm start](/cli/azure/vm#start). Nell'esempio seguente viene avviata la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. Eseguire SSH nella macchina virtuale con le credenziali appropriate. Per verificare che il disco del sistema operativo sia stato ridimensionato, usare `df -h`. L'output di esempio seguente mostra che la partizione primaria (`/dev/sda1`) ha ora una dimensione di 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>Passaggi successivi
Se è necessario altro spazio di archiviazione, è possibile [aggiungere dischi dati a una VM Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Per altre informazioni sulla crittografia del disco, vedere [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

