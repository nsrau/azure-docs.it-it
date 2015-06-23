<properties 
	pageTitle="Come usare Servizi multimediali (Java) - Guida alle funzionalità di Azure" 
	description="Descrive come usare Servizi multimediali di Azure per eseguire attività comuni, tra cui codifica, crittografia e streaming delle risorse." 
	services="media-services" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Come usare Servizi multimediali

In questa guida viene illustrato come iniziare a programmare con Servizi multimediali di Azure in Java. Questa guida include informazioni tecniche su Servizi multimediali, le procedure per la configurazione dell'account Azure per Servizi multimediali ed esempi di codice che illustrano come eseguire attività di programmazione comuni. 

## <a id="media_services"></a>Informazioni su Servizi multimediali

Servizi multimediali di Azure costituisce una piattaforma multimediale estensibile che integra il meglio di Microsoft Media Platform e componenti multimediali di terze parti in Azure. Servizi multimediali offre una pipeline multimediale nel cloud che consente ai partner di settore di estendere o sostituire tecnologie di componenti. Fornitori di software indipendenti e provider di contenuti multimediali possono usare Servizi multimediali per creare soluzioni multimediali end-to-end. In questa panoramica vengono descritti gli scenari di sviluppo comuni e l'architettura generale per Servizi multimediali.

Nel diagramma riportato di seguito viene illustrata l'architettura di Servizi multimediali di base.

![Architettura di servizi multimediali](./media/media-services-java-how-to-use/wams-01.png)

### Supporto delle funzionalità di Servizi multimediali
Nella versione corrente di Servizi multimediali viene fornito il seguente set di funzionalità per lo sviluppo di applicazioni multimediali nel cloud. 

- **Inserimento**. Le operazioni di inserimento consentono di inserire gli asset nel sistema, ad esempio caricandoli e crittografandoli prima che vengano collocati nel servizio di archiviazione di Azure. Servizi multimediali offre l'integrazione con componenti partner per fornire soluzioni di caricamento rapide tramite protocollo UDP (User Diagram Protocol).
- **Codifica**. Le operazioni di codifica consentono di codificare, trasformare e convertire asset multimediali. È possibile eseguire le attività di codifica nel cloud tramite il codificatore multimediale incluso in Servizi multimediali. Di seguito sono riportate le opzioni di codifica disponibili:
   - Uso di Azure Media Encoder e di un'ampia gamma di codec e formati standard, inclusi quelli principali, come ad esempio IIS Smooth Streaming, MP4 e conversione in Apple HTTP Live Streaming.
   - Conversione di intere librerie o singoli file con controllo completo su input e output.
   - Ampio set di tipi di file, formati e codec supportati (vedere [Tipi di file supportati per Servizi multimediali][]).
   - Conversioni tra formati supportati. Servizi multimediali consente di convertire file MP4 ISO (con estensione mp4) al formato di file Smooth Streaming (PIFF 1.3) ( con estensione ismv o isma). Consente inoltre di convertire il formato di file Smooth Streaming (PIFF) al formato Apple HTTP Live Streaming (con estensione msu8 o ts).
- **Protezione**. Per proteggere il contenuto è necessario crittografare il contenuto Live Streaming o su richiesta per il trasporto, l'archiviazione e la distribuzione sicuri. Servizi multimediali fornisce una soluzione DRM indipendente dalla tecnologia per la protezione del contenuto.  Le tecnologie DRM attualmente supportate sono Microsoft PlayReady e MPEG Common Encryption. A breve sarà disponibile il supporto per altre tecnologie DRM. 
- **Streaming**. Durante lo streaming il contenuto viene distribuito in diretta o su richiesta ai client oppure è possibile recuperare o scaricare file multimediali specifici dal cloud. Servizi multimediali fornisce una soluzione indipendente dal formato per i contenuti in streaming.  Servizi multimediali offre il supporto dell'origine di streaming per i formati Smooth Streaming, Apple HTTP Live Streaming ed MP4. A breve sarà disponibile il supporto per altri formati. È anche possibile distribuire contenuti in streaming mediante una rete CDN di Azure o una rete CDN di terze parti che offre la scalabilità per la distribuzione a milioni di utenti.   

### Scenari di sviluppo di Servizi multimediali
Servizi multimediali supporta numerosi scenari di sviluppo di contenuti multimediali comuni, come illustrato nella seguente tabella. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Scenario</th>
       <th>Descrizione</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Creazione di flussi di lavoro end-to-end</td>
        <td>Creare flussi di lavoro multimediali completi interamente nel cloud. Dal caricamento dei file multimediali alla distribuzione del contenuto, Servizi multimediali offre un'ampia gamma di componenti che possono essere combinati per gestire flussi di lavoro dell'applicazione specifici. Le funzionalità correnti includono caricamento, archiviazione, codifica, conversione di formati, protezione del contenuto e distribuzione di contenuti in streaming su richiesta.</td>
    </tr>
    <tr>
        <td>Creazione di flussi di lavoro ibridi</td>
        <td>È possibile integrare Servizi multimediali con strumenti e processi esistenti. Ad esempio, codificare il contenuto in sede e quindi caricarlo in Servizi multimediali per la transcodifica in più formati e la distribuzione tramite la rete CDN di Azure o una rete CDN di terze parti. È possibile chiamare Servizi multimediali individualmente tramite le API REST standard per l'integrazione con applicazioni e servizi esterni.</td>
    </tr>
    <tr>
        <td>Supporto cloud per lettori multimediali</td>
        <td>È possibile creare, gestire e distribuire contenuti multimediali su più dispositivi (iOS, Android e Windows) e piattaforme.</td>
    </tr>
  </tbody>
</table>

### Sviluppo client di Servizi multimediali
È possibile estendere la copertura della soluzione Servizi multimediali usando SDK e Player framework per creare applicazioni client multimediali. Questi client sono concepiti per gli sviluppatori che vogliono creare applicazioni Servizi multimediali che offrono un'esperienza utente accattivante su un'ampia gamma di dispositivi e piattaforme. A seconda dei dispositivi per i quali si intende compilare applicazioni client, sono disponibili varie opzioni di SDK e Player Framework di Microsoft e altri partner di terze parti.  

Di seguito è riportato un elenco di SDK e Player Framework client disponibili.  Per maggiori informazioni su questi e altri SDK e Player Framework pianificati e le relative funzionalità supportate, vedere gli argomenti relativi allo [sviluppo di client per Servizi multimediali]. 

#### Supporto client Mac e PC  
Per i PC e i Mac è possibile creare un'esperienza di streaming usando Microsoft Silverlight o Adobe Open Source Media Framework.

-	[Smooth Streaming Client per Silverlight](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform: Player Framework per Silverlight](http://smf.codeplex.com/documentation)
-	[Plug-in Smooth Streaming per OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022). Per informazioni sull'uso di questo plug-in, vedere [Come usare il plug-in Microsoft Smooth Streaming per Adobe Open Source Media Framework](http://go.microsoft.com/fwlink/?LinkId=275034).

#### Applicazioni per Windows 8
Per Windows 8, è possibile creare app di Windows Store usando uno qualsiasi dei costrutti e dei linguaggi di sviluppo supportati come HTML, JavaScript, XAML, C# e C+.

-	[Smooth Streaming Client SDK per Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Per altre informazioni su come creare un'app di Windows Store usando questo SDK, vedere [Come creare un'applicazione Windows Store Smooth Streaming](http://go.microsoft.com/fwlink/?LinkId=271647). Per informazioni su come creare un lettore Smooth Streaming in HTML5, vedere la [procedura dettagliata relativa alla creazione del primo lettore Smooth Streaming in HTML5](http://msdn.microsoft.com/library/jj573656.aspx).

-	[Microsoft Media Platform: Player Framework per app di Windows Store per Windows 8](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

#### XBox
Xbox supporta le applicazioni Xbox LIVE che possono usare contenuto Smooth Streaming. L'ADK (Application Development Kit) di Xbox LIVE include:

-	Smooth Streaming Client per Xbox LIVE ADK
-	Microsoft Media Platform: Player Framework per Xbox LIVE ADK

#### Dispositivi incorporati o dedicati
Dispositivi quali TV connessi, set-top box, lettori Blu-Ray, OTT TV box e dispositivi mobili che dispongono di un framework di sviluppo applicazioni personalizzato e di una pipeline multimediale personalizzata. Microsoft fornisce i seguenti kit per il porting che possono essere concessi in licenza e che consentono ai partner di eseguire il porting della riproduzione Smooth Streaming per la piattaforma.

-	[Microsoft Smooth Streaming Client Porting Kit](http://www.microsoft.com/mediaplatform/sspk.aspx)
-	[Microsoft PlayReady Device Porting Kit](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

#### Windows Phone
Microsoft fornisce un SDK che può essere usato per creare applicazioni video premium per Windows Phone. 

-	[Smooth Streaming Client per Silverlight](http://www.iis.net/download/smoothclient)
-	[Microsoft Media Platform: Player Framework per Silverlight](http://smf.codeplex.com/documentation)

#### Dispositivi iOS
Per i dispositivi iOS, ad esempio iPhone, iPod e iPad, Microsoft fornisce un SDK che consente di creare applicazioni per queste piattaforme e distribuire contenuti video premium: Smooth Streaming SDK per dispositivi iOS con PlayReady.  L'SDK è disponibile solo per i titolari di una licenza. Per altre informazioni, [inviare un messaggio e-mail a Microsoft](mailto:askdrm@microsoft.com). Per informazioni sullo sviluppo per iOS, visitare il [sito dedicato agli sviluppatori per iOS](https://developer.apple.com/devcenter/ios/index.action).

#### Dispositivi Android
Numerosi partner Microsoft forniscono SDK per la piattaforma Android che consentono di aggiungere la funzionalità di riproduzione dei contenuti Smooth Streaming nei dispositivi Android. Per informazioni dettagliate sui partner, [inviare un messaggio e-mail a Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices).


## <a id="setup-account"></a>Configurare un account Azure per Servizi multimediali

Per configurare l'account di Servizi multimediali, usare il portale di gestione di Azure. Vedere l'argomento [Come creare un account di Servizi multimediali][]. Dopo avere creato l'account nel portale di gestione, sarà possibile configurare il computer per lo sviluppo in Servizi multimediali. 

## <a id="setup-dev"></a>Configurare lo sviluppo con Servizi multimediali

Questa sezione illustra i prerequisiti generali per lo sviluppo di applicazioni Servizi multimediali tramite l'SDK di Servizi multimediali per Java.

### Prerequisiti

-   Un account di Servizi multimediali ottenuto con una sottoscrizione di Azure nuova o esistente. Vedere l'argomento [Come creare un account di Servizi multimediali][].
-   Librerie di Azure per Java, che si possono installare dal [Centro per sviluppatori Java in Azure][].

## <a if="connect"></a>Procedura: Usare Servizi multimediali con Java

Il seguente codice illustra come creare un asset, caricare un file multimediale nell'asset, eseguire un processo con un'attività per trasformare l'asset e scaricare i file di output dell'asset trasformato.

Prima di usare il codice, sarà necessario configurare un account di Servizi multimediali. Per informazioni su come configurare un account, vedere [Come creare un account di Servizi multimediali](http://www.windowsazure.com/manage/services/media-services/how-to-create-a-media-services-account/).

Sostituire le variabili `clientId` e `clientSecret` con i valori personali. Il codice si basa inoltre su un file archiviato localmente, `c:/media/MPEG4-H264.mp4`. You'll need to provide your own file to use. The code also requires an output folder, `c:/output`, ossia dove verranno scaricati i file di output.

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

Gli asset creati vengono archiviati nel servizio di archiviazione di Azure. Tuttavia, usare solo le API di Servizi multimediali di Azure (non le API del servizio di archiviazione di Azure) per aggiungere, aggiornare o eliminare asset.

### Determinare i processori di contenuti multimediali disponibili

Il codice precedente usa un processore di contenuti multimediali e vi accede tramite un nome di processore di contenuti multimediali specifico (se fosse presente più di una versione, verrebbe usata la versione più recente). Per determinare quali sono i processori di contenuti multimediali disponibili, è possibile usare il seguente codice.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

In alternativa, il seguente codice illustra come recuperare l'ID di un processore di contenuti multimediali in base al nome.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### Annullare un processo
Se occorre annullare un processo che è ancora in corso di elaborazione, usare il seguente codice che consente di annullare un processo in base all'ID processo.

    mediaService.action(Job.cancel(jobId));

## Risorse aggiuntive

Per la documentazione Java per Servizi multimediali, vedere le [librerie di Azure per la documentazione Java][].

<!-- URLs. -->

  [Come creare un account di Servizi multimediali]: http://go.microsoft.com/fwlink/?linkid=256662
  [Centro per sviluppatori Java in Azure]: http://www.windowsazure.com/develop/java/
  [Librerie di Azure per la documentazione Java]: http://dl.windowsazure.com/javadoc/
  [sviluppo di client per Servizi multimediali]: http://msdn.microsoft.com/library/windowsazure/dn223283.aspx



<!--HONumber=52--> 