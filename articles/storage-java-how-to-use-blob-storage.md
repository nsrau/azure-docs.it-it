<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Come utilizzare l'archiviazione BLOB da Java

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Microsoft Azure. Gli esempi sono scritti in Java e usano [Azure Storage SDK per Java][Azure Storage SDK per Java]. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi][Passaggi successivi].

Nota: per gli sviluppatori che usano il servizio di archiviazione di Azure in dispositivi Android, è disponibile un SDK specifico. Per altre informazioni, vedere [Azure Storage SDK per Android][Azure Storage SDK per Android].

## <a name="Contents"> </a>Sommario

-   [Informazioni sull'archiviazione BLOB][Informazioni sull'archiviazione BLOB]
-   [Concetti][Concetti]
-   [Creazione di un account di Archiviazione di Azure][Creazione di un account di Archiviazione di Azure]
-   [Creazione di un'applicazione Java][Creazione di un'applicazione Java]
-   [Configurazione dell'applicazione per l'accesso all'archiviazione BLOB][Configurazione dell'applicazione per l'accesso all'archiviazione BLOB]
-   [Configurazione di una stringa di connessione di archiviazione di Azure][Configurazione di una stringa di connessione di archiviazione di Azure]
-   [Procedura: Creare un contenitore][Procedura: Creare un contenitore]
-   [Procedura: Caricare un BLOB in un contenitore][Procedura: Caricare un BLOB in un contenitore]
-   [Procedura: Elencare i BLOB in un contenitore][Procedura: Elencare i BLOB in un contenitore]
-   [Procedura: Scaricare un BLOB][Procedura: Scaricare un BLOB]
-   [Procedura: Eliminare un BLOB][Procedura: Eliminare un BLOB]
-   [Procedura: Eliminare un contenitore BLOB][Procedura: Eliminare un contenitore BLOB]
-   [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Creazione di un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Creazione di un'applicazione Java

In questa guida si utilizzeranno le funzionalità di archiviazione che possono essere eseguite in un'applicazione Java in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in Azure.

A questo scopo, è necessario installare Java Development Kit (JDK) e creare un account di archiviazione di Azure nella sottoscrizione di Azure. Dopo avere eseguito questa operazione, sarà necessario verificare che il sistema di sviluppo in uso soddisfi i requisiti minimi e le dipendenze elencate nell'archivio [Azure Storage SDK for Java][Azure Storage SDK per Java] su GitHub. Se il sistema soddisfa i requisiti, è possibile seguire le istruzioni per scaricare e installare le librerie di archiviazione di Azure per Java nel sistema dall'archivio indicato. Dopo avere completato queste attività, sarà possibile creare un'applicazione Java che usa gli esempi illustrati in questo articolo.

## <a name="ConfigureStorage"> </a>Configurazione dell'applicazione per l'accesso all'archiviazione BLOB

Aggiungere le istruzioni import seguenti all'inizio del file Java in cui si desidera utilizzare le API di archiviazione di Azure per accedere ai BLOB:

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## <a name="ConnectionString"> </a>Configurazione di una stringa di connessione di archiviazione di Azure

I client di archiviazione di Azure utilizzano le stringhe di connessione di archiviazione per archiviare
endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente, utilizzando il nome dell'account di archiviazione e la chiave di accesso primaria relativa all'account di archiviazione riportata nel portale di gestione per i valori di *AccountName* e *AccountKey*. In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In un'applicazione in esecuzione in un ruolo di Microsoft Azure, questa stringa può essere archiviata nel file di configurazione del servizio *ServiceConfiguration.cscfg* ed è accessibile con una chiamata al metodo **RoleEnvironment.getConfigurationSettings**. Nell'esempio seguente viene recuperata la stringa di connessione da un elemento **Setting** denominato *StorageConnectionString* nel file di configurazione del servizio:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Gli esempi seguenti presumono che sia stato usato uno di questi due metodi per ottenere la stringa di connessione di archiviazione.

## <a name="CreateContainer"> </a>Procedura: Creare un contenitore

Gli oggetti CloudBlobClient consentono di ottenere oggetti di riferimento per contenitori e BLOB. Il codice seguente consente di creare un oggetto **CloudBlobClient**. Nota: esistono altri modi per creare oggetti **CloudStorageAccount**. Per altre informazioni, vedere **CloudStorageAccount** nel [Riferimento all'SDK del client di archiviazione di Azure][Riferimento all'SDK del client di archiviazione di Azure].

Tutti i BLOB risiedono in un contenitore. Utilizzare l'oggetto **CloudBlobClient** per ottenere un riferimento al contenitore da utilizzare. È possibile creare un contenitore, se non esiste, con il metodo **createIfNotExists**, che in caso contrario restituirà il contenitore esistente. Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Facoltativo: Configurare un contenitore per l'accesso pubblico

Per impostazione predefinita autorizzazioni di un contenitore sono configurate per l'accesso privato, ma è possibile configurare le autorizzazioni di un contenitore per consentire l'accesso pubblico di sola lettura per tutti gli utenti in Internet:

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## <a name="UploadBlob"> </a>Procedura: Caricare un BLOB in un contenitore

Per caricare un file in un BLOB, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando upload sul riferimento al BLOB. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nel codice seguente viene illustrato come effettuare questa operazione presupponendo che il contenitore sia già stato creato.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListBlobs"> </a>Procedura: Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore, come previsto per caricare un BLOB. È possibile usare il metodo **listBlobs** del contenitore con un ciclo **for**. Il codice seguente consente di inviare alla console l'URI di ogni BLOB in un contenitore.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più simile a quella delle cartelle.

È ad esempio possibile disporre di un contenitore denominato "photos", in cui sono stati caricati i BLOB denominati "rootphoto1", "2010/photo1", "2010/photo2" e "2011/photo1". Questa situazione implica in genere la creazione delle directory virtuali "2010" e "2011" nel contenitore "photos". Quando si chiama **listBlobs** sul contenitore "photos", la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlob** che rappresentano le directory e i BLOB contenuti al primo livello. In questo caso oltre alle directory "2010" e "2011", verrà restituita anche la foto "rootphoto1". È possibile utilizzare l'operatore **instanceof** per distinguere questi oggetti.

Facoltativamente, è possibile passare parametri al metodo **listBlobs** con
il parametro **useFlatBlobListing** impostato su True. In questo modo
verranno restituiti tutti i BLOB indipendentemente dalla directory. Per altre
informazioni, vedere **CloudBlobContainer.listBlobs** nel [Riferimento all'SDK del client di Archiviazione di Azure][Riferimento all'SDK del client di archiviazione di Azure].

## <a name="DownloadBlob"> </a>Procedura: Scaricare un BLOB

Per scaricare i BLOB, eseguire la stessa procedura illustrata per caricarli al fine di ottenere un riferimento al BLOB. Nell'esempio di caricamento è stato chiamato upload sull'oggetto BLOB. Nell'esempio seguente verrà chiamato download per trasferire il contenuto del BLOB in un oggetto stream, come **FileOutputStream**, che è possibile utilizzare per mantenere il BLOB in un file locale.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteBlob"> </a>Procedura: Eliminare un BLOB

Per eliminare un BLOB, ottenere il riferimento al BLOB e chiamare **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteContainer"> </a>Procedura: Eliminare un contenitore BLOB

Infine, per eliminare un contenitore BLOB, ottenere un riferimento al contenitore BLOB e
chiamare **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
            
       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   [Windows Azure SDK for Java][Azure Storage SDK per Java]
-   [Riferimento all'SDK del client di archiviazione di Azure][Riferimento all'SDK del client di archiviazione di Azure]
-   [API REST di Archiviazione di Azure][API REST di Archiviazione di Azure]
-   [Blog del team di Archiviazione di Azure][Blog del team di Archiviazione di Azure]

  [Azure Storage SDK per Java]: https://github.com/azure/azure-storage-java
  [Passaggi successivi]: #NextSteps
  [Azure Storage SDK per Android]: https://github.com/azure/azure-storage-android
  [Informazioni sull'archiviazione BLOB]: #what-is
  [Concetti]: #Concepts
  [Creazione di un account di Archiviazione di Azure]: #CreateAccount
  [Creazione di un'applicazione Java]: #CreateApplication
  [Configurazione dell'applicazione per l'accesso all'archiviazione BLOB]: #ConfigureStorage
  [Configurazione di una stringa di connessione di archiviazione di Azure]: #ConnectionString
  [Procedura: Creare un contenitore]: #CreateContainer
  [Procedura: Caricare un BLOB in un contenitore]: #UploadBlob
  [Procedura: Elencare i BLOB in un contenitore]: #ListBlobs
  [Procedura: Scaricare un BLOB]: #DownloadBlob
  [Procedura: Eliminare un BLOB]: #DeleteBlob
  [Procedura: Eliminare un contenitore BLOB]: #DeleteContainer
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Riferimento all'SDK del client di archiviazione di Azure]: http://dl.windowsazure.com/storage/javadoc/
  [API REST di Archiviazione di Azure]: http://msdn.microsoft.com/it-it/library/azure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
