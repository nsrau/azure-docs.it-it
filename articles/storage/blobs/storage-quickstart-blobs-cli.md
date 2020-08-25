---
title: "Avvio rapido: Creare un BLOB con l'interfaccia della riga di comando di Azure"
titleSuffix: Azure Storage
description: Questa Guida di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB in un contenitore.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55cbf0a304bbf13d47fefad0981c0143c101bbb0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520771"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Avvio rapido: Caricare, scaricare ed elencare BLOB con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è l'esperienza della riga di comando di Azure per gestire le risorse di Azure. È possibile usarla nel browser con Azure Cloud Shell. È anche possibile installarla in macOS, Linux o Windows ed eseguirla dalla riga di comando. Questa guida introduttiva spiega come usare l'interfaccia della riga di comando di Azure per caricare e scaricare dati in Archiviazione BLOB di Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Installare l'interfaccia della riga di comando di Azure in locale

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questo argomento di avvio rapido è necessario usare la versione 2.0.46 o successiva. Per determinare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Se si esegue l'interfaccia della riga di comando di Azure in locale, è necessario effettuare l'accesso e l'autenticazione. Se si usa Azure Cloud Shell, questo passaggio non è necessario. Per accedere all'interfaccia della riga di comando di Azure, eseguire `az login` e quindi l'autenticazione nella finestra del browser:

```azurecli
az login
```

Per altre informazioni sull'autenticazione con l'interfaccia della riga di comando di Azure, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autorizzare l'accesso all'archiviazione BLOB

È possibile autorizzare l'accesso all'archiviazione BLOB dall'interfaccia della riga di comando di Azure con le credenziali di Azure AD o usando la chiave di accesso dell'account di archiviazione. È consigliabile usare le credenziali di Azure AD. Questo articolo illustra come autorizzare le operazioni con l'archiviazione BLOB tramite Azure AD.

I comandi dell'interfaccia della riga di comando di Azure per le operazioni sui dati dell'archiviazione BLOB supportano il parametro `--auth-mode`, che consente di specificare come autorizzare una determinata operazione. Impostare il parametro `--auth-mode` su `login` per concedere l'autorizzazione con le credenziali di Azure AD. Per altre informazioni, vedere [Autorizzare l'accesso ai dati di BLOB o code con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Solo le operazioni sui dati dell'archiviazione BLOB supportano il parametro `--auth-mode`. Le operazioni di gestione, ad esempio la creazione di un gruppo di risorse o di un account di archiviazione, usano automaticamente le credenziali di Azure AD per l'autorizzazione.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con il comando [az group create](/cli/azure/group). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un account di archiviazione per utilizzo generico con il comando [az storage account create](/cli/azure/storage/account). L'account di archiviazione per utilizzo generico può essere usato per tutti e quattro i servizi: BLOB, file, tabelle e code.

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. È possibile organizzare i gruppi di BLOB in contenitori, modo simile a come si organizzano i file in cartelle sul computer. Creare un contenitore per l'archiviazione di BLOB con il comando [az storage container create](/cli/azure/storage/container).

Nell'esempio seguente viene usato l'account Azure AD per autorizzare l'operazione di creazione del contenitore. Prima di creare il contenitore, assegnare il ruolo [Collaboratore ai dati dei BLOB di archiviazione](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a se stessi. Anche se si è il proprietario dell'account, sono necessarie autorizzazioni esplicite per eseguire operazioni sui dati nell'account di archiviazione. Per altre informazioni sull'assegnazione di ruoli di Azure, vedere [Usare l'interfaccia della riga di comando di Azure per assegnare un ruolo di Azure per l'accesso](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

È anche possibile usare la chiave dell'account di archiviazione per autorizzare l'operazione di creazione del contenitore. Per altre informazioni sull'autorizzazione delle operazioni sui dati con l'interfaccia della riga di comando di Azure, vedere [Autorizzare l'accesso ai dati di BLOB o code con l'interfaccia della riga di comando di Azure](../common/authorize-data-operations-cli.md?toc=/azure/storage/blobs/toc.json).

## <a name="upload-a-blob"></a>Caricare un BLOB

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. Gli esempi in questa Guida di avvio rapido illustrano come usare i BLOB in blocchi.

Creare prima di tutto un file da caricare in un BLOB in blocchi. Se si usa Azure Cloud Shell, usare il comando seguente per creare un file:

```bash
vi helloworld
```

Quando si apre il file, premere **INS**. Digitare *Hello World*, quindi premere **ESC**. Digitare *:x* e quindi premere **INVIO**.

In questo esempio si usa il comando [az storage blob upload](/cli/azure/storage/blob) per caricare un BLOB nel contenitore creato nel passaggio precedente. Non è necessario specificare un percorso perché il file è stato creato nella directory radice. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Questa operazione crea il BLOB se non esiste o lo sovrascrive se esiste già. Caricare tutti i file desiderati prima di continuare.

Per caricare più file contemporaneamente, è possibile usare il comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB nel contenitore con il comando [az storage blob list](/cli/azure/storage/blob). È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Scaricare un BLOB

Usare il comando [az storage blob download](/cli/azure/storage/blob) per scaricare il BLOB caricato in precedenza. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Trasferimento dati con AzCopy

L'utilità da riga di comando AzCopy offre il trasferimento dei dati di script a elevate prestazioni per Archiviazione di Azure. È possibile usare AzCopy per trasferire i dati da e verso Archiviazione BLOB e File di Azure. Per altre informazioni su AzCopy v10, la versione più recente, vedere [Introduzione ad AzCopy](../common/storage-use-azcopy-v10.md). Per informazioni sull'uso di AzCopy v10 con archiviazione BLOB, vedere [Trasferire dati con AzCopy e archiviazione BLOB](../common/storage-use-azcopy-blobs.md).

Gli esempi seguenti usano AzCopy per caricare un file locale in un BLOB. Ricordarsi di sostituire i valori dell'esempio con i propri valori:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vogliono eliminare le risorse create come parte di questo argomento di avvio rapido, incluso l'account di archiviazione, eliminare il gruppo di risorse usando il comando [az group delete](/cli/azure/group). È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida di avvio rapido è stato descritto il trasferimento di file tra un file system locale e un contenitore in Archiviazione BLOB di Azure. Per altre informazioni sull'uso dell'archiviazione BLOB con l'interfaccia della riga di comando di Azure, esplorare gli esempi dell'interfaccia della riga di comando di Azure per l'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Esempi di interfaccia della riga di comando di Azure per l'archiviazione BLOB di Azure](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
