<properties  linkid="dev-net-2-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Microsoft Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use the Windows Azure blob service to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use the Windows Azure Blob Storage Service in .NET" authors="tamram" />

# Come utilizzare l'archiviazione BLOB da .NET

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Negli esempi, scritti in C#, viene utilizzata la libreria del client di archiviazione di Azure per .NET. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps).

## Sommario

* [Informazioni sull'archiviazione BLOB](#what-is)
* [Concetti](#concepts)
* [Creazione di un account di archiviazione di Azure](#create-account)
* [Configurazione di una stringa di connessione di
  archiviazione](#setup-connection-string)
* [Procedura: Accedere all'archiviazione BLOB a livello di
  programmazione](#configure-access)
* [Procedura: creare un contenitore](#create-container)
* [Procedura: caricare un BLOB in un contenitore](#upload-blob)
* [Procedura: Elencare i BLOB in un contenitore](#list-blob)
* [Procedura: Scaricare BLOB](#download-blobs)
* [Procedura: Eliminare BLOB](#delete-blobs)
* [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span  class="short-header">Creazione di un account</span>Creazione di un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span  class="short-header">Configurazione di una stringa di connessione</span>Configurazione di una stringa di connessione di archiviazione

Nella libreria del client di archiviazione di Azure per .NET è possibile utilizzare una stringa di connessione di archiviazione per configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione. È possibile inserire la stringa di connessione di archiviazione in un file di configurazione, anziché impostarla come hardcoded nel codice:

* Quando si utilizza Servizi cloud di Azure, è consigliabile archiviare
  la stringa di connessione con il sistema di configurazione dei servizi
  di Azure (file `*.csdef` e `*.cscfg`).
* Quando si utilizza Siti Web di Azure, Macchine virtuali di Azure o si
  compilano applicazioni .NET destinate a essere eseguite all'esterno
  di Azure, è consigliabile archiviare la stringa di connessione con il
  sistema di configurazione .NET, ad esempio il file `web.config` o
  `app.config`.

Il recupero della stringa di connessione viene illustrato più avanti in questa guida.

### Configurazione della stringa di connessione per l'utilizzo con Servizi cloud

Il meccanismo di configurazione dei servizi è univoco per i progetti di Servizi cloud di Azure e consente di modificare dinamicamente le impostazioni di configurazione dal portale di gestione di Azure senza ridistribuire l'applicazione.

Per configurare la stringa di connessione nella configurazione dei servizi di Azure:

1.  All'interno di Esplora soluzioni di Visual Studio fare clic con il
    pulsante destro del mouse sul ruolo Web o sul ruolo di lavoro nella
    cartella **Ruoli** del progetto di distribuzione di Azure, quindi
    scegliere **Proprietà**.  
     ![Selezione delle proprietà per un ruolo servizio cloud in
    Visual Studio](./media/storage-dotnet-how-to-use-blobs-20/blob5.png)

2.  Fare clic sulla scheda **Impostazioni** e quindi sul pulsante
    **Aggiungi impostazione**.  
     ![Aggiunta di un'impostazione del servizio cloud in Visual
    Studio](./media/storage-dotnet-how-to-use-blobs-20/blob6.png)
    
    Nella griglia delle impostazioni verrà visualizzata una nuova voce
    **Impostazione1**.

3.  Nell'elenco a discesa **Tipo** della nuova voce **Impostazione1**
    selezionare **Stringa di connessione**.  
     ![BLOB7](./media/storage-dotnet-how-to-use-blobs-20/blob7.png)

4.  Fare clic sul pulsante **...** a destra della voce
    **Impostazione1**. Verrà visualizzata la finestra di dialogo
    **Stringa di connessione all'account di archiviazione**.

5.  Scegliere se si desidera utilizzare come destinazione l'emulatore
    di archiviazione (servizio Archiviazione di Azure simulato nel
    computer locale) oppure un account di archiviazione nel cloud. Il
    codice riportato in questa guida funziona con entrambe le opzioni.
    Se si desidera archiviare i dati BLOB nell'account di archiviazione
    creato in precedenza in Azure, immettere il valore di **Chiave di
    accesso primaria** copiato dal passaggio precedente di questa
    esercitazione.   
     ![BLOB8](./media/storage-dotnet-how-to-use-blobs-20/blob8.png)

6.  Modificare il valore **Impostazione1** della voce **Nome**
    specificando un nome più descrittivo, ad esempio
    **StorageConnectionString**. A questa stringa di connessione verrà
    fatto riferimento più avanti nel codice in questa guida.  
     ![BLOB9](./media/storage-dotnet-how-to-use-blobs-20/blob9.png)

### Impostazione della stringa di connessione con la configurazione .NET

Se si intende scrivere un'applicazione diversa da un servizio cloud di Azure (vedere la sezione precedente), è consigliabile utilizzare il sistema di configurazione .NET, ad esempio `web.config` o `app.config`. Oltre alle applicazioni progettate per l'esecuzione all'esterno di Azure, questo sistema include Siti Web di Azure o Macchine virtuali di Azure. Per archiviare la stringa di connessione, utilizzare l'elemento
`<a ppSettings>` come illustrato di seguito:

	<configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 Per ulteriori informazioni sulle stringhe di connessione di archiviazione, leggere [Configurazione delle stringhe di connessione][1].

 È ora possibile eseguire le procedure di questa guida.

## <a name="configure-access"> </a><span  class="short-header">Accedere a livello di programmazione</span>Procedura: Accedere all'archiviazione BLOB a livello di programmazione

### Recupero dell'assembly

 Per recuperare l'assembly `Microsoft.WindowsAzure.Storage.dll`, è possibile utilizzare NuGet. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

 `Il file `Microsoft.WindowsAzure.Storage.dll[ è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal ][2]Centro per sviluppatori .NET. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk -version>\ref\`.

### Dichiarazioni dello spazio dei nomi

 Aggiungere le seguenti dichiarazioni dello spazio dei nomi all'inizio del file C# in cui si desidera accedere ad Archiviazione di Azure a livello di programmazione:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

 Assicurarsi di fare riferimento all'assembly
`Microsoft.WindowsAzure.Storage.dll`.

### Recupero della stringa di connessione

 Per rappresentare le informazioni dell'account di archiviazione, è possibile utilizzare il tipo **CloudStorageAccount**. Se si intende utilizzare un modello di progetto Azure e/o si dispone di un riferimento a Microsoft.WindowsAzure.CloudConfigurationManager, è possibile utilizzare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile utilizzare **ConfigurationManager**. Sarà necessario aggiungere un riferimento a
System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

    using System.Configuration;?...?CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 Un tipo **CloudBlobClient** consente di recuperare oggetti che rappresentano contenitori e BLOB archiviati nel servizio di archiviazione BLOB. Il codice seguente consente di creare un oggetto **CloudBlobClient** utilizzando l'oggetto account di archiviazione
recuperato in precedenza:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Dipendenze ODataLib

 Le dipendenze ODataLib nella libreria del client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici sono
[OData][3], [Edm][4] e [Spatial][5].

## <a name="create-container"> </a><span  class="short-header">Creare un contenitore</span>Procedura: Creare un contenitore

 Tutti i BLOB di archiviazione risiedono in un contenitore. Per ottenere un riferimento al contenitore che si desidera utilizzare, è possibile utilizzare un oggetto **CloudBlobClient**. È possibile creare il contenitore se non esistente:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

 Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile impostare il contenitore come pubblico utilizzando il codice seguente:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 

 BlobContainerPublicAccessType.Blob }); I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di
Internet, tuttavia è possibile modificarli o eliminarli solo se si dispone della chiave di accesso appropriata.

 <h2> <a name="upload-blob"> </a><span  class="short-header">Caricare in un contenitore</span>Procedura: Caricare un BLOB in un contenitore</h2>


 In Archiviazione BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Nella maggior parte dei casi è consigliabile utilizzare il tipo di BLOB in blocchi.

 Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e utilizzarlo per ottenere un riferimento a un BLOB in blocchi. Dopo aver ottenuto un riferimento al BLOB, sarà possibile caricarvi qualsiasi flusso di dati chiamando il metodo **UploadFromStream**. Questa operazione consentirà di creare il BLOB se non già esistente o di sovrascriverlo se già esistente. Nell'esempio seguente viene illustrato come caricare un BLOB in un contenitore e si presuppone che il contenitore sia già stato creato.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span  class="short-header">Elencare i BLOB in un contenitore</span>Procedura: Elencare i BLOB in un contenitore

 Per elencare i BLOB in un contenitore, ottenere prima un riferimento al contenitore. Sarà quindi possibile utilizzare il metodo **ListBlobs** del contenitore per recuperare i BLOB e/o le directory in esso contenuti. Per accedere all'insieme avanzato di proprietà e metodi per un oggetto **IListBlobItem** restituito, è necessario eseguirne il cast in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile utilizzare un controllo del tipo per determinare quello in cui viene eseguito il cast. Nel codice seguente viene illustrato come recuperare e visualizzare l'URI di ogni elemento nel contenitore `photos`:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

 Come illustrato in precedenza, il servizio BLOB include anche il concetto di directory all'interno di contenitori. È quindi possibile organizzare i BLOB in una struttura più analoga a quella delle cartelle. Si consideri, ad esempio, il seguente set di BLOB in blocchi in un contenitore denominato `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

 Quando si chiama **ListBlobs** sul contenitore 'photos' (come nell'esempio precedente), la raccolta restituita conterrà gli oggetti **CloudBlobDirectory** e **CloudBlockBlob** che rappresentano le directory e i BLOB contenuti al primo livello. L'output risultante è il seguente:

    Directory: https://<a ccountname>.blob.core.windows.net/photos/2010/
    Directory: https://<a ccountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Facoltativamente, è possibile impostare su **true** il parametro **UseFlatBlobListing** del metodo **ListBlobs**. In questo modo tutti i BLOB verranno restituiti come **CloudBlockBlob**, indipendentemente dalla directory. La chiamata a **ListBlobs** è la seguente:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

 e questi sono i risultati:

    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<a ccountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<a ccountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<a ccountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<a ccountname>.blob.core.windows.net/photos/photo1.jpg

 Per ulteriori informazioni, vedere [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Scaricare BLOB</span>Procedura: Scaricare BLOB

 Per scaricare BLOB, recuperare prima un riferimento al BLOB e quindi chiamare il metodo **DownloadToStream**. Nell'esempio seguente il metodo **DownloadToStream** viene utilizzato per trasferire il contenuto del BLOB a un oggetto stream che è quindi possibile rendere persistente in un file locale.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

 È anche possibile utilizzare il metodo **DownloadToStream** per scaricare il contenuto di un BLOB come stringa di testo.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## <a name="delete-blobs"> </a><span  class="short-header">Eliminare BLOB</span>Procedura: Eliminare BLOB

 Per eliminare un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il metodo **Delete**.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    
    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");
    
    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a><span  class="short-header">Passaggi successivi</span>Passaggi successivi

 A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

 *  Per informazioni dettagliate sulle API disponibili, vedere la
  documentazione di riferimento del servizio BLOB:
  *  [Informazioni di riferimento sulla libreria del client di
    archiviazione per .NET][6]
  *  [Informazioni di riferimento sulle API REST][7]
  

*  Per ulteriori informazioni sulle attività avanzate che è possibile
  eseguire con Archiviazione di Azure, vedere la pagina relativa
  all'[archiviazione e all'accesso di dati in Azure][8].
*  Per ulteriori opzioni di archiviazione dei dati in Azure, consultare
  altre guide alle funzionalità.
  *  Per archiviare dati strutturati, utilizzare [Archiviazione
    tabelle](/en-us/develop/net/how-to-guides/table-services/).
  *  Per archiviare dati relazionali, utilizzare [Database
    SQL](/en-us/develop/net/how-to-guides/sql-database/).
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx