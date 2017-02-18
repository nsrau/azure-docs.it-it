---
title: Uso dell&quot;interfaccia della riga di comando di Azure 2.0 (Anteprima) con Archiviazione di Azure | Documentazione Microsoft
description: "Informazioni su come usare l&quot;interfaccia della riga di comando di Azure 2.0 (Anteprima) con Archiviazione di Azure per creare e gestire gli account di archiviazione e usare file e BLOB di Azure. L&quot;interfaccia della riga di comando di Azure 2.0 è uno strumento multipiattaforma."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 3136b8345d0c851c29a9498089da73c8564549d1
ms.openlocfilehash: 9a9ae2758aba17f35b7262560f8d980404ecd394


---
# <a name="using-the-azure-cli-20-preview-with-azure-storage"></a>Uso dell'interfaccia della riga di comando di Azure 2.0 (Anteprima) con Archiviazione di Azure

## <a name="overview"></a>Panoramica

L'interfaccia della riga di comando di Azure 2.0 (Anteprima), open-source e multipiattaforma, offre un insieme di comandi per usare la piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel [portale di Azure](https://portal.azure.com) incluso l'accesso ai dati complessi.

Questa guida illustra come usare l'[interfaccia della riga di comando di Azure 2.0 (Anteprima)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) per eseguire diverse attività con le risorse nell'account di Archiviazione di Azure. Prima di usare questa guida, si consiglia di scaricare e installare oppure di aggiornare il sistema alla versione più recente dell'interfaccia della riga di comando 2.0.

Gli esempi di questa guida presuppongono l'uso della shell Bash in Ubuntu, ma la procedura dovrebbe funzionare in modo simile anche nelle altre piattaforme. 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Prerequisiti
Questa guida si presuppone che si conoscano i concetti di base dell'archiviazione di Azure. e possa soddisfare i requisiti di creazione dell'account specificati di seguito per Azure e per il servizio Archiviazione.

### <a name="accounts"></a>Account
* **Account Azure**: se non si ha già una sottoscrizione di Azure, [creare un account Azure gratuito](https://azure.microsoft.com/free/).
* **Account di archiviazione**: vedere [Creare un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account) in [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

### <a name="install-the-azure-cli-20-preview"></a>Installare l'interfaccia della riga di comando di Azure 2.0 (Anteprima)

Scaricare e installare l'interfaccia della riga di comando di Azure 2.0 (Anteprima) seguendo le istruzioni riportate in [Installare l'interfaccia della riga di comando di Azure 2.0 (Anteprima)](/cli/azure/install-az-cli2).

> [!TIP]
> Se si riscontrano problemi con l'installazione, consultare la sezione [Installation Troubleshooting](/cli/azure/install-az-cli2#installation-troubleshooting) (Risoluzione dei problemi di installazione) dell'articolo e la guida [Install Troubleshooting](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) (Risoluzione dei problemi di installazione) su GitHub.
>

## <a name="working-with-the-cli"></a>Uso dell'interfaccia della riga di comando

Dopo l'installazione dell'interfaccia della riga di comando, sarà possibile usare il comando `az` nell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi. Digitare il comando `az` per visualizzare un output simile al seguente:

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 (Preview) components.
    configure : Configure Azure CLI 2.0 Preview or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    context   : Manage contexts.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove accesses to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

Nell'interfaccia della riga di comando eseguire il comando `az storage -h` per elencare i comandi del gruppo `storage` e i relativi sottogruppi. Le descrizioni dei sottogruppi forniscono una panoramica delle funzionalità che l'interfaccia della riga di comando offre per lavorare con le risorse di archiviazione.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
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
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

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
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!NOTE]
> L'output precedente è in formato **tabella**. È possibile specificare il formato di output da usare specificando l'argomento `--output` nei comandi dell'interfaccia della riga di comando oppure impostarlo a livello globale tramite `az configure`.
>

## <a name="manage-storage-accounts"></a>Gestire gli account di archiviazione

### <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.
Per usare Archiviazione di Azure, è necessario un account di archiviazione. Dopo aver configurato il computer per [connettersi alla sottoscrizione](#connect-to-your-azure-subscription), è possibile creare un nuovo account di Archiviazione di Azure.

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` [Richiesto]: posizione. Ad esempio "Stati Uniti occidentali".
* `-n` [Richiesto]: il nome dell'account di archiviazione. Il nome può contenere da 3 a 24 caratteri e usare solo numeri e lettere minuscole.
* `-g`[Richiesto]: il nome del gruppo di risorse.
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
az storage account show-connection-string -n <account_name> -g <resource_group>
```

Quindi copiare la stringa di connessione di output e impostare la variabile di ambiente `AZURE_STORAGE_CONNECTION_STRING` (potrebbe essere necessario racchiudere la stringa di connessione tra virgolette):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> Tutti gli esempi nelle sezioni seguenti di questo articolo presuppongono che siano state impostate le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Creare e gestire gli account di accesso
Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio BLOB](/rest/api/storageservices/fileservices/blob-service-concepts).

### <a name="create-a-container"></a>Creare un contenitore
Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. È possibile creare un contenitore usando il comando `az storage container create`:

```azurecli
az storage container create -n <container_name>
```

È possibile impostare uno fra tre livelli di accesso in lettura per un nuovo contenitore specificando l'argomento opzionale `--public-access`:

* `off`(impostazione predefinita): i dati del contenitore sono privati per il proprietario dell'account.
* `blob`: Accesso in lettura pubblico per i BLOB.
* `container`: Accesso in lettura e per elenchi pubblico all'intero contenitore.

Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Caricare un BLOB in un contenitore
Archiviazione BLOB di Azure supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. Caricare i BLOB in un contenitore utilizzando il comando `blob upload`:

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 Per impostazione predefinita il comando `blob upload` carica i file *.vhd in BLOB di pagine o altrimenti in BLOB in blocchi. Per specificare un altro tipo quando si carica un BLOB, è possibile usare l'argomento `--type`: i valori consentiti sono `append`, `block` e `page`.

 Per altre informazioni sui diversi tipi di BLOB, vedere [Informazioni sui BLOB in blocchi, sui BLOB di accodamento e sui BLOB di pagine](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="download-blobs-from-a-container"></a>Come scaricare i BLOB da un contenitore
Questo esempio dimostra come scaricare i BLOB da un contenitore:

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>Copiare i BLOB
È possibile copiare i BLOB tra aree e account di archiviazione in modo asincrono.

Nell'esempio seguente viene illustrato come copiare BLOB da un account di archiviazione a un altro. Prima di tutto si crea un contenitore in un altro account specificando che i suoi BLOB sono accessibili pubblicamente in modo anonimo. Quindi si carica un file nel contenitore e infine si copia il BLOB dal contenitore nel contenitore **mycontainer** dell'account corrente.

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

L'URL del BLOB di origine (specificato da `-u`) deve essere accessibile pubblicamente o includere un token di firma di accesso condiviso.

### <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un BLOB, usare il comando `blob delete`:

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Creare e gestire condivisioni di file
L'archiviazione file di Azure offre un'archiviazione condivisa per le applicazioni che usano il protocollo Server Message Block (SMB). Macchine virtuali di Microsoft Azure e servizi cloud, nonché applicazioni locali, possono condividere i dati di file tra condivisioni montate. È possibile gestire condivisioni di file e dati di file tramite la CLI di Azure. Per altre informazioni su Archiviazione file di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md) o [Come usare Archiviazione file di Azure con Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Creare una condivisione file
Una condivisione di archiviazione file è una condivisione file SMB di Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione. Nell'esempio seguente viene creata una condivisione file denominata **myshare**.

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>Creare una directory
Una directory fornisce una struttura gerarchica facoltativa per una condivisione di file di Azure. Nell'esempio seguente viene creata una directory denominata **myDir** nella condivisione di file.

```azurecli
az storage directory create -n myDir -s myshare
```

Si noti che tale percorso di directory può includere più livelli, *ad esempio*, **un / b**. Tuttavia è necessario assicurarsi che tutte le directory padre esistano. Ad esempio, per il percorso **a/b**, è necessario creare prima la directory **a** e poi la directory **b**.

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
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
```

### <a name="copy-files"></a>Copiare i file      
È possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Ad esempio, per copiare un file in una directory di un'altra condivisione:        
        
```azurecli
# Get the URL for the source file you want to copy
az storage file url -s myshare -p /mydir/image.png

# Copy the file to another share
az storage file copy start \
    --source-uri https://mystorageaccount.file.core.windows.net/myshare/mydir/image.png \   
    --destination-share myshare2 --destination-path mydir2/image.png        
```

> [!NOTE]
> Un problema noto nell'interfaccia della riga di comando 2.0 (Anteprima) impedisce l'uso di `--source-share` e `--source-path`. È possibile usare l'argomento `--source-uri` come soluzione temporanea finché il problema non sarà risolto.
>

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono riportate altre risorse con altre informazioni sull'uso dell'interfaccia della riga di comando di Azure 2.0 (Anteprima).

* [Introduzione all'interfaccia della riga di comando di Azure 2.0 (Anteprima)](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Riferimento ai comandi dell'interfaccia della riga di comando di Azure 2.0 (Anteprima)](/cli/azure)
* [Interfaccia della riga di comando di Azure 2.0 (Anteprima) su GitHub](https://github.com/Azure/azure-cli)



<!--HONumber=Jan17_HO5-->


