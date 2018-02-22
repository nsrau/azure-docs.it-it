---
title: Uso dell'interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure | Documentazione Microsoft
description: "Informazioni su come usare l'interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure per creare e gestire gli account di archiviazione e usare file e BLOB di Azure. L'interfaccia della riga di comando di Azure 2.0 è uno strumento multipiattaforma compilato in Python."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.openlocfilehash: 509c702054961c9d9fa525242ce0542059e32d81
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Uso dell'interfaccia della riga di comando di Azure 2.0 con Archiviazione di Azure

L'interfaccia della riga di comando di Azure 2.0, open-source e multipiattaforma, offre un insieme di comandi per usare la piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel [portale di Azure](https://portal.azure.com) incluso l'accesso ai dati complessi.

Questa guida illustra come usare l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) per eseguire diverse attività con le risorse nell'account di Archiviazione di Azure. Prima di usare questa guida, si consiglia di scaricare e installare oppure di aggiornare il sistema alla versione più recente dell'interfaccia della riga di comando 2.0.

Gli esempi di questa guida presuppongono l'uso della shell Bash in Ubuntu, ma la procedura dovrebbe funzionare in modo simile anche nelle altre piattaforme. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>prerequisiti
Questa guida si presuppone che si conoscano i concetti di base dell'archiviazione di Azure. e possa soddisfare i requisiti di creazione dell'account specificati di seguito per Azure e per il servizio Archiviazione.

### <a name="accounts"></a>Account
* **Account Azure**: se non si ha già una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/).
* **Account di archiviazione**: vedere [Creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Installare l'interfaccia della riga di comando di Azure 2.0.

Scaricare e installare l'interfaccia della riga di comando di Azure 2.0 seguendo le istruzioni riportate in [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Se si riscontrano problemi con l'installazione, consultare la sezione [Installation Troubleshooting](/cli/azure/install-az-cli2#installation-troubleshooting) (Risoluzione dei problemi di installazione) dell'articolo e la guida [Install Troubleshooting](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) (Risoluzione dei problemi di installazione) su GitHub.
>

## <a name="working-with-the-cli"></a>Uso dell'interfaccia della riga di comando

Dopo l'installazione dell'interfaccia della riga di comando, sarà possibile usare il comando `az` nell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi. Digitare il comando `az` per visualizzare un elenco completo dei comandi di base (l'output di esempio seguente è stato troncato):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Nell'interfaccia della riga di comando eseguire il comando `az storage --help` per elencare i sottogruppi del comando `storage`. Le descrizioni dei sottogruppi forniscono una panoramica delle funzionalità che l'interfaccia della riga di comando offre per lavorare con le risorse di archiviazione.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connettere l'interfaccia della riga di comando alla sottoscrizione di Azure

Per lavorare con le risorse nella sottoscrizione di Azure è necessario innanzitutto accedere al proprio account Azure con `az login`. È possibile eseguire l'accesso in diversi modi:

* **Accesso interattivo**: `az login`
* **Accesso con nome utente e password**: `az login -u johndoe@contoso.com -p VerySecret`
  * Questo non funziona con gli account Microsoft o gli account che usano Multi-Factor Authentication.
* **Accesso con un'entità servizio**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Script di esempio dell'interfaccia della riga di comando di Azure 2.0

Successivamente useremo un piccolo script della shell che attiva alcuni semplici comandi dell'interfaccia della riga di comando di Azure 2.0 per interagire con risorse di Archiviazione di Azure. Innanzitutto lo script crea un nuovo contenitore nell'account di archiviazione e poi carica un file esistente (come un BLOB) in tale contenitore. Quindi genera un elenco di tutti i BLOB nel contenitore e infine scarica il file in una destinazione specificata nel computer locale.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Configurare ed eseguire lo script**

1. Aprire un editor di testo quindi copiare e incollare lo script precedente nell'editor.

2. A questo punto aggiornare le variabili dello script in modo che riflettano le impostazioni di configurazione. Sostituire i seguenti valori come indicato:

   * **\<storage_account_name\>** Il nome dell'account di archiviazione.
   * **\<storage_account_key\>** La chiave di accesso primaria o secondaria per l'account di archiviazione.
   * **\<container_name\>** Un nome per il nuovo contenitore da creare, come "azure-cli-sample-container".
   * **\<blob_name\>** Un nome per il BLOB di destinazione nel contenitore.
   * **\<file_to_upload\>** Il percorso di un piccolo file nel computer locale, come "~/images/HelloWorld.png".
   * **\<destination_file\>** Il percorso del file di destinazione, come "~/downloadedImage.png".

3. Dopo aver aggiornato le variabili necessarie, salvare lo script e chiudere l'editor. I passaggi successivi presuppongono che allo sript sia stato assegnato il nome **my_storage_sample.sh**.

4. Contrassegnare lo script come eseguibile, se necessario:`chmod +x my_storage_sample.sh`

5. Eseguire lo script. Ad esempio, in Bash: `./my_storage_sample.sh`

Verrà visualizzato un output simile al seguente e il **\<destination_file\>** specificato nello script dovrebbe essere visualizzato sul computer locale.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> L'output precedente è in formato **tabella**. È possibile specificare il formato di output da usare specificando l'argomento `--output` nei comandi dell'interfaccia della riga di comando oppure impostarlo a livello globale tramite `az configure`.
>

## <a name="manage-storage-accounts"></a>Gestire gli account di archiviazione

### <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.
Per usare Archiviazione di Azure, è necessario un account di archiviazione. Dopo aver configurato il computer per [connettersi alla sottoscrizione](#connect-to-your-azure-subscription), è possibile creare un nuovo account di Archiviazione di Azure.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Richiesto]: posizione. Ad esempio "Stati Uniti occidentali".
* `--name` [Richiesto]: il nome dell'account di archiviazione. Il nome può contenere da 3 a 24 caratteri e usare solo numeri e lettere minuscole.
* `--resource-group`[Richiesto]: il nome del gruppo di risorse.
* `--sku` [Richiesto]: lo SKU dell'account di archiviazione. Valori consentiti:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Impostare le variabili di ambiente predefinite dell'account di archiviazione di Azure
È possibile avere più account di archiviazione nella sottoscrizione di Azure. Per selezionarne uno da usare per tutti i comandi di archiviazione successivi, è possibile impostare queste variabili di ambiente:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Un altro modo per impostare un account di archiviazione predefinito è mediante una stringa di connessione. In primo luogo ottenere la stringa di connessione con il comando `show-connection-string`:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Quindi copiare la stringa di connessione di output e impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` (potrebbe essere necessario racchiudere la stringa di connessione tra virgolette):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Tutti gli esempi nelle sezioni seguenti di questo articolo presuppongono che siano state impostate le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Creare e gestire gli account di accesso
Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio BLOB](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Creare un contenitore
Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. È possibile creare un contenitore usando il comando `az storage container create`:

```azurecli
az storage container create --name <container_name>
```

È possibile impostare uno fra tre livelli di accesso in lettura per un nuovo contenitore specificando l'argomento opzionale `--public-access`:

* `off`(impostazione predefinita): i dati del contenitore sono privati per il proprietario dell'account.
* `blob`: Accesso in lettura pubblico per i BLOB.
* `container`: Accesso in lettura e per elenchi pubblico all'intero contenitore.

Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Caricare un BLOB in un contenitore
Archiviazione BLOB di Azure supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. Caricare i BLOB in un contenitore utilizzando il comando `blob upload`:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Per impostazione predefinita il comando `blob upload` carica i file *.vhd in BLOB di pagine o altrimenti in BLOB in blocchi. Per specificare un altro tipo quando si carica un BLOB, è possibile usare l'argomento `--type`: i valori consentiti sono `append`, `block` e `page`.

 Per altre informazioni sui diversi tipi di BLOB, vedere [Informazioni sui BLOB in blocchi, sui BLOB di accodamento e sui BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Scaricare un BLOB da un contenitore
Questo esempio dimostra come scaricare i BLOB da un contenitore:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB in un contenitore con il comando [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Copiare i BLOB
È possibile copiare i BLOB tra aree e account di archiviazione in modo asincrono.

Nell'esempio seguente viene illustrato come copiare BLOB da un account di archiviazione a un altro. Prima di tutto si crea un contenitore nell'account di archiviazione di origine, specificando l'accesso in lettura pubblico per i suoi BLOB. Quindi si carica un file nel contenitore e infine si copia il BLOB dal contenitore in un contenitore nell'account di archiviazione di destinazione.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

Nell'esempio precedente, perché l'operazione di copia riesca il contenitore di destinazione deve esistere già nell'account di archiviazione di destinazione. Inoltre, il BLOB di origine specificato nell'argomento `--source-uri` deve includere un token di firma di accesso condiviso oppure essere accessibile pubblicamente, come in questo esempio.

### <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un BLOB, usare il comando `blob delete`:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Creare e gestire condivisioni di file
File di Azure offre un'archiviazione condivisa per le applicazioni che usano il protocollo Server Message Block. Macchine virtuali di Microsoft Azure e servizi cloud, nonché applicazioni locali, possono condividere i dati di file tra condivisioni montate. È possibile gestire condivisioni di file e dati di file tramite la CLI di Azure. Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Creare una condivisione file
Una condivisione di archiviazione file è una condivisione file SMB di Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione. Nell'esempio seguente viene creata una condivisione file denominata **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Creare una directory
Una directory fornisce una struttura gerarchica in una condivisione di file di Azure. Nell'esempio seguente viene creata una directory denominata **myDir** nella condivisione di file.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Un percorso di directory può includere più livelli, ad esempio **dir1/dir2**. Tuttavia, prima di creare una sottodirectory, è necessario assicurarsi che tutte le directory padre esistano. Ad esempio, per il percorso **dir1/dir2**, è necessario creare prima la directory **dir1** e poi la directory **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Caricare un file locale da condividere
Nell'esempio seguente viene caricato un file da **~/temp/samplefile.txt** nella radice della condivisione file **myshare**. L'argomento `--source` specifica il file locale esistente da caricare.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Come con la creazione di directory, è possibile specificare un percorso di directory all'interno della condivisione per caricare il file in una directory esistente all'interno della condivisione:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Un file della condivisione può avere una dimensione massima di 1 TB.

### <a name="list-the-files-in-a-share"></a>Elencare i file di una condivisione
È possibile elencare i file e le directory di una condivisione tramite il comando `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Copiare i file      
È possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Ad esempio, per copiare un file in una directory di un'altra condivisione:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Creare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione usando il comando `az storage share snapshot`:

```cli
az storage share snapshot -n <share name>
```

Output di esempio
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione

È possibile elencare gli snapshot di condivisione di una specifica condivisione usando `az storage share list --include-snapshots`.

```cli
az storage share list --include-snapshots
```

**Output di esempio**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Esplorare gli snapshot di condivisione
È inoltre possibile esplorare un determinato snapshot di condivisione per visualizzarne il contenuto usando `az storage file list`. È necessario specificare il nome condivisione `--share-name <snare name>` e il timestamp `--snapshot '2017-10-04T19:45:18.0000000Z'`.

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Output di esempio**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Eseguire il ripristino da snapshot di condivisione

È possibile ripristinare un file copiandolo o scaricandolo da uno snapshot di condivisione usando il comando `az storage file download`.

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Output di esempio**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Eliminare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione tramite il comando `az storage share delete` fornendo il parametro `--snapshot` con il timestamp dello snapshot di condivisione:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Output di esempio
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono segnalate altre risorse relative all'utilizzo dell'interfaccia della riga di comando di Azure 2.0.

* [Introduzione all'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Riferimento ai comandi dell'interfaccia della riga di comando di Azure 2.0](/cli/azure)
* [Interfaccia della riga di comando di Azure 2.0 su GitHub](https://github.com/Azure/azure-cli)
