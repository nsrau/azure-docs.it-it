---
title: Guida rapida di Azure - Trasferire oggetti da e verso la risorsa di archiviazione BLOB con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Apprendere rapidamente a trasferire oggetti da e verso la risorsa di archiviazione BLOB di Azure usando l'interfaccia della riga di comando di Azure
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: tamram
ms.openlocfilehash: a300294c83cb206e6211985c736e3ff01bb1ab43
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Trasferire oggetti da e verso la risorsa di archiviazione BLOB con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per caricare e scaricare dati nella risorsa di archiviazione BLOB di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. I contenitori consentono di organizzare i gruppi di BLOB come si organizzano i file nelle directory del computer.

Creare un contenitore per l'archiviazione di BLOB con il comando [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Caricare un BLOB

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. La maggior parte dei file presenti nell'archiviazione BLOB viene archiviata sotto forma di BLOB in blocchi. I BLOB di aggiunta vengono usati quando è necessario aggiungere dati a un BLOB esistente senza modificarne il contenuto, ad esempio nelle operazioni di registrazione. I BLOB di pagine supportano i file con estensione VHD delle macchine virtuali IaaS.

In questo esempio si usa il comando [az storage blob upload](/cli/azure/storage/blob#upload) per caricare un BLOB nel contenitore creato nel passaggio precedente.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Questa operazione crea il BLOB se non esiste o lo sovrascrive se esiste già. Caricare tutti i file desiderati prima di continuare.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore con il comando [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Scaricare un BLOB

Usare il comando [az storage blob download](/cli/azure/storage/blob#download) per scaricare un BLOB caricato in precedenza.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Trasferire i dati con AzCopy

L'utility [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'altra opzione per il trasferimento di dati script ad alte prestazioni per Archiviazione di Azure. È possibile usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle.

Questo esempio rapido visualizza il comando AzCopy per il caricamento del file *myfile.txt* nel contenitore *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse del gruppo risorse non sono più necessarie, incluso l'account di archiviazione creato in questa Guida rapida, è possibile eliminare il gruppo di risorse con il comando [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida rapida è stato descritto il trasferimento di file tra il disco locale e un contenitore nell'archiviazione BLOB di Azure. Per altre informazioni sull'uso dei BLOB in Archiviazione di Azure, continuare con l'esercitazione per l'uso dell'archiviazione BLOB di Azure.

> [!div class="nextstepaction"]
> [Procedura: Operazioni di archiviazione BLOB con l'interfaccia della riga di comando di Azure](storage-how-to-use-blobs-cli.md)
