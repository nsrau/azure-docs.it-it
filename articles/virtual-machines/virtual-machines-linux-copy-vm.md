---
title: Copiare una macchina virtuale Linux usando l&quot;interfaccia della riga di comando di Azure 2.0 |Microsoft Docs
description: Informazioni su come creare una copia della macchina virtuale Linux di Azure nel modello di distribuzione Resource Manager usando l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima).
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Creare una copia di una macchina virtuale Linux usando l'interfaccia della riga di comando di Azure 2.0 (anteprima)
Questo articolo descrive come creare una copia di una macchina virtuale (VM) di Azure che esegue Linux usando il modello di distribuzione Azure Resource Manager.

Eseguire la copia dei dischi dati e del sistema operativo in un nuovo contenitore, quindi configurare le risorse di rete e creare la macchina virtuale.

È anche possibile [caricare e creare una macchina virtuale da un'immagine disco personalizzata](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions"></a>Versioni dell'interfaccia della riga di comando
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

* [Interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): per i modelli di distribuzione classica e Resource Manager.
* Interfaccia della riga di comando di Azure 2.0 (anteprima): interfaccia di prossima generazione per il modello di distribuzione Resource Manager, trattato in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
* [Interfaccia della riga di comando di Azure 2.0 (anteprima)](/cli/azure/install-az-cli2), installata e connessa a un account Azure usando il comando [az login](/cli/azure/#login).
* Una macchina virtuale di Azure da usare come origine per la copia.

## <a name="step-1-stop-the-source-vm"></a>Passaggio 1: Arrestare la macchina virtuale di origine
Deallocare la macchina virtuale di origine usando il comando [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente mostra come deallocare la macchina virtuale `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Passaggio 2: Copiare la macchina virtuale di origine
Per copiare una macchina virtuale, si crea una copia del disco rigido virtuale sottostante. Questo processo consente di creare una macchina virtuale specializzata che contiene la stessa configurazione e le stesse impostazioni della macchina virtuale di origine.

Il processo di copia del disco virtuale è diverso a seconda che si usi Azure Managed Disks o dischi non gestiti. La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede una pianificazione o un percorso per l'archiviazione. I dischi gestiti sono una risorsa di primo livello e sono più semplici da usare. Ciò significa che è possibile eseguire una copia diretta della risorsa disco.

Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

A seconda del tipo di archiviazione della macchina virtuale di origine, seguire le istruzioni di una delle due sezioni seguenti e quindi andare al Passaggio 3: Configurare una rete virtuale.

### <a name="managed-disks"></a>Dischi gestiti

1. Elencare ogni macchina virtuale e il nome del relativo disco gestito del sistema usando il comando [az vm list](/cli/azure/vm#list). L'esempio seguente elenca tutte le macchine virtuali nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. Per copiare il disco, creare un nuovo disco gestito usando il comando [az disk create](/cli/azure/disk#create). L'esempio seguente crea un disco `myCopiedDisk` dal disco gestito `myDisk`:

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. Verificare i dischi gestiti ora presenti nel gruppo di risorse usando il comando [az disk list](/cli/azure/disk#list). L'esempio seguente elenca i dischi gestiti nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. Andare al [Passaggio 3: Configurare una rete virtuale](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Dischi non gestiti

1. Per creare una copia di un disco rigido virtuale, sono necessari le chiavi dell'account di archiviazione di Azure e l'URI del disco. Per visualizzare le chiavi dell'account di archiviazione, usare il comando [az storage account keys list](/cli/azure/storage/account/keys#list).

 L'esempio seguente elenca le chiavi dell'account di archiviazione `mystorageaccount` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 L'output è simile all'esempio seguente:

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. Per visualizzare un elenco di macchine virtuali e i relativi URI, usare il comando [az vm list](/cli/azure/vm#list). L'esempio seguente elenca le macchine virtuali nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. Copiare il disco rigido virtuale usando il comando [az storage blob copy start](/cli/azure/storage/blob/copy#start). Per fornire le chiavi dell'account di archiviazione e l'URI del disco rigido virtuale necessari, usare le informazioni contenute negli elenchi `az storage account keys` e `az vm`.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>Passaggio 3: Configurare una rete virtuale
La procedura facoltativa seguente permette di creare una nuova rete virtuale, una subnet, un indirizzo IP pubblico e una scheda di interfaccia di rete virtuale.

Se si copia una macchina virtuale per la risoluzione di problemi o per distribuzioni aggiuntive, non è consigliabile usare una macchina virtuale in una rete virtuale esistente.

Se si vuole creare un'infrastruttura di rete virtuale per le macchine virtuali copiate, seguire questa procedura. Se non si vuole creare una rete virtuale, andare al [Passaggio 4: Creare una macchina virtuale](#create-a-vm).

1. Creare la rete virtuale usando il comando [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente crea una rete virtuale `myVnet` e una subnet `mySubnet`:

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. Creare un indirizzo IP pubblico usando il comando [az network public-ip create](/cli/azure/network/public-ip#create). L'esempio seguente crea un indirizzo IP pubblico `myPublicIP` con il nome DNS `mypublicdns`. È necessario specificare un nome DNS univoco.

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. Creare la scheda di interfaccia di rete usando il comando [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea una scheda di interfaccia di rete `myNic` associata alla subnet `mySubnet`:

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>Passaggio 4: Creare una macchina virtuale
Ora è possibile creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#create). Come per la copia di un disco, il processo presenta lievi differenze nel caso di dischi gestiti o non gestiti. A seconda del tipo di archiviazione della macchina virtuale di origine, seguire le istruzioni di una delle due sezioni seguenti.

### <a name="managed-disks"></a>Dischi gestiti
1. Creare una macchina virtuale usando il comando [az vm create](/cli/azure/vm#create).
2. Specificare il disco gestito copiato da usare come disco del sistema operativo (`--attach-os-disk`). A tale scopo, procedere come segue:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>Dischi non gestiti
1. Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create).
2. Specificare l'account di archiviazione, il nome del contenitore e il disco rigido virtuale usati per creare il disco copiato con il comando `az storage blob copy start` (`--image`). A tale scopo, procedere come segue:

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova macchina virtuale, vedere [Comandi dell'interfaccia della riga di comando Azure per Azure Resource Manager](azure-cli-arm-commands.md).

