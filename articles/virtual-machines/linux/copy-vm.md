---
title: Copiare una VM Linux usando l'interfaccia della riga di comando
description: Informazioni su come creare una copia della macchina virtuale Linux di Azure usando l'interfaccia della riga di comando di Azure e i dischi gestiti.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: ec8fd91dc768b44b027c96efead6924782eb0f75
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87368609"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Creare una copia di una macchina virtuale Linux di Azure usando l'interfaccia della riga di comando di Azure e i dischi gestiti

Questo articolo illustra come creare una copia della macchina virtuale (VM) di Azure che esegue Linux usando l'interfaccia della riga di comando di Azure. Per copiare, creare, archiviare e condividere immagini di VM su larga scala, vedere [raccolte di immagini condivise](../shared-images-cli.md).

È anche possibile [caricare e creare una VM da un disco rigido virtuale](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Prerequisiti

-   Installare l' [interfaccia](/cli/azure/install-az-cli2)della riga di comando di Azure.

-   Accedere a un account di Azure con [az login](/cli/azure/reference-index#az-login).

-   Disporre di una VM di Azure da usare come origine per la copia.

## <a name="stop-the-source-vm"></a>Arrestare la macchina virtuale di origine

Deallocare la macchina virtuale di origine usando il comando [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
L'esempio seguente dealloca la VM denominata *myVM* nel gruppo di risorse *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Copiare la macchina virtuale di origine

Per copiare una macchina virtuale, si crea una copia del disco rigido virtuale sottostante. Questo processo crea un disco rigido virtuale specializzato come disco gestito che contiene la stessa configurazione e le stesse impostazioni della macchina virtuale di origine.

Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../windows/managed-disks-overview.md) (Panoramica di Azure Managed Disks). 

1.  Elencare ogni VM e il nome del relativo disco del sistema operativo con [az vm list](/cli/azure/vm#az-vm-list). L'esempio seguente elenca tutte le VM del gruppo di risorse denominato *myResourceGroup*:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Copiare il disco creando un nuovo disco gestito e usando [az disk create](/cli/azure/disk#az-disk-create). L'esempio seguente crea un disco denominato *myCopiedDisk* dal disco gestito denominato *myDisk*:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verificare i dischi gestiti ora presenti nel gruppo di risorse usando il comando [az disk list](/cli/azure/disk#az-disk-list). L'esempio seguente elenca i dischi gestiti nel gruppo di risorse denominato *myResourceGroup*:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Configurare una rete virtuale

La procedura facoltativa seguente permette di creare una nuova rete virtuale, una subnet, un indirizzo IP pubblico e una scheda di interfaccia di rete virtuale.

Se si copia una macchina virtuale per la risoluzione di problemi o per distribuzioni aggiuntive, non è consigliabile usare una macchina virtuale in una rete virtuale esistente.

Se si vuole creare un'infrastruttura di rete virtuale per le macchine virtuali copiate, seguire questa procedura. Se non si vuole creare una rete virtuale, passare a [Creare una macchina virtuale](#create-a-vm).

1.  Creare la rete virtuale usando il comando [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). L'esempio seguente crea una rete virtuale denominata *myVnet* e una subnet denominata *mySubnet*:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Creare un indirizzo IP pubblico usando il comando [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). L'esempio seguente crea un indirizzo IP pubblico chiamato *myPublicIP* con il nome DNS *mypublicdns*. È necessario specificare un nome DNS univoco.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Creare la scheda di interfaccia di rete usando il comando [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    L'esempio seguente crea una scheda di interfaccia di rete denominata *myNic* associata alla subnet *mySubnet*:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az-vm-create).

Specificare il disco gestito copiato da usare come disco del sistema operativo (`--attach-os-disk`). A tale scopo, procedere come segue:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare una [raccolta di immagini condivise](../shared-images-cli.md) per gestire le immagini di macchina virtuale.
