---
title: Montare Archiviazione file di Azure su macchine virtuali Linux usando SMB | Documentazione Microsoft
description: Come montare Archiviazione file di Azure su macchine virtuali Linux usando SMB.
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
ms.sourcegitcommit: 6a3c4b85642a7b6eb4dbd0efc3b046a89328b3f0
ms.openlocfilehash: 711d217841690ee78321a1ae1a56571c49ef74cc


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montare Archiviazione file di Azure su macchine virtuali Linux usando SMB

Questo articolo descrive come usare il servizio Archiviazione file di Azure in una macchina virtuale Linux usando un montaggio SMB.  L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard.  I requisiti sono:

- [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)

- [File di chiavi SSH pubbliche e private](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Comandi rapidi

Se si vuole eseguire rapidamente l'attività, la sezione seguente indica in dettaglio i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, [a partire da qui](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough).

Prerequisiti: gruppo di risorse, rete virtuale, gruppo di sicurezza di rete con SSH in ingresso, subnet, account di archiviazione di Azure, chiavi dell'account di archiviazione di Azure, condivisione di Archiviazione file di Azure e una macchina virtuale Linux. Sostituire gli esempi con le impostazioni desiderate.

Creare una directory per il montaggio locale

```bash
mkdir -p /mnt/mymountpoint
```

Montare la condivisione SMB di Archiviazione file di Azure sul punto di montaggio

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Per mantenere il montaggio anche dopo il riavvio, aggiungere una riga a `/etc/fstab`

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard.  Con la versione più recente del servizio Archiviazione file è possibile anche montare una condivisione di file da un sistema operativo che supporta SMB 3.0.  L'uso di un montaggio SMB in Linux consente di eseguire facilmente copie di backup su un percorso di archiviazione affidabile e permanente supportato da un contratto di servizio.  

Spostare i file da una macchina virtuale a un montaggio SMB ospitato nel servizio Archiviazione file di Azure è un'ottima soluzione per eseguire il debug dei log poiché la stessa condivisione SMB può essere montata localmente su una workstation Mac, Linux o Windows.  SMB, tuttavia, non è la soluzione migliore per eseguire lo streaming di log applicazioni o Linux in tempo reale poiché il protocollo SMB non è stato creato per la registrazione di carichi così pesanti.  Per raccogliere l'output di log applicazioni o Linux, è preferibile usare uno strumento dedicato con livello di registrazione unificato come Fluentd.

In questa procedura dettagliata vengono definiti i prerequisiti necessari prima per creare la condivisione del servizio Archiviazione file di Azure e quindi per montarla tramite SMB su una macchina virtuale Linux.

## <a name="create-the-azure-storage-account"></a>Creare l'account di archiviazione di Azure

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>Visualizzare le chiavi dell'account di archiviazione

Le chiavi dell'account di archiviazione di Azure vengono create a coppie nel momento in cui viene creato l'account di archiviazione.  Le chiavi dell'account di archiviazione vengono create a coppie affinché possano essere ruotate senza interrompere il servizio.  Ogni volta che si ruota sulla seconda chiave della coppia, viene creata una nuova coppia di chiavi.  Le nuove chiavi dell'account di archiviazione vengono sempre create a coppie in modo da avere sempre a disposizione almeno una chiave di archiviazione non usata su cui poter ruotare.

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Creare la condivisione di Archiviazione file di Azure

Creare la condivisione di archiviazione file che contiene la condivisione SMB.  Le dimensioni sono sempre espresse in gigabyte (GB).

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>Creare la directory del punto di montaggio

Sul file system di Linux è necessaria una directory locale su cui poter montare la condivisione SMB.  Qualsiasi elemento scritto o letto dalla directory di montaggio locale viene inoltrato alla condivisione SMB ospitata nel servizio Archiviazione file di Azure.

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>Montare la condivisione SMB

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>Mantenere il montaggio di SMB dopo il riavvio del sistema

Quando si riavvia la macchina virtuale Linux, durante la fase di arresto viene disinstallata la condivisione SMB montata.  Per consentire il rimontaggio della condivisione SMB all'avvio, è necessario aggiungere una riga al comando `/etc/fstab` di Linux.  Linux usa il file `fstab` per elencare i file system necessari per eseguire il montaggio durante la fase di avvio.  Aggiungendo la condivisione SMB si garantisce che la condivisione di Archiviazione file di Azure costituisca un file system montato in modo permanente sulla macchina virtuale Linux.  Per aggiungere la condivisione SMB di Archiviazione file di Azure in una nuova macchina virtuale è possibile usare `cloud-init`.

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>Passaggi successivi

- [Uso di cloud-init per personalizzare una VM Linux durante la creazione](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Aggiungere un disco a una VM Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO3-->


