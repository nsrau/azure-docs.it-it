<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Java) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Java" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use Blob Storage from Java" authors="" solutions="" manager="" editor="" />

Come utilizzare l'archiviazione BLOB da Java
============================================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Gli esempi sono scritti in Java e utilizzano [Azure SDK for Java](http://www.windowsazure.com/it-it/develop/java/). Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#NextSteps).

Sommario
--------

-   [Informazioni sull'archiviazione BLOB](#what-is)
-   [Concetti](#Concepts)
-   [Creazione di un account di archiviazione di Azure](#CreateAccount)
-   [Creazione di un'applicazione Java](#CreateApplication)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione BLOB](#ConfigureStorage)
-   [Configurazione di una stringa di connessione di archiviazione di Azure](#ConnectionString)
-   [Procedura: Creare un contenitore](#CreateContainer)
-   [Procedura: Caricare un BLOB in un contenitore](#UploadBlob)
-   [Procedura: Elencare i BLOB in un contenitore](#ListBlobs)
-   [Procedura: Scaricare un BLOB](#DownloadBlob)
-   [Procedura: Eliminare un BLOB](#DeleteBlob)
-   [Procedura: Eliminare un contenitore BLOB](#DeleteContainer)
-   [Passaggi successivi](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creazione di un account di archiviazione di Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creazione di un'applicazione Java
---------------------------------

In questa guida si utilizzeranno le funzionalità di archiviazione che possono essere eseguite in un'applicazione Java in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in Azure. Si presuppone che sia stato scaricato e installato Java Development Kit (JDK), che siano stati installati Azure SDK e le librerie di Azure per Java in base alle istruzioni riportate in [Download di Azure SDK for Java](http://www.windowsazure.com/it-it/develop/java/) e che sia stato creato un account di archiviazione di Azure nella sottoscrizione di Azure.

Per creare l'applicazione, è possibile utilizzare qualsiasi strumento di sviluppo, incluso il Blocco note. È sufficiente essere in grado di compilare un progetto Java e di fare riferimento alle librerie di Azure per Java.

Configurazione dell'applicazione per l'accesso all'archiviazione BLOB
---------------------------------------------------------------------

Aggiungere le istruzioni import seguenti all'inizio del file Java in cui si desidera utilizzare le API di archiviazione di Azure per accedere ai BLOB:

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

Configurazione di una stringa di connessione di archiviazione di Azure
----------------------------------------------------------------------

I client di archiviazione di Azure utilizzano le stringhe di connessione di archiviazione per archiviare endpoint e credenziali per l'accesso ai servizi di gestione dati. Quando si esegue un'applicazione client, è necessario specificare la stringa di connessione di archiviazione nel formato seguente, utilizzando il nome dell'account di archiviazione e la chiave di accesso primaria relativa all'account di archiviazione riportata nel portale di gestione per i valori di *AccountName* e *AccountKey*. In questo esempio viene illustrato come dichiarare un campo statico per memorizzare la stringa di connessione:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

In un'applicazione in esecuzione in un ruolo di Azure, questa stringa può essere archiviata nel file di configurazione del servizio ServiceConfiguration.cscfg ed è accessibile con una chiamata al metodo **RoleEnvironment.getConfigurationSettings**. Nell'esempio seguente viene recuperata la stringa di connessione da un elemento **Setting** denominato *StorageConnectionString* nel file di configurazione del servizio:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Procedura: Creare un contenitore
--------------------------------

Gli oggetti CloudBlobClient consentono di ottenere oggetti di riferimento per contenitori e BLOB. Il codice seguente consente di creare un oggetto **CloudBlobClient**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

Tutti i BLOB risiedono in un contenitore. Utilizzare l'oggetto **CloudBlobClient** per ottenere un riferimento al contenitore da utilizzare. È possibile creare un contenitore, se non esiste, con il metodo **createIfNotExist**, che in caso contrario restituirà il contenitore esistente. Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore.

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

Se si desidera rendere pubblici i file, è possibile impostare le autorizzazioni del contenitore.

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

Chiunque acceda a Internet può visualizzare i BLOB di un contenitore pubblico, ma l'accesso pubblico è limitato alla sola lettura.

Procedura: Caricare un BLOB in un contenitore
---------------------------------------------

Per caricare un file in un BLOB, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando upload sul riferimento al BLOB. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nel codice seguente viene illustrato come effettuare questa operazione presupponendo che il contenitore sia già stato creato.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\myimages\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

Procedura: Elencare i BLOB in un contenitore
--------------------------------------------

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore, come previsto per caricare un BLOB. È possibile utilizzare il metodo **listBlobs** del contenitore con un ciclo for. Il codice seguente consente di inviare alla console l'URI di ogni BLOB in un contenitore.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
        }

Il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più simile a quella delle cartelle.

È ad esempio possibile disporre di un contenitore denominato "photos", in cui sono stati caricati i BLOB denominati "rootphoto1", "2010/photo1", "2010/photo2" e "2011/photo1". Questa situazione implica in genere la creazione delle directory virtuali "2010" e "2011" nel contenitore "photos". Quando si chiama **listBlobs** sul contenitore "photos", la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlob** che rappresentano le directory e i BLOB contenuti al primo livello. In questo caso oltre alle directory "2010" e "2011", verrà restituita anche la foto "rootphoto1". È possibile utilizzare l'operatore **instanceof** per distinguere questi oggetti.

Facoltativamente, è possibile passare parametri al metodo **listBlobs** con il parametro **useFlatBlobListing** impostato su True. In questo modo verranno restituiti tutti i BLOB indipendentemente dalla directory. Per ulteriori informazioni, vedere CloudBlobContainer.listBlobs nella documentazione relativa a Java.

Procedura: Scaricare un BLOB
----------------------------

Per scaricare i BLOB, eseguire la stessa procedura illustrata per caricarli al fine di ottenere un riferimento al BLOB. Nell'esempio di caricamento è stato chiamato upload sull'oggetto BLOB. Nell'esempio seguente verrà chiamato download per trasferire il contenuto del BLOB in un oggetto stream, come **FileOutputStream**, che è possibile utilizzare per mantenere il BLOB in un file locale.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

Procedura: Eliminare un BLOB
----------------------------

Per eliminare un BLOB, ottenere il riferimento al BLOB e chiamare **delete**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

Procedura: Eliminare un contenitore BLOB
----------------------------------------

Infine, per eliminare un contenitore BLOB, ottenere un riferimento al contenitore BLOB e chiamare delete.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure]
-   Blog del team di Archiviazione di Azure: &lt;http://blogs.msdn.com/b/windowsazurestorage/\>

