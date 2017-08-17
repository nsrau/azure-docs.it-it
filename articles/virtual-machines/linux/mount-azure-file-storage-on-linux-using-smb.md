---
title: Montare l'archiviazione file di Azure su VM Linux usando SMB | Microsoft Docs
description: Come montare l'archiviazione file di Azure su VM Linux usando SMB con l'interfaccia della riga di comando di Azure 2.0
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 9eae17b304f8a987b44ebed8906dabd8ff3a36a8
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montare l'archiviazione file di Azure su VM Linux usando SMB

Questo articolo descrive come usare il servizio di archiviazione file di Azure su una VM Linux usando un montaggio SMB con l'interfaccia della riga di comando di Azure 2.0. L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
- [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Comandi rapidi

* Un gruppo di risorse
* Una rete virtuale di Azure
* Un gruppo di sicurezza di rete con SSH in ingresso
* Una subnet
* Un account di archiviazione di Azure
* Chiavi dell'account di archiviazione di Azure
* Una condivisione di archiviazione file di Azure
* Una VM Linux

Sostituire gli esempi con le impostazioni desiderate.

### <a name="create-a-directory-for-the-local-mount"></a>Creare una directory per il montaggio locale

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montare la condivisione SMB di archiviazione file sul punto di montaggio

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Rendere persistente il montaggio dopo un riavvio
Per farlo, aggiungere la riga seguente a `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

L'archiviazione file offre condivisioni file nel cloud che usano il protocollo SMB standard. Con la versione più recente di archiviazione file è anche possibile montare una condivisione di file da un sistema operativo che supporta SMB 3.0. Quando si usa un montaggio SMB in Linux è possibile eseguire facilmente copie di backup in un percorso di archiviazione affidabile e permanente supportato da un Contratto di servizio.

Lo spostamento di file da una VM a un montaggio SMB ospitato nell'archiviazione file è un ottimo modo per eseguire il debug dei log, dato che la stessa condivisione SMB può essere montata in locale in workstation Mac, Linux o Windows. SMB non è la soluzione migliore per eseguire lo streaming di log applicazioni o Linux in tempo reale perché il protocollo SMB non è stato creato per la gestione di attività di registrazione così impegnative. Per raccogliere l'output di log applicazioni o Linux è preferibile usare uno strumento dedicato con livello di registrazione unificato come Fluentd piuttosto che SMB.

Per questa procedura dettagliata vengono definiti i prerequisiti necessari prima per creare la condivisione di archiviazione file di Azure e quindi per montarla tramite SMB in una VM Linux.

1. Creare un gruppo di risorse con [az group create](/cli/azure/group#create) per contenere la condivisione file.

    Per creare un gruppo di risorse denominato `myResourceGroup` nell'area "Stati Uniti occidentali" usando il comando seguente:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Creare un account di archiviazione di Azure con [az storage account create](/cli/azure/storage/account#create) per archiviare i file effettivi.

    Per creare un account di archiviazione denominato mystorageaccount usando SKU di archiviazione Standard_LRS, usare l'esempio seguente:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Visualizzare le chiavi dell'account di archiviazione.

    Quando si crea un account di archiviazione, le chiavi dell'account vengono create a coppie perché possano essere ruotate senza interrompere il servizio. Quando si passa alla seconda chiave della coppia, viene creata una nuova coppia di chiavi. Le nuove chiavi dell'account di archiviazione vengono sempre create a coppie in modo da avere sempre a disposizione almeno una chiave dell'account di archiviazione non usata alla quale passare.

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

4. Creare la condivisione di archiviazione file.

    La condivisione di archiviazione file contiene la condivisione SMB con [az storage share create](/cli/azure/storage/share#create). La quota è sempre espressa in gigabyte (GB). Passare a una delle chiavi dal comando precedente `az storage account keys list`. Creare una condivisione denominata mystorageshare con una quota di 10 GB con l'esempio seguente:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Creare una directory del punto di montaggio.

    Nel file system di Linux creare una directory locale nella quale montare la condivisione SMB. Qualsiasi elemento scritto o letto dalla directory di montaggio locale viene inoltrato alla condivisione SMB ospitata nell'archiviazione file. Per creare una directory locale in /mnt/mymountdirectory, usare l'esempio seguente:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Montare la condivisione SMB nella directory locale.

    Fornire il proprio nome utente dell'account di archiviazione e la chiave dell'account di archiviazione per le credenziali di montaggio nel modo seguente:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Mantenere il montaggio di SMB dopo il riavvio del sistema.

    Quando si riavvia la VM Linux, durante la fase di arresto viene smontata la condivisione SMB montata. Per consentire il rimontaggio della condivisione SMB all'avvio, aggiungere una riga a /etc/fstab di Linux. Linux usa il file fstab per elencare i file system da montare durante la fase di avvio. Aggiungendo la condivisione SMB si garantisce che la condivisione di archiviazione file costituisca un file system montato in modo permanente per la VM Linux. L'aggiunta della condivisione SMB di archiviazione file in una nuova VM è possibile quando si usa cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Uso di cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Aggiungere un disco a una VM Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

