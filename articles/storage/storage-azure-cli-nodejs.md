---
title: Uso dell&quot;interfaccia della riga di comando 1.0 di Azure con Archiviazione di Azure | Documentazione Microsoft
description: "Informazioni su come usare l&quot;interfaccia della riga di comando 1.0 di Azure con Archiviazione di Azure per creare e gestire gli account di archiviazione e usare file e BLOB di Azure. La CLI di Azure è uno strumento multipiattaforma"
services: storage
documentationcenter: na
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: b502232a-e8f6-4d6c-befd-3476592e0e35
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b246d8813a41d353a9c0fa31fe838e025fc93046
ms.lasthandoff: 03/21/2017


---
# <a name="using-the-azure-cli-10-with-azure-storage"></a>Uso dell'interfaccia della riga di comando 1.0 di Azure con Archiviazione di Azure

## <a name="overview"></a>Panoramica

L'interfaccia della riga di comando di Azure fornisce un insieme di comandi open source e multipiattaforma per utilizzare la piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel [portale di Azure](https://portal.azure.com) , nonché funzionalità di accesso ai dati complessi.

In questa guida verrà illustrato come usare l'[interfaccia della riga di comando di Azure (Azure CLI)](../cli-install-nodejs.md) per eseguire una serie di attività di sviluppo e amministrazione con Archiviazione di Azure. Si consiglia di scaricare e installare oppure di aggiornare il modulo alla versione di Azure PowerShell più recente prima di usare questa guida.

Questa guida si presuppone che si conoscano i concetti di base dell'archiviazione di Azure. La guida fornisce diversi script che mostrano come usare PowerShell con Archiviazione di Azure. Prima di eseguire gli script, è necessario aggiornarne le variabili in base alla configurazione.

> [!NOTE]
> La guida fornisce esempi di comandi e script dell'interfaccia della riga di comando di Azure per gli account di archiviazione della versione classica. Vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Management](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) per i comandi dell'interfaccia della riga di comando di Azure per gli account di archiviazione di Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Iniziare a utilizzare archiviazione di Azure e Azure CLI in 5 minuti
In questa guida utilizza Ubuntu per gli esempi, ma altre piattaforme del sistema operativo devono eseguire in modo analogo.

**Novità in Azure:** ottenere una sottoscrizione di Microsoft Azure e un account Microsoft associato alla sottoscrizione. Per informazioni sulle opzioni di acquisto di Azure, vedere la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/), le [opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/) e le [offerte per i membri](https://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network, BizSpark e altri programmi Microsoft).

Per altre informazioni sulle sottoscrizioni di Azure, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Dopo aver creato una sottoscrizione e un account di Microsoft Azure:**

1. Scaricare e installare CLI Azure seguendo le istruzioni riportate nel [installare CLI Azure](../cli-install-nodejs.md).
2. Dopo l'installazione dell'interfaccia della riga di comando di Azure, sarà possibile utilizzare il comando azure dall'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) per accedere ai relativi comandi. Digitare il comando _azure_. Verrà visualizzato l'output seguente.

    ![Output del comando di esempio:][Image1]
3. Nell'interfaccia della riga di comando, digitare `azure storage` per elencare tutti i comandi di archiviazione di azure e ottenere una panoramica delle funzionalità offerte dall'interfaccia della riga di comando di Azure. È possibile digitare il nome del comando con parametro **-h** (ad esempio, `azure storage share create -h`) per visualizzare i dettagli della sintassi del comando.
4. A questo punto viene fornito uno script semplice che mostra i comandi di base dell'interfaccia della riga di comando di Azure per accedere ad Archiviazione di Azure. Lo script prima verrà chiesto di impostare due variabili per l'account di archiviazione e la chiave. Lo script crea un nuovo contenitore in questo nuovo account di archiviazione e carica un file di immagine esistente (BLOB) in tale contenitore. Dopo aver elencato tutti i BLOB nel contenitore, lo script crea una nuova directory di destinazione nel computer locale e scarica il file di immagine.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. Nel computer locale, aprire l'editor di testo preferito (vim ad esempio). Digitare lo script precedente nell'editor di testo.
6. A questo punto, è necessario aggiornare le variabili dello script in base alle impostazioni di configurazione.

   * **<storage_account_name>**: usare il nome specificato nello script oppure immettere un nuovo nome per l'account di archiviazione. **Importante:** il nome dell'account di archiviazione deve essere univoco in Azure. Utilizzare caratteri minuscoli.
   * **<storage_account_key>**: la chiave di accesso dell'account di archiviazione.
   * **<container_name>**: usare il nome specificato nello script oppure immettere un nuovo nome per il contenitore.
   * **<image_to_upload>**: immettere il percorso di un'immagine nel computer locale, ad esempio "~/images/HelloWorld.png".
   * **<destination_folder>**: immettere il percorso di una directory locale per archiviare i file scaricati da Archiviazione di Azure, ad esempio "~/downloadImages".
7. Dopo avere aggiornato le variabili necessarie in vim, premere le combinazioni di tasti `ESC`, `:`, `wq!` per salvare lo script.
8. Per eseguire questo script, è sufficiente digitare il nome del file script nella console di bash. Dopo l'esecuzione dello script è necessario disporre di una cartella di destinazione locale che includa il file di immagine scaricato. La schermata seguente mostra un output di esempio:

Dopo l'esecuzione dello script è necessario disporre di una cartella di destinazione locale che includa il file di immagine scaricato.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Gestione degli account di archiviazione con Azure CLI
### <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure
Sebbene la maggior parte dei comandi di archiviazione funzioni senza una sottoscrizione di Azure, è consigliabile connettersi alla sottoscrizione dall'interfaccia della riga di comando di Azure. Per configurare l'interfaccia della riga di comando di Azure per l'uso con la sottoscrizione, seguire la procedura nell'argomento [Connessione a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Creare un nuovo account di archiviazione.
Per usare Archiviazione di Azure, è necessario un account di archiviazione. Dopo aver configurato il computer per connettersi alla sottoscrizione, è possibile creare un nuovo account di archiviazione di Azure.

```azurecli
azure storage account create <account_name>
```

Il nome dell'account di archiviazione deve essere di lunghezza compresa tra 3 e 24 caratteri e utilizzare numeri e lettere minuscole solo.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Impostare un account di archiviazione Azure predefinito nelle variabili di ambiente
È possibile avere più account di archiviazione nella sottoscrizione. È possibile scegliere uno di essi e impostarlo in variabili di ambiente per tutti i comandi di archiviazione nella stessa sessione. Questo consente di eseguire i comandi di archiviazione di Azure PowerShell senza specificare in modo esplicito il contesto di archiviazione.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Un altro modo per impostare un account di archiviazione predefinito utilizza stringa di connessione. In primo luogo per ottenere la stringa di connessione comando:

```azurecli
azure storage account connectionstring show <account_name>
```

Quindi copiare la stringa di connessione di output e impostare alla variabile di ambiente:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Creare e gestire gli account di accesso
Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione BLOB di Azure. Per informazioni dettagliate, vedere [Introduzione all'archiviazione BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio BLOB](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Creare un contenitore
Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. È possibile creare un contenitore privato utilizzando il `azure storage container create` comando:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Esistono tre livelli di accesso in lettura anonimo: **Off**, **BLOB** e **contenitore**. Per impedire l'accesso anonimo ai BLOB, impostare il parametro di autorizzazione su **Disattivato**. Per impostazione predefinita, il nuovo contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico anonimo alle risorse BLOB, ma non ai metadati del contenitore o all'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione su **BLOB**. Per consentire l'accesso in lettura pubblico completo alle risorse BLOB, ai metadati del contenitore e all'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione **su Contenitore**. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOB](storage-manage-access-to-resources.md).
>
>

### <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore
Nell'archivio BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Per altre informazioni, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Per caricare BLOB in un contenitore, è possibile utilizzare il `azure storage blob upload`. Per impostazione predefinita, questo comando carica i file locali in un BLOB in blocchi. Per specificare il tipo per il BLOB, è possibile usare il parametro `--blobtype` .

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Come scaricare i BLOB da un contenitore
Nell'esempio seguente viene mostrato come scaricare i BLOB da un contenitore.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Copiare i BLOB
È possibile copiare i BLOB tra aree e account di archiviazione in modo asincrono.

Nell'esempio seguente viene illustrato come copiare BLOB da un account di archiviazione a un altro. In questo esempio è possibile creare un contenitore in cui i BLOB sono pubblicamente, accessibile in forma anonima.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Nell'esempio viene eseguita una copia asincrona. È possibile monitorare lo stato di ogni operazione di copia eseguendo il `azure storage blob copy show` operazione.

Si noti che l'URL di origine fornito per l'operazione di copia devono essere accessibile pubblicamente o includere un token SAS (firma di accesso condiviso).

### <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un blob, utilizzare il comando seguente:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Creare e gestire condivisioni di file
L'archiviazione file di Azure offre un'archiviazione condivisa per le applicazioni che usano il protocollo SMB standard. Macchine virtuali di Microsoft Azure e servizi cloud, nonché applicazioni locali, possono condividere i dati di file tra condivisioni montate. È possibile gestire condivisioni di file e dati di file tramite la CLI di Azure. Per altre informazioni su Archiviazione file di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](storage-dotnet-how-to-use-files.md) o [Come usare Archiviazione file di Azure con Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Creare una condivisione file
Una condivisione di archiviazione file è una condivisione file SMB di Azure. Tutte le directory e i file devono essere creati in una condivisione padre. Un account può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione. Nell'esempio seguente viene creata una condivisione file denominata **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Creare una directory
Una directory fornisce una struttura gerarchica facoltativa per una condivisione di file di Azure. Nell'esempio seguente viene creata una directory denominata **myDir** nella condivisione di file.

```azurecli
azure storage directory create myshare myDir
```

Si noti che tale percorso di directory può includere più livelli, *ad esempio*, **un / b**. Tuttavia è necessario assicurarsi che tutte le directory padre esistano. Ad esempio, per il percorso **a/b**, è necessario creare prima la directory **a** e poi la directory **b**.

### <a name="upload-a-local-file-to-directory"></a>Caricare un file locale nella directory
Nell'esempio seguente viene caricato un file dalla directory **~/temp/samplefile.txt** to the **myDir**. Modificare il percorso del file in modo che punti a un file valido nel computer locale:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Si noti che un file nella condivisione può essere fino a 1 TB.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Elenco dei file nella directory principale della condivisione o directory
È possibile elencare i file e sottodirectory nella directory principale della condivisione o una directory utilizzando il comando seguente:

```azurecli
azure storage file list myshare myDir
```

Si noti che il nome della directory è facoltativo per l'operazione di elenco. Se omesso, il comando Elenca il contenuto della directory radice della condivisione.

### <a name="copy-files"></a>Copiare i file
A partire dalla versione 0.9.8.CLI di Azure, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Di seguito viene illustrato come eseguire queste operazioni di copia utilizzando i comandi CLI. Copiare un file nella nuova directory:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Copiare un BLOB in una directory del file:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare riferimenti ai comandi dell'interfaccia della riga di comando 1.0 di Azure da usare con le risorse di Archiviazione qui:

* [Comandi dell'interfaccia della riga di comando Azure in modalità Resource Manager](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Comandi dell'interfaccia della riga di comando di Azure in modalità Gestione servizi di Azure](../cli-install-nodejs.md)

È anche possibile provare l'[interfaccia della riga di comando di Azure 2.0](storage-azure-cli.md), ovvero l'interfaccia della riga di comando di nuova generazione scritta in Python per il modello di distribuzione di Resource Manager.

[Image1]: ./media/storage-azure-cli/azure_command.png

