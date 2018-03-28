---
title: 'Guida introduttiva di Azure: Caricare, scaricare ed elencare BLOB in Archiviazione di Azure con Java | Microsoft Docs'
description: In questa guida introduttiva viene creato un account di archiviazione e un contenitore. Si usa quindi la libreria client di archiviazione per Java per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: rogarana
ms.openlocfilehash: 44e5f921ca9303e680c76d439ab7503ea3c39680
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-java"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Java

Questa guida rapida mostra come usare Java per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure.

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida introduttiva:

* Installare un IDE con integrazione Maven

* Oppure installare e configurare Maven per usarlo dalla riga di comando

Questa esercitazione usa [Eclipse](http://www.eclipse.org/downloads/) con la configurazione "IDE Eclipse per sviluppatori Java".

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-java-quickstart) utilizzata in questa guida rapida è un'applicazione console di base. 

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire il progetto, avviare Eclipse e chiudere la schermata iniziale. Selezionare **File** quindi **Open Projects from File System...** (Apri progetti dal File System...). Assicurarsi che l'opzione **Detect and configure project natures** (Rileva e configura nature progetto) sia selezionata. Selezionare **Directory** quindi spostarsi dove è stato archiviato il repository clonato e, al suo interno, selezionare la cartella **javaBlobsQuickstart**. Verificare che il progetto **javaBlobsQuickstarts** sia visualizzato come progetto di Eclipse, quindi selezionare **Fine**.

Dopo che l'importazione del progetto è terminata, aprire **AzureApp.java** (che si trova in **blobQuickstart.blobAzureApp** all'interno di **main/src/java**) e sostituire `accountname` ed `accountkey` all'interno della stringa `storageConnectionString`. Quindi eseguire l'applicazione.
     

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
    
Nell'applicazione è necessario inserire la stringa di connessione per l'account di archiviazione. Aprire il file **AzureApp.Java**. Creare la variabile `storageConnectionString`. Sostituire i valori `AccountName` e `AccountKey` nella stringa di connessione con i valori salvati dal portale di Azure. `storageConnectionString` dovrebbe essere simile al seguente:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio crea un file di test nella directory predefinita (Documenti per gli utenti di Windows), lo carica nell'archiviazione BLOB, elenca i BLOB nel contenitore, quindi scarica il file con un nuovo nome, così da poter confrontare i file nuovi e quelli vecchi. 

Eseguire l'esempio premendo **CTRL+F11** in Eclipse.

Se si desidera eseguire l'esempio usando Maven dalla riga di comando, aprire una shell e passare a **blobAzureApp** all'interno della directory clonata. Immettere quindi `mvn compile exec:java`.

Di seguito è riportato un esempio dell'output che si otterrebbe eseguendo l'applicazione in Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

 Prima di continuare controllare nella directory predefinita (Documenti per gli utenti di Windows) se sono presenti i due file. È possibile aprirli e vedere che sono identici. Copiare l'URL per il BLOB nella finestra della console e incollarlo in un browser per visualizzare il contenuto del file nell'archiviazione BLOB. Quando si preme il tasto INVIO, si elimina il contenitore di archiviazione e i file.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere il tasto INVIO per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file **AzureApp.java** per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage._storage_account) che punti all'account di archiviazione.

    L'oggetto **CloudStorageAccount** è una rappresentazione dell'account di archiviazione e consente di impostare le proprietà di tale account e di accedervi a livello di codice. Con l'oggetto **CloudStorageAccount** è possibile creare un'istanza di **CloudBlobClient**, necessaria per accedere al servizio BLOB.

* Creare un'istanza dell'oggetto **CloudBlobClient** che punti al [servizio BLOB](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) nell'account di archiviazione.

    **CloudBlobClient** costituisce un punto di accesso al servizio BLOB che consente di impostare le proprietà di archiviazione BLOB e di accedervi a livello di codice. Con l'oggetto **CloudBlobClient** è possibile creare un'istanza dell'oggetto **CloudBlobContainer**, necessaria per creare contenitori.

* Creare un'istanza dell'oggetto [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) che rappresenti il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.    

    Dopo aver creato **CloudBlobContainer**, è possibile creare un'istanza dell'oggetto [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) che punti al BLOB specifico a cui si è interessati ed esegua operazioni di caricamento, download, copia e così via.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

### <a name="create-a-container"></a>Creare un contenitore 

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici e accessibili solo con un URL. Il contenitore è denominato **quickstartblobs**. 

Questo esempio usa [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists) perché si desidera creare un nuovo contenitore ogni volta che si esegue l'esempio. In un ambiente di produzione in cui si usa lo stesso contenitore per tutta l'applicazione, è consigliabile chiamare **CreateIfNotExists** solo una volta. In alternativa, è possibile creare il contenitore in anticipo in modo che non sia necessario crearlo nel codice.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequentemente e vengono usati in questa guida rapida. 

Per caricare un file in un BLOB, ottenere un riferimento al BLOB nel contenitore di destinazione. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Questa operazione consente di creare il BLOB se non esiste o di sovrascriverlo se esiste già.

Il codice di esempio crea un file locale da usare per il caricamento e il download, archiviando il file da caricare come **source** e il nome del BLOB in **blob**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Sono disponibili diversi metodi per il caricamento, tra cui [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) e [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext) che è possibile usare con l'archiviazione BLOB. Nel caso di una stringa, ad esempio, è possibile usare il metodo UploadText anziché il metodo Upload. 

I BLOB in blocchi possono essere qualsiasi tipo di file di testo o binario. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere l'elenco dei file nel contenitore usando [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando gli URI dei BLOB trovati. Per visualizzare il file è possibile copiare l'URI dalla finestra di comando e incollarlo in un browser.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB nel disco locale mediante [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente, aggiungendo un suffisso "_DOWNLOADED" al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Pulire le risorse

Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Questa procedura elimina anche i file nel contenitore.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="resources-for-developing-java-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Java con BLOB

Per lo sviluppo Java con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare e scaricare il [codice sorgente della libreria client Java](https://github.com/Azure/azure-storage-java) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client Java, vedere le [informazioni di riferimento sulle API Java](https://docs.microsoft.com/en-us/java/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=java&term=blob) scritti con la libreria client Java.

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e Archiviazione BLOB di Azure con Java. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-java-how-to-use-blob-storage.md)

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md).

Per altri esempi Java, vedere [Esempi di Archiviazione di Azure che usano Java](../common/storage-samples-java.md).