---
title: Copiare una macchina virtuale Linux usando l'interfaccia della riga di comando di Azure 2.0 |Microsoft Docs
description: Informazioni su come creare una copia della macchina virtuale Linux di Azure usando l'interfaccia della riga di comando di Azure 2.0 e i dischi gestiti.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 26e09f4e408b92034594215f602d5ca0ff259c5a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>Creare una copia di una VM Linux di Azure usando l'interfaccia della riga di comando di Azure 2.0 e i dischi gestiti


Questo articolo descrive come creare una copia di una macchina virtuale (VM) di Azure che esegue Linux usando l'interfaccia della riga di comando di Azure 2.0 e il modello di distribuzione Azure Resource Manager. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

È anche possibile [caricare e creare una VM da un disco rigido virtuale](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>prerequisiti


-   Installare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2)

-   Accedere a un account di Azure con [az login](/cli/azure/#az_login).

-   Disporre di una VM di Azure da usare come origine per la copia.

## <a name="step-1-stop-the-source-vm"></a>Passaggio 1: Arrestare la macchina virtuale di origine


Deallocare la macchina virtuale di origine usando il comando [az vm deallocate](/cli/azure/vm#az_vm_deallocate).
L'esempio seguente dealloca la VM denominata **myVM** nel gruppo di risorse **myResourceGroup**:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Passaggio 2: Copiare la macchina virtuale di origine


Per copiare una macchina virtuale, si crea una copia del disco rigido virtuale sottostante. Questo processo crea un disco rigido virtuale specializzato come disco gestito che contiene la stessa configurazione e le stesse impostazioni della VM di origine.

Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../windows/managed-disks-overview.md) (Panoramica di Azure Managed Disks). 

1.  Elencare ogni VM e il nome del relativo disco del sistema operativo con [az vm list](/cli/azure/vm#az_vm_list). L'esempio seguente elenca tutte le VM del gruppo di risorse denominato **myResourceGroup**:
    
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

1.  Copiare il disco creando un nuovo disco gestito con [az disk create](/cli/azure/disk#az_disk_create). L'esempio seguente crea un disco denominato **myCopiedDisk** dal disco gestito denominato **myDisk**:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Verificare i dischi gestiti ora presenti nel gruppo di risorse usando il comando [az disk list](/cli/azure/disk#az_disk_list). L'esempio seguente elenca i dischi gestiti nel gruppo di risorse denominato **myResourceGroup**:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>Passaggio 3: Configurare una rete virtuale


La procedura facoltativa seguente permette di creare una nuova rete virtuale, una subnet, un indirizzo IP pubblico e una scheda di interfaccia di rete virtuale.

Se si copia una macchina virtuale per la risoluzione di problemi o per distribuzioni aggiuntive, non è consigliabile usare una macchina virtuale in una rete virtuale esistente.

Se si vuole creare un'infrastruttura di rete virtuale per le macchine virtuali copiate, seguire questa procedura. Se non si vuole creare una rete virtuale, andare al [Passaggio 4: Creare una VM](#step-4-create-a-vm).

1.  Creare la rete virtuale usando il comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). L'esempio seguente crea una rete virtuale denominata **myVnet** e una subnet denominata **mySubnet**:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Creare un indirizzo IP pubblico usando il comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). L'esempio seguente crea un indirizzo IP pubblico chiamato **myPublicIP** con il nome DNS **mypublicdns**. È necessario specificare un nome DNS univoco.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Creare la scheda di interfaccia di rete usando [az network nic create](/cli/azure/network/nic#az_network_nic_create).
    L'esempio seguente crea una scheda di interfaccia di rete denominata **myNic** associata alla subnet **mySubnet**:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>Passaggio 4: Creare una macchina virtuale

Ora è possibile creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#az_vm_create).

Specificare il disco gestito copiato da usare come disco del sistema operativo (--attach-os-disk). A tale scopo, procedere come segue:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova VM, vedere [Comandi dell'interfaccia della riga di comando di Azure per Azure Resource Manager](../azure-cli-arm-commands.md).
