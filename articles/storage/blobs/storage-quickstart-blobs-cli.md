---
title: "Avvio rapido: Creare un BLOB con l'interfaccia della riga di comando di Azure"
titleSuffix: Azure Storage
description: Questa Guida di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB in un contenitore.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892924"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è l'esperienza della riga di comando di Azure per gestire le risorse di Azure. È possibile usarla nel browser con Azure Cloud Shell. È anche possibile installarla in macOS, Linux o Windows ed eseguirla dalla riga di comando. Questa guida introduttiva spiega come usare l'interfaccia della riga di comando di Azure per caricare e scaricare dati in Archiviazione BLOB di Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa guida introduttiva è necessario eseguire la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Per determinare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. È possibile organizzare i gruppi di BLOB in modo simile a come si organizzano i file in cartelle sul computer.

Creare un contenitore per l'archiviazione di BLOB con il comando [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Caricare un BLOB

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. Gli esempi in questa Guida di avvio rapido illustrano come usare i BLOB in blocchi.

Creare prima di tutto un file da caricare in un BLOB in blocchi. Se si usa Azure Cloud Shell, usare il comando seguente per creare un file:

```bash
vi helloworld
```

Quando si apre il file, premere **INS**. Digitare *Hello World*, quindi premere **ESC**. Digitare *:x* e quindi premere **INVIO**.

In questo esempio si usa il comando [az storage blob upload](/cli/azure/storage/blob) per caricare un BLOB nel contenitore creato nel passaggio precedente. Non è necessario specificare un percorso di file perché il file è stato creato nella directory radice:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Questa operazione crea il BLOB se non esiste o lo sovrascrive se esiste già. Caricare tutti i file desiderati prima di continuare.

Per caricare più file contemporaneamente, è possibile usare il comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore con il comando [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Scaricare un BLOB

Usare il comando [az storage blob download](/cli/azure/storage/blob) per scaricare il BLOB caricato in precedenza.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Trasferimento dati con AzCopy

L'utility [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'altra opzione per il trasferimento di dati script ad alte prestazioni per Archiviazione di Azure. È possibile usare AzCopy per trasferire i dati da e verso risorse di archiviazione BLOB, file e tabelle.

Nell'esempio seguente viene usato AzCopy per caricare un file denominato *myfile.txt* nel contenitore *sample-container*. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse appartenenti a un gruppo non sono più necessarie, incluso l'account di archiviazione creato nella presente Guida di avvio rapido, è possibile eliminare il gruppo di risorse con il comando [az group delete](/cli/azure/group). È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida di avvio rapido è stato descritto il trasferimento di file tra un file system locale e un contenitore in Archiviazione BLOB di Azure. Per altre informazioni sull'uso dei BLOB in Archiviazione di Azure, continuare con l'esercitazione per l'uso dell'archiviazione BLOB di Azure.

> [!div class="nextstepaction"]
> [Procedura: Operazioni di archiviazione BLOB con l'interfaccia della riga di comando di Azure](storage-how-to-use-blobs-cli.md)
