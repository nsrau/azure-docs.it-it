<properties linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service (2.0)" pageTitle="How to use blob storage in .NET | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob .NET, Azure blob C#, Azure blob C#" description="Learn how to use the Azure blob service to upload,,download, list, and delete blob content. Samples are written in C#." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Azure Blob Storage Service in .NET" authors="" solutions="" manager="paulettm" editor="cgronlun" />

Come utilizzare il servizio di archiviazione BLOB di Azure in .NET
==================================================================

[versione 1.7](/en-us/develop/net/how-to-guides/blob-storage-v17/ "versione 1.7") [versione 2.0](/en-us/develop/net/how-to-guides/blob-storage/ "versione 2.0")

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Negli esempi, scritti in C\#, viene utilizzata l'API .NET. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
--------

-   [Informazioni sull'archiviazione BLOB](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-account)
-   [Configurazione di una stringa di connessione di archiviazione](#setup-connection-string)
-   [Procedura: Accedere all'archiviazione BLOB a livello di codice](#configure-access)
-   [Procedura: Creare un contenitore](#create-container)
-   [Procedura: Caricare un BLOB in un contenitore](#upload-blob)
-   [Procedura: Elencare i BLOB in un contenitore](#list-blob)
-   [Procedura: Scaricare BLOB](#download-blobs)
-   [Procedura: Eliminare BLOB](#delete-blobs)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creazione di un accountCreazione di un account di archiviazione di Azure
------------------------------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Configurazione di una stringa di connessioneConfigurazione di una stringa di connessione di archiviazione
---------------------------------------------------------------------------------------------------------

Nell'API di archiviazione di Azure per .NET è possibile utilizzare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. È possibile inserire la stringa di connessione di archiviazione in un file di configurazione, anziché impostarla come hardcoded nel codice:

-   Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione dei servizi di Azure (file `*.csdef` e `*.cscfg`).
-   Quando si utilizza Siti Web o Macchine virtuali di Azure, è consigliabile archiviare la stringa di connessione con il sistema di configurazione .NET, ovvero il file `web.config`.

In entrambi i casi è possibile recuperare la stringa di connessione utilizzando il metodo `CloudConfigurationManager.GetSetting`, come illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella cartella **Ruoli** del progetto di distribuzione di Azure, quindi scegliere **Proprietà**.
     ![Selezione delle proprietà per un ruolo servizio cloud in Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob5.png)

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante **Aggiungi impostazione**.
     ![Aggiunta di un'impostazione del servizio cloud in Visual Studio](./media/storage-dotnet-how-to-use-blobs-17/blob6.png)

    Nella griglia delle impostazioni verrà visualizzata una nuova voce **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1** selezionare **Stringa di connessione**.
     ![BLOB7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Fare clic sul pulsante **...** a destra della voce **Impostazione1**. Verrà visualizzata la finestra di dialogo **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore di archiviazione (servizio di archiviazione di Azure simulato nel computer locale) oppure un effettivo account di archiviazione nel cloud. Il codice riportato in questa guida funziona con entrambe le opzioni. Se si desidera archiviare i dati BLOB nell'account di archiviazione creato in precedenza in Azure, immettere il valore di **Chiave di accesso primaria** copiato dal passaggio precedente di questa esercitazione.
     ![BLOB8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Modificare il valore **Impostazione1** della voce **Nome** specificando un nome più descrittivo, ad esempio **StorageConnectionString**. A questa stringa di connessione verrà fatto riferimento più avanti nel codice in questa guida.
     ![BLOB9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Configurazione della stringa di connessione quando si utilizza Siti Web o Macchine virtuali

Con Siti Web o Macchine virtuali è consigliabile utilizzare il sistema di configurazione .NET, ovvero il file `web.config`. Per archiviare la stringa di connessione, utilizzare l'elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

Per ulteriori informazioni sulle stringhe di connessione di archiviazione, leggere [Configurazione delle stringhe di connessione](http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx).

È ora possibile eseguire le procedure di questa guida.

Accedere a livello di codiceProcedura: Accedere all'archiviazione BLOB a livello di codice
------------------------------------------------------------------------------------------

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

È possibile utilizzare i tipi **CloudStorageAccount** e **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Un tipo **CloudBlobClient** consente di recuperare oggetti che rappresentano contenitori e BLOB archiviati nel servizio di archiviazione BLOB. Il codice seguente consente di creare un oggetto **CloudBlobClient** utilizzando l'oggetto account di archiviazione recuperato in precedenza:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Creare un contenitoreProcedura: Creare un contenitore
-----------------------------------------------------

Tutti i BLOB di archiviazione risiedono in un contenitore. Per ottenere un riferimento al contenitore che si desidera utilizzare, è possibile utilizzare un oggetto **CloudBlobClient**. È possibile creare il contenitore se non esistente:

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist
    container.CreateIfNotExist();

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico utilizzando il codice seguente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob });

I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di Internet, tuttavia è possibile modificarli o eliminarli solo se si dispone della chiave di accesso appropriata.

Caricare in un contenitoreProcedura: Caricare un BLOB in un contenitore
-----------------------------------------------------------------------

Per caricare un file in un BLOB, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream** sul riferimento al BLOB. Questa operazione consentirà di creare il BLOB se non esistente o di sovrascriverlo se esistente. Nel codice seguente viene illustrato come effettuare questa operazione presupponendo che il contenitore sia già stato creato.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
        {
            blob.UploadFromStream(fileStream);
        } 

Elencare i BLOB in un contenitoreProcedura: Elencare i BLOB in un contenitore
-----------------------------------------------------------------------------

Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **ListBlobs** del contenitore per recuperare i BLOB in esso contenuti. Nel codice seguente viene illustrato come recuperare e visualizzare l'**URI** di ogni BLOB in un contenitore:

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    foreach (var blobItem in container.ListBlobs())
        {
            Console.WriteLine(blobItem.Uri);
        } 

Il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più simile a quella delle cartelle. È ad esempio possibile disporre del contenitore denominato 'photos', in cui sono stati caricati i BLOB 'rootphoto1', '2010/photo1', '2010/photo2' e '2011/photo1'. Questa situazione implica in genere la creazione delle directory '2010' e '2011' nel contenitore 'photos'. Quando si chiama **ListBlobs** sul contenitore 'photos', la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlob** che rappresentano le directory e i BLOB contenuti al primo livello. In questo caso oltre alle directory '2010' e '2011', verrà restituita anche la foto 'rootphoto1'. Facoltativamente, è possibile passare una nuova classe **BlobRequestOptions** con **UseFlatBlobListing** impostato su **true**. In questo modo verranno restituiti tutti i BLOB indipendentemente dalla directory. Per ulteriori informazioni, vedere [CloudBlobContainer.ListBlobs][].

Scaricare BLOBProcedura: Scaricare BLOB
---------------------------------------

Per scaricare BLOB, recuperare prima un riferimento al BLOB. Nell'esempio seguente il metodo **DownloadToStream** viene utilizzato per trasferire il contenuto del BLOB a un oggetto stream che è quindi possibile rendere persistente in un file locale. È anche possibile chiamare il metodo **DownloadToFile**, **DownloadByteArray** o **DownloadText** del BLOB.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Save blob contents to disk
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
        {
            blob.DownloadToStream(fileStream);
        } 

Eliminare BLOBProcedura: Eliminare BLOB
---------------------------------------

Per eliminare un BLOB, infine, ottenere un riferimento al BLOB su cui chiamare il metodo **Delete**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob"
    CloudBlob blob = container.GetBlobReference("myblob");

    // Delete the blob
    blob.Delete(); 

Passaggi successiviPassaggi successivi
--------------------------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio BLOB:
    -   [Informazioni di riferimento sulla libreria client .NET](http://msdn.microsoft.com/it-it/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/it-it/library/windowsazure/dd179355)
-   Per ulteriori informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere la pagina relativa all'[archiviazione e all'accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx).
-   Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
    -   Per archiviare dati strutturati, utilizzare [Archiviazione tabelle](/en-us/develop/net/how-to-guides/table-services/).
    -   Per archiviare dati relazionali, utilizzare [Database SQL](/en-us/develop/net/how-to-guides/sql-database/).

