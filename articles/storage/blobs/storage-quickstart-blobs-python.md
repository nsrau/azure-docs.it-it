---
title: Guida rapida di Azure - Trasferire oggetti da e verso Archiviazione BLOB di Azure con Python | Microsoft Docs
description: Apprendere rapidamente a trasferire oggetti da e verso Archiviazione BLOB di Azure con Python
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 44ec416a814ff6a5fef79ef21e2f54ce4ce4da17
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Trasferire oggetti da e verso Archiviazione BLOB di Azure con Python
In questa guida rapida si apprende a usare Python per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva: 
* Installare [Python](https://www.python.org/downloads/)
* Scaricare e installare [Azure Storage SDK per Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Creare un account di archiviazione usando il portale di Azure

Innanzitutto, creare un nuovo account di archiviazione generico da usare per questa guida rapida. 

1. Aprire e accedere al [portale di Azure](https://portal.azure.com) con il proprio account di Azure. 
2. Nel menu Hub selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione: BLOB, File, Tabelle, Code**. 
3. Immettere un nome per l'account di archiviazione. Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri e può contenere solo numeri e lettere minuscole. Deve anche essere univoco.
4. Impostare `Deployment model` su **Gestione risorse**.
5. Impostare `Account kind` su **Utilizzo generico**.
6. Impostare `Performance` su **Standard**. 
7. Impostare `Replication` su **Archiviazione con ridondanza locale**.
8. Impostare `Storage service encryption` su **Disabilitato**.
9. Impostare `Secure transfer required` su **Disabilitato**.
10. Selezionare la propria sottoscrizione. 
11. Per `resource group`, crearne una nuova e assegnarle un nome univoco. 
12. Selezionare `Location` per l'account di archiviazione.
13. Selezionare **Aggiungi al dashboard** e fare clic su **Crea** per creare l'account di archiviazione. 

Dopo aver creato l'account di archiviazione, questa viene aggiunta al dashboard. Fare clic su di essa per aprirla. In **IMPOSTAZIONI** fare clic su **Chiavi di accesso**. Selezionare una chiave e copiare il nome dell'account di archiviazione negli Appunti, quindi incollarlo nel Blocco note per usarlo in seguito.

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

## <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione
La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlockBlobService** che punti al servizio BLOB nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato CloudBlobContainer, è possibile creare un'istanza dell'oggetto **CloudBlockBlob** che punti al BLOB specifico a cui si è interessati ed esegua operazioni di caricamento, download e copia.

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
## <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

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

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di file nel contenitore usando il metodo **list_blobs**. Questo metodo restituisce un generatore. Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando i nomi dei BLOB trovati in un contenitore.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>Scaricare i BLOB

Scaricare i BLOB nel disco locale usando il metodo **get\_blob\_to\_path**. Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente. Il suffisso "_DOWNLOADED" viene aggiunto al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo **delete\_container**. È possibile eliminare anche i file creati se non sono più necessari usando il metodo **delete\_blob**.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e un'Archiviazione BLOB di Azure con Python. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](./storage-python-how-to-use-blob-storage.md)
 

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
