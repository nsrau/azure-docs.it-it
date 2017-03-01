---
title: Montare Archiviazione file di Azure su macchine virtuali Linux usando SMB | Documentazione Microsoft
description: Come montare l&quot;archiviazione file di Azure su VM Linux usando SMB con l&quot;interfaccia della riga di comando di Azure 2.0 (Anteprima)
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>Montare l'archiviazione file di Azure su VM Linux usando SMB con l'interfaccia della riga di comando di Azure 2.0 (Anteprima)

Questo articolo descrive come usare il servizio di archiviazione file di Azure in una VM Linux usando un montaggio SMB. L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard.  I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)

- [File di chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse
- [Interfaccia della riga di comando di Azure 2.0 (anteprima)](#quick-commands): interfaccia di prossima generazione per il modello di distribuzione di gestione delle risorse (questo articolo)


## <a name="quick-commands"></a>Comandi rapidi

Se si vuole eseguire rapidamente l'attività, la sezione seguente indica dettagliatamente i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Prerequisiti: gruppo di risorse, rete virtuale, gruppo di sicurezza di rete con SSH in ingresso, subnet, account di archiviazione di Azure, chiavi dell'account di archiviazione di Azure, condivisione di Archiviazione file di Azure e una macchina virtuale Linux. Sostituire i valori nell'esempio con nomi personalizzati.

Creare una directory per il montaggio locale come segue:

```bash
mkdir -p /mnt/mymountpoint
```

Montare la condivisione SMB dell'archiviazione file di Azure sul punto di montaggio come segue:

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Per mantenere il montaggio anche dopo il riavvio, aggiungere una riga a `/etc/fstab` come segue:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard. Con la versione più recente del servizio Archiviazione file è possibile anche montare una condivisione di file da un sistema operativo che supporta SMB 3.0. L'uso di un montaggio SMB in Linux consente di eseguire facilmente copie di backup su un percorso di archiviazione affidabile e permanente supportato da un contratto di servizio.  

Spostare i file da una macchina virtuale a un montaggio SMB ospitato nel servizio Archiviazione file di Azure è un'ottima soluzione per eseguire il debug dei log poiché la stessa condivisione SMB può essere montata localmente su una workstation Mac, Linux o Windows. SMB, tuttavia, non è la soluzione migliore per eseguire lo streaming di log applicazioni o Linux in tempo reale poiché il protocollo SMB non è stato creato per la registrazione di carichi così pesanti. Per raccogliere l'output di log applicazioni o Linux, è preferibile usare uno strumento dedicato con livello di registrazione unificato come Fluentd.

In questa procedura dettagliata vengono definiti i prerequisiti necessari prima per creare la condivisione del servizio Archiviazione file di Azure e quindi per montarla tramite SMB su una macchina virtuale Linux.

Creare prima un gruppo di risorse con [az group create](/cli/azure/group#create) per contenere la condivisione file. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>Creare l'account di archiviazione di Azure
Quindi creare un account di archiviazione con [az storage account create](/cli/azure/storage/account#create) per archiviare i file effettivi. L'esempio seguente crea un nuovo account di archiviazione denominato `mystorageaccount` usando lo SKU di archiviazione `Standard_LRS`:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>Visualizzare le chiavi dell'account di archiviazione

Le chiavi dell'account di archiviazione di Azure vengono create a coppie nel momento in cui viene creato l'account di archiviazione. Le chiavi dell'account di archiviazione vengono create a coppie affinché possano essere ruotate senza interrompere il servizio. Ogni volta che si ruota sulla seconda chiave della coppia, viene creata una nuova coppia di chiavi. Le nuove chiavi dell'account di archiviazione vengono sempre create a coppie in modo da avere sempre a disposizione almeno una chiave di archiviazione non usata su cui poter ruotare.

Usare [az storage account keys list](/cli/azure/storage/account/keys#list) per visualizzare le chiavi dell'account di archiviazione. L'esempio seguente elenca le chiavi dell'account di archiviazione denominato `mystorageaccount`:

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Per estrarre una singola chiave usare il flag `--query`. L'esempio seguente estrae la prima chiave (`[0]`):

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>Creare la condivisione di Archiviazione file di Azure

Creare la condivisione di archiviazione file che contiene la condivisione SMB con [az storage share create](/cli/azure/storage/share#create). Le dimensioni sono sempre espresse in gigabyte (GB). Passare una delle chiavi ottenute dal precedente comando **az storage account keys list**. L'esempio seguente crea una condivisione denominata `mystorageshare` con una quota di `10` GB:

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Creare la directory del punto di montaggio

Sul file system di Linux è necessaria una directory locale su cui poter montare la condivisione SMB. Qualsiasi elemento scritto o letto dalla directory di montaggio locale viene inoltrato alla condivisione SMB ospitata nel servizio Archiviazione file di Azure. L'esempio seguente crea una directory locale in `/mnt/mymountdirectory`:

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montare la condivisione SMB
Montare la condivisione SMB nella directory locale creata come indicato di seguito. Fornire il proprio nome utente dell'account di archiviazione e la chiave dell'account di archiviazione per le credenziali di montaggio nel modo seguente:

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Mantenere il montaggio di SMB dopo il riavvio del sistema

Quando si riavvia la macchina virtuale Linux, durante la fase di arresto viene disinstallata la condivisione SMB montata. Per consentire il rimontaggio della condivisione SMB all'avvio, aggiungere una riga al comando `/etc/fstab` di Linux. Linux usa il file `fstab` per elencare i file system necessari per eseguire il montaggio durante la fase di avvio. Aggiungendo la condivisione SMB si garantisce che la condivisione di Archiviazione file di Azure costituisca un file system montato in modo permanente sulla macchina virtuale Linux. Per aggiungere la condivisione SMB di Archiviazione file di Azure in una nuova macchina virtuale è possibile usare `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Passaggi successivi

- [Uso di cloud-init per personalizzare una VM Linux durante la creazione](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

