---
title: Gestione di condivisioni file di Azure con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire File di Azure.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Gestione di condivisioni file di Azure con l'interfaccia della riga di comando di Azure
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate in Windows, Linux e macOS. Questa guida contiene tutte le informazioni essenziali sull'uso delle condivisioni file di Azure con l'interfaccia della riga di comando di Azure. È possibile passare agli argomenti seguenti: 

> [!div class="checklist"]
> * Creare un gruppo di risorse e un account di archiviazione
> * Creare una condivisione file di Azure 
> * Creare una directory
> * Caricare un file 
> * Scaricare un file
> * Creare e usare uno snapshot di condivisione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, questa guida richiede la versione 2.0.4 o successiva dell'interfaccia della riga di comando di Azure. Eseguire **az --version** per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

Per impostazione predefinita, i comandi dell'interfaccia della riga di comando di Azure restituiscono JSON (JavaScript Object Notation), che è il modo effettivo di inviare e ricevere messaggi dalle API REST. Per semplificare l'uso delle risposte JSON, alcuni degli esempi di questa guida usano il parametro di query nei comandi dell'interfaccia della riga di comando di Azure. Questo parametro usa il [linguaggio di query JMESPath](http://jmespath.org/) per l'analisi di JSON. Per altre informazioni su come modificare i risultati dei comandi dell'interfaccia della riga di comando di Azure, seguire l'[esercitazione su JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Se non è già disponibile un gruppo di risorse di Azure, è possibile crearne uno nuovo con il comando [az group create](/cli/azure/group#create). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti orientali*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire la condivisione file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni file e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

Questo esempio crea un account di archiviazione denominato `mystorageaccount<random number>` usando il comando [az storage account create](/cli/azure/storage/account#create) e quindi inserisce il nome di tale account di archiviazione nella variabile `$STORAGEACCT`. I nomi degli account di archiviazione devono essere univoci. L'uso di `$RANDOM` consente di aggiungere un numero alla fine del nome dell'account di archiviazione rendendolo univoco. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione
Le chiavi dell'account di archiviazione vengono usate per controllare l'accesso alle risorse in un account di archiviazione. Vengono create automaticamente quando si crea un account di archiviazione. È possibile ottenere le chiavi per l'account di archiviazione usando il comando [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
È ora possibile creare la prima condivisione file di Azure. È possibile creare le condivisioni file usando il comando [az storage share create](/cli/azure/storage/share#create). Questo esempio crea una condivisione file di Azure denominata `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> I nomi condivisione devono essere costituiti da lettere minuscole, numeri e trattini singoli, ma non possono iniziare con un trattino. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Modifica dei contenuti della condivisione file di Azure
Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile modificare la condivisione file di Azure con l'interfaccia della riga di comando di Azure. Questo è un vantaggio rispetto al montaggio della condivisione file con SMB, perché tutte le richieste eseguite con l'interfaccia della riga di comando di Azure vengono effettuate con l'API REST per i file che consente di creare, modificare ed eliminare i file e le directory nella condivisione file da:

- Cloud Shell di Bash (che non può montare le condivisioni file tramite SMB).
- Client che non possono montare le condivisioni SMB, ad esempio client locali con la porta 445 bloccata.
- Scenari senza server, ad esempio in [Funzioni di Azure](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Creare la directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure, usare il comando [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Caricare un file
Per illustrare come caricare un file usando il comando [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), prima è necessario creare un file nell'unità dei file temporanei di Cloud Shell dell'interfaccia della riga di comando di Azure da caricare. Nell'esempio seguente il file viene creato e quindi caricato.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se si esegue l'interfaccia della riga di comando di Azure in locale, è consigliabile sostituire `~/clouddrive` con un interfaccia della riga di comando di Azure esistente nel computer.

Dopo il caricamento del file, è possibile usare il comando [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) per verificare che il file sia stato caricato nella condivisione file di Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Scaricare un file
È possibile usare il comando [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) per scaricare una copia del file caricato nell'unità dei file temporanei di Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Copiare i file
Un'attività comune è la copia dei file da una condivisione file a un'altra oppure a/da un contenitore di archiviazione BLOB di Azure. Per illustrare questa funzionalità, è possibile creare una nuova condivisione e copiare il file caricato in questa nuova condivisione usando il comando [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Se ora si elencano i file nella nuova condivisione, verrà visualizzato il file copiato.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Anche se il comando `az storage file copy start` è utile per gli spostamenti di file ad hoc tra condivisioni file di Azure e contenitori di archiviazione BLOB di Azure, è consigliabile usare AzCopy per gli spostamenti più grandi (in termini di numero o di dimensioni dei file da spostare). Sono disponibili altre informazioni su [AzCopy per Linux](../common/storage-use-azcopy-linux.md) e [AzCopy per Windows](../common/storage-use-azcopy.md). AzCopy deve essere installato in locale, non è disponibile in Cloud Shell 

## <a name="create-and-modify-share-snapshots"></a>Creare e modificare gli snapshot di condivisione
Un'altra utile attività da eseguire con una condivisione file di Azure è la creazione degli snapshot di condivisione. Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di condivisione sono simili alle tecnologie del sistema operativo con cui probabilmente si ha già familiarità, ad esempio:
- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS. 
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) per i file system Windows come NTFS e ReFS

È possibile eliminare uno snapshot di condivisione usando il comando [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Esplorare i contenuti di uno snapshot di condivisione
È possibile esplorare i contenuti di uno snapshot di condivisione passando il timestamp dello snapshot di condivisione acquisito nella variabile `$SNAPSHOT` al comando `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione
È possibile visualizzare l'elenco di snapshot acquisiti per la condivisione con il comando seguente.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
È possibile ripristinare un file con il comando `az storage file copy start` usato prima. Verrà prima di tutto eliminato il file `SampleUpload.txt` caricato per poterlo ripristinare dallo snapshot.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione usando il comando [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete), con la variabile contenente il riferimento `$SNAPSHOT` al parametro `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine, è possibile usare il comando [`az group delete`](/cli/azure/group#delete) per rimuovere il gruppo di risorse e tutte le risorse correlate. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

È anche possibile rimuovere le risorse singolarmente:
- Per rimuovere le condivisioni file di Azure create per questa guida introduttiva.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Per rimuovere l'account di archiviazione stesso. In questo modo verranno implicitamente rimosse le condivisioni file di Azure create, oltre a eventuali altre risorse di archiviazione create, ad esempio un contenitore di archiviazione BLOB di Azure.

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi
- [Gestione di condivisioni file con il portale di Azure](storage-how-to-use-files-portal.md)
- [Gestione di condivisioni file con Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gestione di condivisioni file con Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)