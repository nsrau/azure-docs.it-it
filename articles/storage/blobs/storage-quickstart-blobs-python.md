---
title: 'Guida introduttiva di Azure: Caricare, scaricare ed elencare BLOB in Archiviazione di Azure con Python | Microsoft Docs'
description: In questa guida introduttiva viene creato un account di archiviazione e un contenitore. Si usa quindi la libreria client di archiviazione per Python per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: d4c489bc41a9c04b15e2ee28b944edc1f15ef81c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Python

In questa guida rapida si apprende a usare Python per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. 

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida introduttiva: 
* Installare [Python](https://www.python.org/downloads/)
* Scaricare e installare [Azure Storage SDK per Python](storage-python-how-to-use-blob-storage.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio
L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) usata in questa guida rapida è un'applicazione Python di base.  

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire un programma in Python, cercare la cartella storage-blobs-python-quickstart e il file example.py.  

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Nell'applicazione è necessario inserire il nome dell'account di archiviazione e la chiave dell'account per creare un oggetto `BlockBlobService`. Aprire il file `example.py` da Esplora soluzioni nell'IDE. Sostituire i valori **accountname** e **accountkey** con il nome e la chiave dell'account. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Eseguire l'esempio
Questo esempio consente di creare un file di test nella cartella "Documenti". Il programma di esempio consente di caricare il file di test nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome. 

Eseguire l'esempio. Il seguente output è un esempio dell'output ricevuto durante l'esecuzione dell'applicazione:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Quando si preme un tasto qualsiasi per continuare, il programma di esempio elimina il contenitore di archiviazione e i file. Prima di continuare, controllare che nella cartella "Documenti" siano presenti due file. È possibile aprirli e vedere che sono identici.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file example.py per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione
La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlockBlobService** che punti al servizio BLOB nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato CloudBlobContainer, è possibile creare un'istanza dell'oggetto **CloudBlockBlob** che punti al BLOB specifico a cui si è interessati ed esegua operazioni di caricamento, download e copia.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici. Il contenitore è denominato **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequente e vengono usati in questa guida rapida.  

Per caricare un file in un BLOB, ottenere il percorso completo del file unendo il nome della directory e il nome del file nell'unità locale. È quindi possibile caricare il file nel percorso desiderato usando il metodo **create\_blob\_from\_path**. 

Il codice di esempio crea un file locale da usare per il caricamento e il download, per archiviare il file da caricare come **file\_path\_to\_file** e il nome del BLOB nel **local\_file\_name**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Ad esempio, se si dispone di un flusso di memoria, è possibile usare il metodo **create\_blob\_from\_stream** anziché**create\_blob\_from\_path**. 

I BLOB in blocchi possono avere dimensioni pari a 4,7 TB e possono essere qualsiasi tipo di file, da fogli di calcolo di Excel ai file video di grandi dimensioni. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di file nel contenitore usando il metodo **list_blobs**. Questo metodo restituisce un generatore. Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando i nomi dei BLOB trovati in un contenitore.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Scaricare i BLOB

Scaricare i BLOB nel disco locale usando il metodo **get\_blob\_to\_path**. Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente. Il suffisso "_DOWNLOADED" viene aggiunto al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo **delete\_container**. È possibile eliminare anche i file creati se non sono più necessari usando il metodo **delete\_blob**.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Python con BLOB

Per lo sviluppo Python con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare, scaricare e installare il [codice sorgente della libreria client Python](https://github.com/Azure/azure-storage-python) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Vedere le [informazioni di riferimento sulle API per Python](https://docs.microsoft.com/python/api/overview/azure/storage) per altre informazioni sulla libreria client Python.
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) scritti con la libreria client per Python.

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e un'Archiviazione BLOB di Azure con Python. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](./storage-python-how-to-use-blob-storage.md)
 
Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
