---
title: Guida introduttiva per la gestione di condivisioni file di Azure con l'interfaccia della riga di comando di Azure
description: Questa guida di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per gestire File di Azure. Creare un gruppo di risorse e un account di archiviazione e quindi creare e usare una condivisione file di Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9c569e65546853c4e9c8c460d29041e4bb829c09
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564203"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Guida introduttiva: Creare e gestire condivisioni file di Azure con l'interfaccia della riga di comando di Azure
Questa guida contiene tutte le informazioni essenziali sull'uso delle [condivisioni file di Azure](storage-files-introduction.md) con l'interfaccia della riga di comando di Azure. Le condivisioni file di Azure sono esattamente come le altre condivisioni file, ma vengono archiviate nel cloud e sono supportate dalla piattaforma Azure. Le condivisioni file di Azure supportano il protocollo SMB (Server Message Block) standard di settore, il protocollo NFS (Network File System) (anteprima) e consentono la condivisione di file in più computer, applicazioni e istanze. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per le procedure in questo articolo è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire **az --version** per trovare la versione dell'interfaccia della riga di comando di Azure in uso. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli). 

Per impostazione predefinita, i comandi dell'interfaccia della riga di comando di Azure restituiscono codice JSON (JavaScript Object Notation). JSON è il formato standard per l'invio e la ricezione di messaggi dalle API REST. Per facilitare l'uso delle risposte JSON, alcuni esempi in questo articolo usano il parametro *query* nei comandi dell'interfaccia della riga di comando di Azure. Questo parametro usa il [linguaggio di query JMESPath](http://jmespath.org/) per analizzare il codice JSON. Per altre informazioni su come usare i risultati dei comandi dell'interfaccia della riga di comando di Azure seguendo il linguaggio di query JMESPath, vedere l'[esercitazione di JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Se non è già disponibile un gruppo di risorse di Azure, è possibile usare il comando [az group create](/cli/azure/group) per crearne uno. 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti occidentali 2*:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire condivisioni file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni file. In una condivisione può essere archiviato un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

L'esempio seguente crea un nuovo account di archiviazione usando il comando [az storage account create](/cli/azure/storage/account). I nomi degli account di archiviazione devono essere univoci, quindi usare `$RANDOM` per aggiungere un numero al nome e renderlo univoco.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Le condivisioni di dimensioni maggiori di 5 TiB (fino a un massimo di 100 TiB per condivisione) sono disponibili solo negli account di archiviazione con ridondanza locale (LRS) e con ridondanza della zona (ZRS). Per creare un account di archiviazione con ridondanza geografica (GRS) o con ridondanza geografica della zona (GZRS), rimuovere il parametro `--enable-large-file-share`.

### <a name="get-the-storage-account-key"></a>Ottenere la chiave dell'account di archiviazione
Le chiavi dell'account di archiviazione controllano l'accesso alle risorse in un account di archiviazione. Vengono create automaticamente quando si crea un account di archiviazione. È possibile ottenere le chiavi per l'account di archiviazione con il comando [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
È ora possibile creare la prima condivisione file di Azure. Creare le condivisioni file con il comando [az storage share create](/cli/azure/storage/share). Questo esempio crea una condivisione file di Azure denominata *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

I nomi delle condivisioni possono contenere solo lettere minuscole, numeri e trattini singoli (non possono però iniziare con un trattino). Per informazioni dettagliate su come denominare le condivisioni e i file, vedere [Naming and referencing shares, directories, files, and metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Denominazione e riferimento a condivisioni, directory, file e metadati).

## <a name="use-your-azure-file-share"></a>Usare la condivisione file di Azure
File di Azure offre due metodi per usare i file e le cartelle all'interno della condivisione file di Azure: il [protocollo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard di settore e il [protocollo REST di File](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Per montare una condivisione file con SMB, vedere il documento riportato di seguito in base al sistema operativo in uso:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Uso di una condivisione file di Azure con il protocollo REST di File 
È possibile usare il protocollo REST File direttamente, ovvero creando personalmente chiamate HTTP REST, ma il metodo più comune consiste nell'usare l'interfaccia della riga di comando di Azure, il [modulo di Azure PowerShell](storage-how-to-use-files-powershell.md) o un SDK di Archiviazione di Azure, perché offrono un pratico wrapper per il protocollo REST File nel linguaggio di scripting/programmazione preferito.  

È previsto che la maggior parte degli utenti di File di Azure vorrà usare la propria condivisione file di Azure tramite il protocollo SMB, perché questo consente loro di usare le applicazioni e gli strumenti esistenti con cui possono avere familiarità, ma esistono diversi motivi per cui è vantaggioso usare l'API REST di File invece di SMB, ad esempio:

- Si esplora la condivisione file da Azure Cloud Shell di Bash, che non può montare condivisioni file tramite SMB.
- Si sfruttano risorse serverless, ad esempio [Funzioni di Azure](../../azure-functions/functions-overview.md). 
- Si crea un servizio a valore aggiunto che interagisce con molte condivisioni file di Azure, ad esempio l'esecuzione di backup o analisi antivirus.

Gli esempi seguenti mostrano come usare l'interfaccia della riga di comando di Azure per modificare la condivisione file di Azure con il protocollo REST File. 

### <a name="create-a-directory"></a>Creare una directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure, usare il comando [`az storage directory create`](/cli/azure/storage/directory):

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Caricare un file
Per una dimostrazione del caricamento di un file con il comando [`az storage file upload`](/cli/azure/storage/file), per prima cosa creare un file da caricare nell'unità dei file temporanei di Cloud Shell. Nell'esempio seguente si crea e quindi si carica il file:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se si esegue l'interfaccia della riga di comando di Azure in locale, sostituire `~/clouddrive` con un percorso esistente nel computer.

Dopo il caricamento del file, è possibile usare il comando [`az storage file list`](/cli/azure/storage/file) per verificare che il file sia stato caricato nella condivisione file di Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Scaricare un file
È possibile usare il comando [`az storage file download`](/cli/azure/storage/file) per scaricare una copia del file caricato nell'unità dei file temporanei di Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Copiare i file
Un'attività comune è la copia di file da una condivisione file a un'altra. Per una dimostrazione di questa funzionalità, creare una nuova condivisione. Copiare il file caricato in questa nuova condivisione usando il comando [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Se ora si elencano i file nella nuova condivisione, verrà visualizzato il file copiato:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Sebbene il comando `az storage file copy start` sia utile per gli spostamenti di file tra condivisioni file di Azure, per migrazioni e spostamenti di dati di dimensioni maggiori, è consigliabile usare `rsync` in macOS e Linux e `robocopy` in Windows. `rsync` e `robocopy` usano SMB per eseguire gli spostamenti dei dati invece dell'API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Creare e gestire gli snapshot di condivisione
Un'altra attività utile che è possibile eseguire con una condivisione file di Azure è la creazione di snapshot di condivisione. Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di condivisione sono simili ad alcune tecnologie del sistema operativo con cui probabilmente si ha già familiarità:

- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux.
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS.
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) per i file system Windows, come NTFS e ReFS.
 
È possibile creare uno snapshot di condivisione con il comando [`az storage share snapshot`](/cli/azure/storage/share):

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Esplorare i contenuti di uno snapshot di condivisione
È possibile esplorare il contenuto di uno snapshot di condivisione passando il timestamp dello snapshot di condivisione acquisito nella variabile `$snapshot` al comando `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione
Per visualizzare l'elenco degli snapshot creati per la condivisione, usare il comando seguente:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
È possibile ripristinare un file con il comando `az storage file copy start` usato in precedenza. Per prima cosa, eliminare il file SampleUpload.txt che è stato caricato, per poterlo ripristinare dallo snapshot:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione con il comando [`az storage share delete`](/cli/azure/storage/share). Usare la variabile contenente il riferimento `$SNAPSHOT` al parametro `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine, è possibile usare il comando [`az group delete`](/cli/azure/group) per rimuovere il gruppo di risorse e tutte le risorse correlate: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

In alternativa, è possibile rimuovere le risorse singolarmente.
- Per rimuovere le condivisioni file di Azure create per questo articolo:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Per rimuovere l'account di archiviazione, rimuovendo così implicitamente le condivisioni file di Azure e le eventuali altre risorse di archiviazione, come ad esempio un contenitore di archiviazione BLOB di Azure, che sono state create:

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Che cos'è File di Azure?](storage-files-introduction.md)
