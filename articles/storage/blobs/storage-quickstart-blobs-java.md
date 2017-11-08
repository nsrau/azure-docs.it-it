---
title: Guida rapida di Azure - Trasferire oggetti da e verso Archiviazione BLOB di Azure con Java | Microsoft Docs
description: Imparare velocemente come trasferire oggetti da e verso Archiviazione BLOB di Azure con Java
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 0ed27077f649df2d0673351cda708b9a358ff92b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Trasferire oggetti da e verso Archiviazione BLOB di Azure con Java

Questa guida rapida mostra come usare Java per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* Installare un IDE con l'integrazione di Maven o installare e configurare Maven per il funzionamento da riga di comando
    * Questa esercitazione usa [Eclipse](http://www.eclipse.org/downloads/) con la configurazione "IDE Eclipse per sviluppatori Java"
    


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

Dopo aver creato l'account di archiviazione, questa viene aggiunta al dashboard. Fare clic su di essa per aprirla. In IMPOSTAZIONI fare clic su **Chiavi di accesso**. Selezionare una chiave e copiarla negli Appunti, quindi incollarla in un editor di testo per un usarla in seguito. Copiare anche il nome dell'account di archiviazione e incollarlo in un editor di testo per usarlo in seguito.

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
    public static final String storageConnectionString ="DefaultEndpointsProtocol=https;" +
     "AccountName=<Namehere>;" +
    "AccountKey=<Keyhere>";
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio crea un file di test nella directory predefinita (Documenti per gli utenti di Windows), lo carica nell'archiviazione BLOB, elenca i BLOB nel contenitore, quindi scarica il file con un nuovo nome, così da poter confrontare i file nuovi e quelli vecchi. 

Eseguire l'esempio premendo **CTRL+F11** in Eclipse.

Se si desidera eseguire l'esempio usando Maven dalla riga di comando, aprire una shell e passare a **blobAzureApp** all'interno della directory clonata. Immettere quindi `mvn compile exec:java`.

Di seguito è riportato un esempio dell'output che si otterrebbe eseguendo l'applicazione in Windows.

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 Prima di continuare controllare nella directory predefinita (Documenti per gli utenti di Windows) se sono presenti i due file. È possibile aprirli e vedere che sono identici. Copiare l'URL per il BLOB nella finestra della console e incollarlo in un browser per visualizzare il contenuto del file nell'archiviazione BLOB. Quando si preme il tasto INVIO, si elimina il contenitore di archiviazione e i file.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere il tasto INVIO per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file **AzureApp.java** per esaminare il codice. 

## <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **CloudStorageAccount** che punti all'[account di archiviazione](/java/api/com.microsoft.azure.management.storage._storage_account). 

* Creare un'istanza dell'oggetto **CloudBlobClient** che punti al [servizio BLOB](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) nell'account di archiviazione. 

* Creare un'istanza dell'oggetto **CloudBlobContainer** che rappresenti il [contenitore](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato **CloudBlobContainer**, è possibile creare un'istanza dell'oggetto **CloudBlockBlob** che punti al [BLOB](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) specifico a cui si è interessati ed esegua operazioni di caricamento, download, copia e così via.

In questa sezione si creano istanze degli oggetti, si crea un nuovo contenitore e quindi si impostano le autorizzazioni nel contenitore in modo che i BLOB siano pubblici e accessibili solo con un URL. Il contenitore è denominato **quickstartblobs**. 

Questo esempio usa **CreateIfNotExists** perché si desidera creare un nuovo contenitore ogni volta che si esegue l'esempio. In un ambiente di produzione in cui si usa lo stesso contenitore per tutta l'applicazione, è consigliabile chiamare **CreateIfNotExists** solo una volta. In alternativa, è possibile creare il contenitore in anticipo in modo che non sia necessario crearlo nel codice.

```java
CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
        
CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

// Get a reference to a container.
// The container name must be lower case
CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

// Create the container if it does not exist.
container.createIfNotExists();

// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequentemente e vengono usati in questa guida rapida. 

Per caricare un file in un BLOB, ottenere un riferimento al BLOB nel contenitore di destinazione. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Questa operazione consente di creare il BLOB se non esiste o di sovrascriverlo se esiste già.

Il codice di esempio crea un file locale da usare per il caricamento e il download, archiviando il file da caricare come **origine** e il nome del BLOB in **BLOB**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```java
//Getting the path to user's myDocuments folder
String myDocs = FileSystemView.getFileSystemView().getDefaultDirectory().getPath();

//Creating a file in the myDocuments folder
File source = new File(myDocs + File.separator + "results.txt");
try( Writer output = new BufferedWriter(new FileWriter(source)))
{
    System.out.println("Location of file: " + source.toString());

    output.write("Hello Azure!");
    output.close();
    
    System.out.println("File has been written");
}
catch(IOException x) 
{
    System.err.println(x);
}

//Getting blob reference
CloudBlockBlob blob = container.getBlockBlobReference("results.txt");

//Creating a FileInputStream as it is necessary for the upload
FileInputStream myFile = new FileInputStream(source);

//Creating blob and uploading file to it
blob.upload( myFile, source.length());

//Closing FileInputStream
myFile.close();
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Ad esempio, se si dispone di un flusso di memoria, è possibile usare il metodo UploadFromStreamAsync anziché UploadFromFileAsync. 

I BLOB in blocchi possono essere qualsiasi tipo di file di testo o binario. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si desidera scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere l'elenco dei file nel contenitore usando [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando gli URI dei BLOB trovati. Per visualizzare il file è possibile copiare l'URI dalla finestra di comando e incollarlo in un browser.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

## <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB nel disco locale mediante [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente, aggiungendo un suffisso "_DOWNLOADED" al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in your default directory.
blob.downloadToFile(myDocs + File.separator + "results_DOWNLOADED.txt");
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se i BLOB caricati in questa guida rapida non sono più necessari, è possibile eliminare l'intero contenitore usando [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Questa procedura elimina anche i file nel contenitore.

```java
//Deletes container if it exists then deletes files created locally
container.deleteIfExists();
downloadedFile.deleteOnExit();
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida rapida è stato descritto il trasferimento di file tra il disco locale e Archiviazione BLOB di Azure con Java. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-java-how-to-use-blob-storage.md)

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md).