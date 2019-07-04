---
title: Guida introduttiva di Azure - Creare un BLOB nell'archivio oggetti con Python | Microsoft Docs
description: In questa guida introduttiva si creano un account di archiviazione e un contenitore nell'archivio oggetti (BLOB). Si usa quindi la libreria client di archiviazione per Python per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: 0ae47a7898e380a25618a8d6ae6a1e0251fe466c
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514581"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Python

In questa guida rapida viene illustrato come usare Python per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. I BLOB sono semplicemente oggetti che possono contenere qualsiasi quantità di dati di testo o binari (ad esempio immagini, documenti, flussi multimediali, dati di archivio e così via) e in Archiviazione di Azure sono diversi dalle condivisioni file, dalle tabelle senza schema e dalle code di messaggi. Per altre informazioni, vedere [Introduzione ad Archiviazione di Azure](/azure/storage/common/storage-introduction).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verificare che siano installati i prerequisiti aggiuntivi seguenti:

* [Python](https://www.python.org/downloads/)
* [SDK di Archiviazione di Azure per Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio
L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) in questa guida rapida è un'applicazione Python di base.  

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Questo comando clona il repository *Azure-Samples/storage-blobs-python-quickstart* nella cartella git locale. Per eseguire il programma Python, aprire il file *example.py* nella radice del repository.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Nell'applicazione, inserire il nome dell'account di archiviazione e la chiave dell'account per creare un oggetto `BlockBlobService`. Aprire il file *example.py* da Esplora soluzioni nell'IDE. Sostituire i valori `accountname` e `accountkey` con il nome e la chiave dell'account. 

```python 
block_blob_service = BlockBlobService(account_name = 'accountname', account_key = 'accountkey') 
```

## <a name="run-the-sample"></a>Eseguire l'esempio
Questo esempio crea un file di test nella cartella *Documenti*. Il programma di esempio consente di caricare il file di test nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome. 

Per prima cosa, installare le dipendenze eseguendo `pip install`:

```python
    pip install azure-storage-blob
```

In seguito, eseguire l'esempio. Verranno visualizzati messaggi simili all'output seguente:
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Prima di continuare, controllare che nella cartella *Documenti* siano presenti i due file. È possibile aprirli e vedere che sono identici.

È anche possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file *example.py* per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene ora descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione
Prima di tutto, creare i riferimenti agli oggetti usati per accedere all'archivio BLOB e gestirlo. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlockBlobService** che punti al servizio BLOB nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Una volta creato il contenitore cloud di BLOB, creare un'istanza dell'oggetto **CloudBlockBlob** che punta allo specifico BLOB a cui è interessati. È possibile quindi caricare, scaricare e copiare il BLOB in base alle proprie esigenze.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici. Il contenitore è denominato **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(account_name = 'accountname', account_key = 'accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequentemente e vengono usati in questa guida rapida.  

Per caricare un file in un BLOB, ottenere il percorso completo del file unendo il nome della directory con il nome del file nell'unità locale. È quindi possibile caricare il file nel percorso specificato con il metodo `create_blob_from_path`. 

Il codice di esempio crea un file locale da usare per il caricamento e il download, archiviando il file da caricare come *full_path_to_file* e il nome del BLOB come *local_file_name*. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Nel caso di un flusso di memoria, ad esempio, è possibile usare il metodo `create_blob_from_stream` anziché il metodo `create_blob_from_path`. 

I BLOB in blocchi possono avere dimensioni pari a 4,7 TB e possono essere qualsiasi tipo di file, da fogli di calcolo di Excel ai file video di grandi dimensioni. I BLOB di pagine vengono usati principalmente per i file VHD su cui si basano le macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di file nel contenitore usando il metodo `list_blobs`. Questo metodo restituisce un generatore. Il codice seguente recupera l'elenco di BLOB&mdash;quindi esegue il ciclo per tutti loro&mdash;mostrando i nomi dei BLOB trovati in un contenitore.  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Scaricare i BLOB

Scaricare i BLOB nel disco locale usando il metodo `get_blob_to_path`. Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente. Il suffisso *_DOWNLOADED* viene aggiunto al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name, '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo `delete_container`. Per eliminare singoli file, usare invece il metodo `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Python con BLOB

Per altre informazioni sullo sviluppo Python con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare, scaricare e installare il [codice sorgente della libreria client Python](https://github.com/Azure/azure-storage-python) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client Python, vedere le [informazioni di riferimento sulle API per Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) scritti con la libreria client per Python.

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida introduttiva si è appreso come trasferire file tra il disco locale e un archivio BLOB di Azure con Python. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](./storage-python-how-to-use-blob-storage.md)
 
Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
