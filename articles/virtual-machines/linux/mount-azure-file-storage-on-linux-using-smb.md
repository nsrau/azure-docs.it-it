---
title: Montare l'archiviazione file di Azure su VM Linux usando SMB | Microsoft Docs
description: Come montare l'archiviazione file di Azure su VM Linux usando SMB con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 4b3bba1da5238655ca749f6464c539e53ca48f27
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542782"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montare l'archiviazione file di Azure su VM Linux usando SMB

Questo articolo descrive come usare il servizio di archiviazione file di Azure su una VM Linux usando un montaggio SMB con l'interfaccia della riga di comando di Azure. L'archiviazione file di Azure offre condivisioni file nel cloud usando il protocollo SMB standard. 

L'archiviazione file offre condivisioni file nel cloud che usano il protocollo SMB standard. È possibile montare una condivisione di file da qualsiasi sistema operativo che supporta SMB 3.0. Quando si usa un montaggio SMB in Linux è possibile eseguire facilmente copie di backup in un percorso di archiviazione affidabile e permanente supportato da un Contratto di servizio.

Lo spostamento di file da una VM a un montaggio SMB ospitato nell'archiviazione file è un ottimo modo per eseguire il debug dei log, La stessa condivisione SMB può essere montata in locale in workstation Mac, Linux o Windows. SMB non è la soluzione migliore per eseguire lo streaming di log applicazioni o Linux in tempo reale perché il protocollo SMB non è stato creato per la gestione di attività di registrazione così impegnative. Per raccogliere l'output di log applicazioni o Linux è preferibile usare uno strumento dedicato con livello di registrazione unificato come Fluentd piuttosto che SMB.

Questa guida richiede l'interfaccia della riga di comando di Azure 2.0.4 o versioni successive. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse denominato *myResourceGroup* nella posizione *Stati Uniti orientali*.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un nuovo account di archiviazione nel gruppo di risorse creato usando [az storage account create](/cli/azure/storage/account). Questo esempio viene creato un account di archiviazione denominato *mySTORAGEACCT\<numero casuale >* e inserisce il nome dell'account di archiviazione nella variabile **STORAGEACCT**. I nomi degli account di archiviazione devono essere univoci, usando `$RANDOM` si aggiunge un numero al nome e lo si rende univoco.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Ottenere la chiave di archiviazione

Quando si crea un account di archiviazione, le chiavi dell'account vengono create a coppie perché possano essere ruotate senza interrompere il servizio. Quando si passa alla seconda chiave della coppia, viene creata una nuova coppia di chiavi. Le nuove chiavi dell'account di archiviazione vengono sempre create a coppie in modo da avere sempre a disposizione almeno una chiave dell'account di archiviazione non usata alla quale passare.

Visualizzare le chiavi dell'account di archiviazione tramite il comando [az storage account keys list](/cli/azure/storage/account/keys). Questo esempio archivia il valore della chiave 1 nella variabile **STORAGEKEY**.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Creare una condivisione file

Creare la condivisione File di archiviazione usando [az storage share create](/cli/azure/storage/share). 

I nomi condivisione devono essere costituiti da lettere minuscole, numeri e trattini singoli, ma non possono iniziare con un trattino. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Questo esempio crea una condivisione denominata *myshare* con una quota di 10 GiB. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Creare un punto di montaggio

Per montare la condivisione file di Azure nel computer Linux, è necessario assicurarsi di avere il pacchetto **cifs-utils** installato. Per le istruzioni di installazione consultare [Installare il pacchetto cifs-utils per la distribuzione Linux](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

File di Azure usa il protocollo SMB, che comunica sulla porta TCP 445.  Se si verificano problemi di montaggio della condivisione file di Azure, assicurarsi che il firewall non blocchi la porta TCP 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montare la condivisione

Montare la condivisione file di Azure nella directory locale. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Il comando riportato sopra usa il comando [mount](https://linux.die.net/man/8/mount) per montare la condivisione file di Azure e opzioni specifiche per [cifs](https://linux.die.net/man/8/mount.cifs). In particolare, le opzioni file_mode e dir_mode impostano file e directory sull'autorizzazione `0777`. L'autorizzazione `0777` concede a tutti gli utenti l'autorizzazione di lettura, scrittura ed esecuzione. È possibile modificare queste autorizzazioni sostituendo i valori con altre [autorizzazioni chmod](https://en.wikipedia.org/wiki/Chmod). È anche possibile usare altre opzioni [cifs](https://linux.die.net/man/8/mount.cifs), ad esempio gid o uid. 


## <a name="persist-the-mount"></a>Rendere permanente il montaggio

Quando si riavvia la VM Linux, durante la fase di arresto viene smontata la condivisione SMB montata. Per consentire il rimontaggio della condivisione SMB all'avvio, aggiungere una riga a /etc/fstab di Linux. Linux usa il file fstab per elencare i file system da montare durante la fase di avvio. Aggiungendo la condivisione SMB si garantisce che la condivisione di archiviazione file costituisca un file system montato in modo permanente per la VM Linux. L'aggiunta della condivisione SMB di archiviazione file in una nuova VM è possibile quando si usa cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
Per aumentare la sicurezza negli ambienti di produzione, è consigliabile archiviare le credenziali di fuori di fstab.

## <a name="next-steps"></a>Passaggi successivi

- [Uso di cloud-init per personalizzare una VM Linux durante la creazione](using-cloud-init.md)
- [Aggiungere un disco a una VM Linux](add-disk.md)
- [Crittografare i dischi di una VM Linux usando l'interfaccia della riga di comando di Azure](encrypt-disks.md)

