---
title: Montare Archiviazione file di Azure in VM Linux usando SMB con l&quot;interfaccia della riga di comando di Azure 1.0 | Microsoft Docs
description: Come montare Archiviazione file di Azure in VM Linux usando SMB
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
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: ac0623a32fd1fb4a3dd9e1dd06f457da6ce56199
ms.lasthandoff: 02/27/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montare Archiviazione file di Azure in VM Linux usando SMB con l'interfaccia della riga di comando di Azure 1.0

Questo articolo illustra come montare Archiviazione file di Azure in una VM Linux usando il protocollo Server Message Block (SMB). Archiviazione file offre condivisioni file nel cloud tramite il protocollo SMB standard. I requisiti sono:

* Un [account Azure](https://azure.microsoft.com/pricing/free-trial/)
* [File di chiavi pubbliche e private Secure Shell (SSH)](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>Versioni dell'interfaccia della riga di comando da usare
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): interfaccia della riga di comando di nuova generazione per il modello di distribuzione Resource Manager


## <a name="quick-commands"></a>Comandi rapidi
Per eseguire rapidamente l'attività, seguire i passaggi in questa sezione. Per informazioni più dettagliate e di contesto, iniziare con la sezione ["Procedura dettagliata"](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

### <a name="prerequisites"></a>Prerequisiti
* Un gruppo di risorse
* Una rete virtuale di Azure
* Un gruppo di sicurezza di rete con SSH in ingresso
* Una subnet
* Un account di archiviazione di Azure
* Chiavi dell'account di archiviazione di Azure
* Una condivisione di Archiviazione file di Azure
* Una VM Linux

Sostituire gli esempi con le impostazioni desiderate.

### <a name="create-a-directory-for-the-local-mount"></a>Creare una directory per il montaggio locale

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montare la condivisione SMB di Archiviazione file sul punto di montaggio

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Rendere persistente il montaggio dopo un riavvio
Aggiungere la riga seguente a `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

Archiviazione file offre condivisioni file nel cloud che usano il protocollo SMB standard. Con la versione più recente di Archiviazione file è anche possibile montare una condivisione di file da un sistema operativo che supporta SMB 3.0. Quando si usa un montaggio SMB in Linux è possibile eseguire facilmente copie di backup in un percorso di archiviazione affidabile e permanente supportato da un contratto di servizio.

Lo spostamento di file da una VM a un montaggio SMB ospitato in Archiviazione file è un ottimo modo per eseguire il debug dei log, dato che la stessa condivisione SMB può essere montata in locale in workstation Mac, Linux o Windows. SMB non è la soluzione migliore per eseguire lo streaming di log applicazioni o Linux in tempo reale perché il protocollo SMB non è stato creato per la gestione di attività di registrazione così impegnative. Per raccogliere l'output di log applicazioni o Linux, è preferibile usare uno strumento dedicato con livello di registrazione unificato come Fluentd.

Per questa procedura dettagliata vengono definiti i prerequisiti necessari prima per creare la condivisione di Archiviazione file di Azure e quindi per montarla tramite SMB in una VM Linux.

1. Creare un account di archiviazione di Azure usando il codice seguente:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Visualizzare le chiavi dell'account di archiviazione.

    Quando si crea un account di archiviazione, le chiavi dell'account vengono create a coppie perché possano essere ruotate senza interrompere il servizio. Quando si passa alla seconda chiave della coppia, viene creata una nuova coppia di chiavi. Le nuove chiavi dell'account di archiviazione vengono sempre create a coppie in modo da avere sempre a disposizione almeno una chiave di archiviazione non usata alla quale passare. Per visualizzare le chiavi dell'account di archiviazione usare il codice seguente:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Creare la condivisione di Archiviazione file.

    La condivisione di Archiviazione file contiene la condivisione SMB. La quota è sempre espressa in gigabyte (GB). Per creare la condivisione di Archiviazione file usare il codice seguente:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Creare la directory del punto di montaggio.

    Nel file system di Linux è necessario creare una directory locale nella quale montare la condivisione SMB. Qualsiasi elemento scritto o letto dalla directory di montaggio locale viene inoltrato alla condivisione SMB ospitata in Archiviazione file. Per creare la directory usare il codice seguente:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Montare la condivisione SMB usando il codice seguente:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Rendere persistente il montaggio SMB dopo il riavvio.

    Quando si riavvia la VM Linux, durante la fase di arresto viene smontata la condivisione SMB montata. Per rimontare la condivisione SMB all'avvio, è necessario aggiungere una riga al file /etc/fstab di Linux. Linux usa il file fstab per elencare i file system da montare durante la fase di avvio. Aggiungendo la condivisione SMB si assicura che la condivisione di Archiviazione file costituisca un file system montato in modo permanente per la VM Linux. L'aggiunta della condivisione SMB di Archiviazione file in una nuova VM è possibile quando si usa cloud-init.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Uso di cloud-init per personalizzare una VM Linux durante la creazione](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

