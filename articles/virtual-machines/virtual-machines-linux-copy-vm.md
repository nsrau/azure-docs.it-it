---
title: Creare una copia della macchina virtuale Linux usando l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima) | Microsoft Docs
description: Informazioni su come creare una copia della macchina virtuale Linux di Azure nel modello di distribuzione Resource Manager con l&quot;interfaccia della riga di comando di Azure 2.0 (anteprima)
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Creare la copia di una macchina virtuale con l'interfaccia della riga di comando di Azure 2.0 (anteprima)
Questo articolo descrive come creare una copia di una macchina virtuale (VM) di Azure che esegue Linux nel modello di distribuzione Resource Manager. Per prima cosa si esegue la copia dei dischi dati e del sistema operativo in un nuovo contenitore, quindi si configurano le risorse di rete e si crea la macchina virtuale.

È anche possibile [caricare e creare una VM da un'immagine disco personalizzata](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse
- Interfaccia della riga di comando di Azure 2.0 (anteprima): interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse (questo articolo)

## <a name="prerequisites"></a>Prerequisiti
- È necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0 (Anteprima)](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/#login).
- È necessaria una macchina virtuale di Azure da usare come origine per creare una copia.

## <a name="stop-the-vm"></a>Arrestare la VM
Deallocare la macchina virtuale di origine con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>Copiare la macchina virtuale
Per copiare una macchina virtuale, si crea una copia del disco rigido virtuale sottostante. Questo processo consente di creare una macchina virtuale specializzata che contiene la stessa configurazione e le stesse impostazioni della macchina virtuale di origine. Il processo per la copia del disco virtuale è diverso a seconda che si usino Azure Managed Disks o dischi non gestiti. La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede alcuna pianificazione o alcuna posizione per l'archiviazione. I dischi gestiti sono una risorsa di livello principale, pertanto sono più facili da usare: è possibile effettuare una copia diretta della risorsa disco. Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks). Scegliere una delle seguenti operazioni appropriate per il tipo di archiviazione della macchina virtuale di origine:

- [Dischi gestiti](#managed-disks)
- [Dischi non gestiti](#unmanaged-disks) 

### <a name="managed-disks"></a>Dischi gestiti
Elencare ogni macchina virtuale e il nome del disco gestito del sistema con [az vm list](/cli/azure/vm#list). L'esempio seguente elenca tutte le macchine virtuali nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

L'output è simile all'esempio seguente:

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

Copiare il disco creando un nuovo disco gestito con [az disk create](/cli/azure/disk#create). L'esempio seguente crea un disco denominato `myCopiedDisk` dal disco gestito `myDisk`:

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

Verificare i dischi gestiti presenti nel gruppo di risorse con [az disk list](/cli/azure/disk#list). L'esempio seguente elenca i dischi gestiti nel gruppo di risorse `myResourceGroup`:

```azurecli
az disk list --resource-group myResourceGroup --output table
```

Passare alla [creazione e verifica delle impostazioni della rete virtuale](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Dischi non gestiti
Per creare una copia di un disco rigido virtuale, sono necessari le chiavi dell'account di archiviazione e l'URI del disco. Usare [az storage account keys list](/cli/azure/storage/account/keys#list) per visualizzare le chiavi dell'account di archiviazione. L'esempio seguente elenca le chiavi dell'account di archiviazione denominato `mystorageaccount` nel gruppo di risorse `myResourceGroup`

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

Usare [az vm list](/cli/azure/vm#list) per visualizzare un elenco di macchine virtuali e i relativi URI. L'esempio seguente elenca le macchine virtuali nel gruppo di risorse `myResourceGroup`:

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

L'output è simile all'esempio seguente:

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

Copiare il disco rigido virtuale con [az storage blob copy start](/cli/azure/storage/blob/copy#start). Usare le informazioni da **az storage account keys list** e **az vm list** per fornire le chiavi dell'account di archiviazione e gli URI del disco rigido virtuale richiesti.

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>Configurare la rete virtuale
La procedura seguente crea una nuova rete virtuale, una subnet, un indirizzo IP pubblico e una scheda di interfaccia di rete virtuale. Questi passaggi sono facoltativi. Se si copia una macchina virtuale per risolvere problemi o per distribuzioni aggiuntive, si potrebbe non voler usare una macchina virtuale in una rete virtuale esistente. Se si desidera creare un'infrastruttura di rete virtuale per le macchine virtuali copiate, seguire questi passaggi successivi, in caso contrario passare alla [creazione di una macchina virtuale](#create-a-vm).

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente creata una rete virtuale denominata `myVnet` e una sottorete denominata `mySubnet`:

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip#create). Nell'esempio seguente viene aggiunto un indirizzo IP pubblico chiamato `myPublicIP` con il nome DNS `mypublicdns`. Il nome DNS deve essere univoco; specificare quindi il proprio nome univoco.

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Creare la scheda di interfaccia di rete con [az network nic create](/cli/azure/network/nic#create). Nell'esempio seguente viene creata una scheda di interfaccia di rete chiamata `myNic` associata alla subnet `mySubnet`:

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>Creare una macchina virtuale
Ora è possibile creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). Come per la copia del disco, il processo varia a seconda che si usino dischi gestiti o dischi non gestiti. Creare una macchina virtuale usando uno dei seguenti comandi appropriati.

### <a name="managed-disks"></a>Dischi gestiti
Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). Specificare il disco gestito copiato da utilizzare come disco del sistema operativo (`--attach-os-disk`) come indicato di seguito:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>Dischi non gestiti
Creare una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). Specificare l'account di archiviazione, il nome del contenitore e il disco rigido virtuale usato per la creazione della copia con **az storage blob copy start** (`--image`) come indicato di seguito:

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su come usare l'interfaccia della riga di comando di Azure per gestire la nuova macchina virtuale, vedere [Comandi dell'interfaccia della riga di comando Azure per Azure Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


