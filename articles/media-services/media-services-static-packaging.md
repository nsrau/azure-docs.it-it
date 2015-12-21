<properties 
	pageTitle="Utilizzare Azure Media Packager per eseguire attività di creazione statica dei pacchetti" 
	description="In questo argomento vengono mostrate varie attività eseguite con Azure Media Packager." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/05/2015"   
	ms.author="juliako"/>


# Utilizzare Azure Media Packager per eseguire attività di creazione statica dei pacchetti

>[AZURE.NOTE]Windows Azure Media Packager e Windows Azure Media Encryptor raggiungeranno la fine della vita il 1° marzo 2016. In quel momento, questi componenti non saranno più disponibili. Le funzionalità di crittografia e di conversione di formato verranno rese disponibile tramite la creazione dinamica dei pacchetti e la crittografia dinamica.

## Panoramica

Per distribuire un video digitale tramite Internet è necessario comprimere il file multimediale. I file video digitali hanno dimensioni piuttosto elevate e possono risultare troppo grandi per la distribuzione su Internet o per la visualizzazione corretta sui dispositivi dei clienti. Mediante il processo di codifica è possibile comprimere video e audio per consentire ai clienti di visualizzare i file multimediali. Una volta codificato, un video può essere inserito in contenitori di file diversi. Il processo di collocare supporti di memorizzazione codificati in un contenitore è denominato creazione di pacchetti. Ad esempio, è possibile prendere un file MP4 e convertirlo in un contenuto Smooth Streaming o HLS usando Azure Media Packager. Per altre informazioni, vedere [confronto tra le funzionalità di codifica e di creazione pacchetti](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Servizi Multimediali supporta pacchetti statici e dinamici. Quando si utilizza il pacchetto di creazione statico è necessario creare una copia dei contenuti in ognuno dei formati richiesti dagli utenti. Con il pacchetto di creazione dinamico, è sufficiente creare un asset che contenga un set di file a velocità in bit adattiva MP4 o Smooth Streaming. In base al formato specificato nella richiesta del manifesto o del frammento, il server di streaming on demand garantirà agli utenti che il flusso sia ricevuto nel protocollo scelto. Di conseguenza, si archiviano e si pagano solo i file in un singolo formato di archiviazione e il servizio Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client.

>[AZURE.NOTE]Si consiglia di utilizzare [pacchetto di creazione dinamico](media-services-dynamic-packaging-overview.md).

Tuttavia, esistono alcuni scenari che richiedono la pacchetti di creazione statica:

- Convalida di velocità in bit adattiva MP4s codificata con codificatori esterni (ad esempio utilizzando codificatori di terze parti).

È possibile utilizzare anche pacchetti di creazione statici per eseguire le attività seguenti. È tuttavia consigliabile utilizzare la crittografia dinamica.

- Utilizzare la crittografia statica per proteggere i formati Smooth e MPEG DASH con PlayReady
- Utilizzare la crittografia statica per proteggere i pacchetti HLSv3 con AES-128
- Utilizzare la crittografia statica per proteggere i pacchetti HLSv3 con PlayReady


## Convalida della velocità in bit adattiva di MP4 codificata con codificatori esterni

Se si desidera utilizzare un set di file MP4 a velocità in bit adattiva (più velocità in bit) che non siano stati codificati con Media Services Encoder, è necessario convalidare i file prima di un'ulteriore elaborazione. Media Services Packager può convalidare un asset che contiene un set di file MP4 e verificare se l'asset può essere inserito in Smooth Streaming o HLS. Se l'attività di convalida non riesce, il processo di elaborazione dell'attività verrà completato con un errore. Il codice XML che definisce il set di impostazioni per l'attività di convalida, può essere trovato nell’argomento [Attività predefinite per Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx).

>[AZURE.NOTE]Per evitare problemi di runtime, utilizzare Media Services Encoder per generare o Media Services Packager per convalidare il contenuto. Se il server di Streaming su richiesta non è in grado di analizzare i file di origine in fase di esecuzione, si riceverà l'errore HTTP 1.1 "415 Unsupported Media Type". Impedisce ripetutamente al server di eseguire l'analisi dei file di origine sulle prestazioni del server Streaming on demand e può ridurre la larghezza di banda disponibile per l'elaborazione di altre richieste. Servizi multimediali di Azure offre un Contratto di servizio (SLA) dei suoi servizi di streaming on demand; tuttavia, questo contratto di servizio non può essere rispettato se il server non viene correttamente utilizzato nel modo descritto in precedenza.

In questa sezione viene illustrato come elaborare le attività di convalida. Viene inoltre illustrato come visualizzare lo stato e il messaggio di errore del processo che viene completato con JobStatus.Error.

Per convalidare i file MP4 con Media Services Packager, è necessario creare un proprio file manifesto (.ism) e caricarlo insieme ai file di origine nell'account di servizi multimediali. Di seguito è riportato un esempio del file con estensione .ism generato da Azure Media Encoder. I nomi dei file fanno la differenza tra maiuscole e minuscole. Inoltre, assicurarsi che il testo nel file .ism sia codificato con UTF-8.

	
	<?xml version="1.0" encoding="utf-8" standalone="yes"?>
	<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
	  <head>
	<!-- Tells the server that these input files are MP4s – specific to Dynamic Packaging -->
	    <meta name="formats" content="mp4" /> 
	  </head>
	  <body>
	    <switch>
	      <video src="BigBuckBunny_1000.mp4" />
	      <video src="BigBuckBunny_1500.mp4" />
	      <video src="BigBuckBunny_2250.mp4" />
	      <video src="BigBuckBunny_3400.mp4" />
	      <video src="BigBuckBunny_400.mp4" />
	      <video src="BigBuckBunny_650.mp4" />
	      <audio src="BigBuckBunny_400.mp4" />
	    </switch>
	  </body>
	</smil>

Dopo avere creato la serie MP4 con velocità in bit adattiva, è possibile sfruttare i pacchetti di creazione dinamici. I pacchetti di creazione dinamici consentono di distribuire flussi nel protocollo specificato senza creare ulteriori pacchetti. Per altre informazioni, vedere [pacchetti di creazione dinamici](media-services-dynamic-packaging-overview.md).

L’esempio di codice seguente utilizza le estensioni di Azure Media Services .NET SDK. Assicurarsi di aggiornare il codice in modo che punti alla cartella dove si trovano i file MP4 di input e un file con estensione .ism. E inoltre a dove si trova il file Mediapackager\_validatetask. Questo file XML è definito nell’argomento [set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx).
	
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Xml.Linq;
	
	namespace MediaServicesStaticPackaging
	{
	    class Program
	    {
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        // The MultibitrateMP4Files folder should also
	        // contain the .ism manifest file.
	        private static readonly string _multibitrateMP4s =
	            Path.Combine(_mediaFiles, @"MultibitrateMP4Files");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Ingest a set of multibitrate MP4s.
	            //
	            // Use the SDK extension method to create a new asset by 
	            // uploading files from a local directory.
	            IAsset multibitrateMP4sAsset = _context.Assets.CreateFromFolder(
	                _multibitrateMP4s,
	                AssetCreationOptions.None,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	            // Use Azure Media Packager to validate the files.
	            IAsset validatedMP4s =
	                ValidateMultibitrateMP4s(multibitrateMP4sAsset);
	
	            // Publish the asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                validatedMP4s,
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	                                 // Get the streaming URLs.
	            Console.WriteLine("Smooth Streaming URL:");
	            Console.WriteLine(validatedMP4s.GetSmoothStreamingUri().ToString());
	            Console.WriteLine("MPEG DASH URL:");
	            Console.WriteLine(validatedMP4s.GetMpegDashUri().ToString());
	            Console.WriteLine("HLS URL:");
	            Console.WriteLine(validatedMP4s.GetHlsUri().ToString());
	        }
	
	        public static IAsset ValidateMultibitrateMP4s(IAsset multibitrateMP4sAsset)
	        {
	            // Set .ism as a primary file 
	            // in a multibitrate MP4 set.
	            SetISMFileAsPrimary(multibitrateMP4sAsset);
	
	            // Create a new job.
	            IJob job = _context.Jobs.Create("MP4 validation and converstion to Smooth Stream job.");
	
	            // Read the task configuration data into a string. 
	            string configMp4Validation = File.ReadAllText(Path.Combine(
	                    _configurationXMLFiles,
	                    "MediaPackager_ValidateTask.xml"));
	
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaPackager);
	
	            // Create a task with the conversion details, using the configuration data. 
	            ITask task = job.Tasks.AddNew("Mp4 Validation Task",
	                processor,
	                configMp4Validation,
	                TaskOptions.None);
	
	            // Specify the input asset to be validated.
	            task.InputAssets.Add(multibitrateMP4sAsset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted). 
	            task.OutputAssets.AddNew("Validated output asset",
	                    AssetCreationOptions.None);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	          
	            // If the validation task fails and job completes with JobState.Error,
	            // display the error message and throw an exception.
	            if (job.State == JobState.Error)
	            {
	                Console.WriteLine("  Job ID: " + job.Id);
	                Console.WriteLine("  Name: " + job.Name);
	                Console.WriteLine("  State: " + job.State);
	
	                foreach (var jobTask in job.Tasks)
	                {
	                    Console.WriteLine("  Task Id: " + jobTask.Id);
	                    Console.WriteLine("  Name: " + jobTask.Name);
	                    Console.WriteLine("  Progress: " + jobTask.Progress);
	                    Console.WriteLine("  Configuration: " + jobTask.Configuration);
	                    Console.WriteLine("  Running time: " + jobTask.RunningDuration);
	                    if (jobTask.ErrorDetails != null)
	                    {
	                        foreach (var errordetail in jobTask.ErrorDetails)
	                        {
	
	                            Console.WriteLine("  Error Message:" + errordetail.Message);
	                            Console.WriteLine("  Error Code:" + errordetail.Code);
	                        }
	                    }
	                }
	                throw new Exception("The specified multi-bitrate MP4 set is not valid.");
	            }
	
	
	            return job.OutputMediaAssets[0];
	        }
	
	        static void SetISMFileAsPrimary(IAsset asset)
	        {
	            var ismAssetFiles = asset.AssetFiles.ToList().
	                Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
	
	            // The following code assigns the first .ism file as the primary file in the asset.
	            // An asset should have one .ism file.  
	            ismAssetFiles.First().IsPrimary = true;
	            ismAssetFiles.First().Update();
	        }
	    }
	}

## Utilizzare la crittografia statica per proteggere i formati Smooth e MPEG DASH con PlayReady

Se si desidera proteggere i contenuti con PlayReady, è possibile scegliere di utilizzare [la crittografia dinamica](media-services-protect-with-drm.md) (opzione consigliata) o la crittografia statica (come descritto in questa sezione).

L'esempio riportato in questa sezione consente di codificare un file in formato intermedio (in questo caso MP4) MP4 a velocità in bit adattiva. Poi crea dei pacchetti MP4s in Smooth Streaming e quindi crittografa Smooth Streaming con PlayReady. Di conseguenza si è in grado di trasmettere Smooth Streaming o MPEG DASH.

Servizi multimediali offre un servizio per la distribuzione di licenze Microsoft PlayReady. Nell'esempio riportato in questo articolo viene illustrato come configurare il servizio di recapito licenze PlayReady di servizi multimediali (vedere il metodo ConfigureLicenseDeliveryService definito nel codice riportato di seguito). Per ulteriori informazioni sul servizio di recapito licenza PlayReady per servizi multimediali, vedere [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze](media-services-protect-with-drm.md).

>[AZURE.NOTE]Per distribuire il contenuto crittografato MPEG DASH con PlayReady, assicurarsi di utilizzare le opzioni CENC impostando le proprietà e useSencBox e adjustSubSamples (descritte nel [set di impostazioni di attività per Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx) argomento) su true.


Assicurarsi di aggiornare il codice seguente in modo che punti alla cartella in cui si trova il file MP4 di input.

E anche a dove si trovano i file Mediapackager\_mp4tosmooth e MediaEncryptor\_PlayReadyProtection.xml. Mediapackager\_mp4tosmooth è definito in [set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx) e MediaEncryptor\_PlayReadyProtection.xml è definito nell’argomento[set di impostazioni di attività per Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx).

Nell'esempio viene definito il metodo UpdatePlayReadyConfigurationXMLFile che è possibile utilizzare per aggiornare dinamicamente il file MediaEncryptor\_PlayReadyProtection.xml. Se si ha il seme chiave disponibile, è possibile utilizzare il metodo CommonEncryption.GeneratePlayReadyContentKey per generare la chiave simmetrica sulla base dei valori keySeedValue e KeyId.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace PlayReadyStaticEncryptAndKeyDeliverySvc
	{
	    class Program
	    {
	       
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Encoding and encrypting assets //////////////////////
	            // Load a single MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset =
	                ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create a common encryption content key that is used 
	            // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	            //    that is used for encryption.
	            // b) to configure the license delivery service and 
	            //
	            Guid keyId;
	            byte[] contentKey;
	
	            IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);
	
	            // The content key authorization policy must be configured by you 
	            // and met by the client in order for the PlayReady license
	            // to be delivered to the client. 
	            // In this example the Media Services PlayReady license delivery service is used.
	            ConfigureLicenseDeliveryService(key);
	
	            // Get the Media Services PlayReady license delivery URL.
	            // This URL will be assigned to the licenseAcquisitionUrl property 
	            // of the MediaEncryptor_PlayReadyProtection.xml file.
	            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
	
	            // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
	            UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);
	
	
	            // Encrypt your clear Smooth Streaming to Smooth Streaming with PlayReady.
	            IAsset outputAsset = CreateSmoothStreamEncryptedWithPlayReady(clearSmoothStreamAsset);
	
	
	            // You can use the http://smf.cloudapp.net/healthmonitor player 
	            // to test the smoothStreamURL URL.
	            string smoothStreamURL = outputAsset.GetSmoothStreamingUri().ToString();
	            Console.WriteLine("Smooth Streaming URL:");
	            Console.WriteLine(smoothStreamURL);
	
	            // You can use the http://dashif.org/reference/players/javascript/ player 
	            // to test the dashURL URL.
	            string dashURL = outputAsset.GetMpegDashUri().ToString();
	            Console.WriteLine("MPEG DASH URL:");
	            Console.WriteLine(dashURL);
	        }
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Stream.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains the Smooth Streaming asset.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Encrypts Smooth Stream with PlayReady.
	        /// Then creates a Smooth Streaming Url.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateSmoothStreamEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
	        {
	            // Create a job.
	            IJob job = _context.Jobs.Create("Encrypt to PlayReady Smooth Streaming.");
	
	            // Add task 1 - Encrypt Smooth Streaming with PlayReady 
	            IAsset encryptedSmoothAsset =
	                EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // The OutputMediaAssets[0] contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[0],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[0];
	        }
	
	        /// <summary>
	        /// Create a common encryption content key that is used 
	        /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	        /// that is used for encryption.
	        /// </summary>
	        /// <param name="keyId"></param>
	        /// <param name="contentKey"></param>
	        /// <returns></returns>
	        public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
	        {
	            keyId = Guid.NewGuid();
	            contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	        /// <summary>
	        /// Update your configuration .xml file dynamically.
	        /// </summary>
	        public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
	        {
	            string xmlFileName = Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml");
	
	            XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";
	
	            // Prepare the encryption task template
	            XDocument doc = XDocument.Load(xmlFileName);
	
	            var licenseAcquisitionUrlEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
	                    .FirstOrDefault();
	            var contentKeyEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "contentKey")
	                    .FirstOrDefault();
	            var keyIdEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "keyId")
	                    .FirstOrDefault();
	
	            // Update the "value" property.
	            if (licenseAcquisitionUrlEl != null)
	                licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());
	
	            if (contentKeyEl != null)
	                contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));
	
	            if (keyIdEl != null)
	                keyIdEl.Attribute("value").SetValue(keyId);
	
	            doc.Save(xmlFileName);
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	            return asset;
	        }
	
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles,
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset =
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Stream",
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	
	        /// <summary>
	        /// Creates a task to encrypt Smooth Streaming with PlayReady.
	        /// Note: To deliver DASH, make sure to set the useSencBox and adjustSubSamples 
	        /// configuration properties to true. 
	        /// In this example, MediaEncryptor_PlayReadyProtection.xml contains configuration.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
	            IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaEncryptor);
	
	            // Read the configuration XML.
	            //
	            // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
	            // is using keySeedValue. It is recommended that you do this only for testing 
	            // and not in production. For more information, see 
	            // http://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
	            //
	            string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml"));
	
	            ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
	               playreadyProcessor,
	               configPlayReady,
	               TaskOptions.ProtectedConfiguration);
	
	            playreadyTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
	            IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
	                                            "PlayReady Smooth Streaming",
	                                            AssetCreationOptions.CommonEncryptionProtected);
	
	            return playreadyAsset;
	        }
	
	        /// <summary>
	        /// Configures authorization policy for the content key. 
	        /// </summary>
	        /// <param name="contentKey">The content key.</param>
	        static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
	        {
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction 
	                { 
	                    Name = "Open", 
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady license template.
	            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption policyOption =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, newLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	    }
	}

## Utilizzare la crittografia statica per proteggere i pacchetti HLSv3 con AES-128

Se si desidera crittografare il contenuto HLS con AES-128, è possibile scegliere di utilizzare la crittografia dinamica (opzione consigliata) o crittografia statica (come illustrato in questa sezione). Se si decide di utilizzare la crittografia dinamica, vedere [utilizzare la crittografia AES-128 dinamica e il servizio di recapito chiave](media-services-protect-with-aes128).

>[AZURE.NOTE]Per convertire il contenuto in formato HLS, è necessario prima convertire/codificare il contenuto in formato Smooth Streaming. Inoltre, per crittografare HLS con AES assicurarsi di impostare le proprietà seguenti nel file MediaPackager\_SmoothToHLS.xml: impostare la proprietà encrypt su true, impostare il valore della chiave e il valore keyuri in modo che punti al server di autenticazione\\autorizzazione.. Servizi multimediali creerà un file di chiave e lo posizionerà nel contenitore di asset. Si deve copiare il file /asset-containerguid/*.key nel server (o creare un proprio file di chiave) e poi eliminare il file *.key dal contenitore di asset.

L'esempio riportato in questa sezione codifica un file in formato intermedio (in questo caso MP4) in file MP4 a velocità multipla e poi i pacchetti MP4s in Smooth Streaming. Viene poi inserito Smooth Streaming in HTTP Live Streaming (HLS) crittografato con crittografia Advanced Encryption Standard (AES) 128-bit. Assicurarsi di aggiornare il codice seguente in modo che punti alla cartella in cui si trova il file MP4 di input. E inoltre a dove si trovano i file di configurazione Mediapackager\_mp4tosmooth e MediaPackager\_SmoothToHLS.xml. È possibile trovare la definizione per questi file nell’argomento [set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx).
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	
	namespace MediaServicesContentProtection
	{
	    class Program
	    {
	        // Paths to support files (within the above base path). You can use 
	        // the provided sample media files from the "SupportFiles" folder, or 
	        // provide paths to your own media files below to run these samples.
	
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	        
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName = 
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey = 
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName, 
	                            _mediaServicesAccountKey);
	            // Use the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Encoding and encrypting assets //////////////////////
	
	            // Load an MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create HLS encrypted with AES.
	            IAsset HLSEncryptedWithAESAsset = CreateHLSEncryptedWithAES(clearSmoothStreamAsset);
	
	            // You can use the following player to test the HLS with AES stream.
	            // http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614 
	            string hlsWithAESURL = HLSEncryptedWithAESAsset.GetHlsUri().ToString();
	            Console.WriteLine("HLS with AES URL:");
	            Console.WriteLine(hlsWithAESURL);
	        }
	
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains Smooth Streaming.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Encrypts an HLS with AES-128.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateHLSEncryptedWithAES(IAsset clearSmoothStreamAsset)
	        {
	            IJob job = _context.Jobs.Create("Encrypt to HLS with AES.");
	
	            // Add task 1 - Package clear Smooth Streaming to HLS with AES.
	            PackageSmoothStreamToHLS(job, clearSmoothStreamAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // The OutputMediaAssets[0] contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[0],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[0];
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	 
	            return asset;
	        }
	
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s", 
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles, 
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset = 
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming", 
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	        /// <summary>
	        /// Converts Smooth Streaming to HLS.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The Smooth Streaming asset.</param>
	        /// <returns>The asset that was packaged to HLS.</returns>
	        private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Read the configuration data into a string. 
	            // For the HLS to get encrypted with AES make sure to set the
	            // encrypt configuration property to true.
	            //
	            // In production, it is recommended to do the following:
	            //    Set a Key url for your authn/authz server.
	            //    Copy the /asset-containerguid/*.key file to your server (or craft a key file for yourself).
	            //    Delete *.key from the asset container.
	            //
	            string configuration = File.ReadAllText(Path.Combine(_configurationXMLFiles, @"MediaPackager_SmoothToHLS.xml"));
	
	            // Create a task with the encoding details, using a configuration file.
	            ITask task = job.Tasks.AddNew("My Smooth Streaming to HLS Task",
	               processor,
	               configuration,
	               TaskOptions.ProtectedConfiguration);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(smoothStreamAsset);
	
	            // Add an output asset to contain the results of the job. 
	            IAsset outputAsset = 
	                task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);
	
	
	            return outputAsset;
	        }
	    }
	}

## Utilizzare la crittografia statica per proteggere i pacchetti HLSv3 con PlayReady

Se si desidera proteggere i contenuti con PlayReady, è possibile scegliere di utilizzare [la crittografia dinamica](media-services-protect-with-drm.md) (opzione consigliata) o la crittografia statica (come descritto in questa sezione).

>[AZURE.NOTE]Per proteggere il contenuto mediante PlayReady è necessario innanzitutto convertire/codificare il contenuto in un formato Smooth Streaming.

L'esempio riportato in questa sezione codifica un file in formato intermedio (in questo caso MP4) in file MP4 a velocità multipla. Poi crea dei pacchetti MP4s in Smooth Streaming e crittografa Smooth Streaming con PlayReady. Per produrre HTTP Live Streaming (HLS) crittografato con PlayReady, si deve inserire in un pacchetto HLS l'asset Smooth Streaming PlayReady. In questo argomento viene illustrato come eseguire questa procedura.

Servizi multimediali offre un servizio per la distribuzione di licenze Microsoft PlayReady. Nell'esempio riportato in questo articolo viene illustrato come configurare il servizio di recapito licenze PlayReady di servizi multimediali (vedere il metodo **ConfigureLicenseDeliveryService** definito nel codice riportato di seguito).

Assicurarsi di aggiornare il codice seguente in modo che punti alla cartella in cui si trova il file MP4 di input. E inoltre a dovei si trovano i file Mediapackager\_mp4tosmooth, MediaPackager\_SmoothToHLS.xml e MediaEncryptor\_PlayReadyProtection.xml. Mediapackager\_mp4tosmooth e MediaPackager\_SmoothToHLS.xml sono definiti in [set di impostazioni di attività per Azure Media Packager](http://msdn.microsoft.com/library/azure/hh973635.aspx) e MediaEncryptor\_PlayReadyProtection.xml è definito nell’argomento [set di impostazioni di attività per Azure Media Encryptor](http://msdn.microsoft.com/library/azure/hh973610.aspx).
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Xml.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	
	namespace MediaServicesContentProtection
	{
	    class Program
	    {
	        // Paths to support files (within the above base path). You can use 
	        // the provided sample media files from the "SupportFiles" folder, or 
	        // provide paths to your own media files below to run these samples.
	
	        private static readonly string _mediaFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _singleMP4File =
	            Path.Combine(_mediaFiles, @"SingleMP4\BigBuckBunny.mp4");
	
	        // XML Configruation files path.
	        private static readonly string _configurationXMLFiles = @"../..\Configurations";
	
	
	        private static MediaServicesCredentials _cachedCredentials = null;
	        private static CloudMediaContext _context = null;
	
	        // Media Services account information.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServiceAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServiceAccountKey"];
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the chached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            // Load an MP4 file.
	            IAsset asset = IngestSingleMP4File(_singleMP4File, AssetCreationOptions.None);
	
	            // Encode an MP4 file to a set of multibitrate MP4s.
	            // Then, package a set of MP4s to clear Smooth Streaming.
	            IAsset clearSmoothStreamAsset = ConvertMP4ToMultibitrateMP4sToSmoothStreaming(asset);
	
	            // Create a common encryption content key that is used 
	            // a) to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	            //    that is used for encryption.
	            // b) to configure the license delivery service and 
	            //
	            Guid keyId;
	            byte[] contentKey;
	
	            IContentKey key = CreateCommonEncryptionKey(out keyId, out contentKey);
	
	            // The content key authorization policy must be configured by you 
	            // and met by the client in order for the PlayReady license
	            // to be delivered to the client. 
	            // In this example the Media Services PlayReady license delivery service is used.
	            ConfigureLicenseDeliveryService(key);
	
	            // Get the Media Services PlayReady license delivery URL.
	            // This URL will be assigned to the licenseAcquisitionUrl property 
	            // of the MediaEncryptor_PlayReadyProtection.xml file.
	            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
	
	            // Update the MediaEncryptor_PlayReadyProtection.xml file with the key and URL info.
	            UpdatePlayReadyConfigurationXMLFile(keyId, contentKey, acquisitionUrl);
	
	            // Create HLS encrypted with PlayReady.
	            IAsset playReadyHLSAsset = CreateHLSEncryptedWithPlayReady(clearSmoothStreamAsset);
	            //
	            string hlsWithPlayReadyURL = playReadyHLSAsset.GetHlsUri().ToString();
	            Console.WriteLine("HLS with PlayReady URL:");
	            Console.WriteLine(hlsWithPlayReadyURL);
	        }
	
	        /// <summary>
	        /// Creates a job with 2 tasks: 
	        /// 1 task - encodes a single MP4 to multibitrate MP4s,
	        /// 2 task - packages MP4s to Smooth Streaming.
	        /// </summary>
	        /// <returns>The output asset.</returns>
	        public static IAsset ConvertMP4ToMultibitrateMP4sToSmoothStreaming(IAsset asset)
	        {
	            // Create a new job.
	            IJob job = _context.Jobs.Create("Convert MP4 to Smooth Streaming.");
	
	            // Add task 1 - Encode single MP4 into multibitrate MP4s.
	            IAsset MP4sAsset = EncodeSingleMP4IntoMultibitrateMP4sTask(job, asset);
	            // Add task 2 - Package a multibitrate MP4 set to Clear Smooth Streaming.
	            IAsset packagedAsset = PackageMP4ToSmoothStreamingTask(job, MP4sAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Get the output asset that contains Smooth Streaming.
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Create a common encryption content key that is used 
	        /// to set the key values in the MediaEncryptor_PlayReadyProtection.xml file
	        /// that is used for encryption.
	        /// </summary>
	        /// <param name="keyId"></param>
	        /// <param name="contentKey"></param>
	        /// <returns></returns>
	        public static IContentKey CreateCommonEncryptionKey(out Guid keyId, out byte[] contentKey)
	        {
	            keyId = Guid.NewGuid();
	            contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	        /// <summary>
	        /// Update your configuration .xml file dynamically.
	        /// </summary>
	        public static void UpdatePlayReadyConfigurationXMLFile(Guid keyId, byte[] keyValue, Uri licenseAcquisitionUrl)
	        {
	            string xmlFileName = Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml");
	
	            XNamespace xmlns = "http://schemas.microsoft.com/iis/media/v4/TM/TaskDefinition#";
	
	            // Prepare the encryption task template
	            XDocument doc = XDocument.Load(xmlFileName);
	
	            var licenseAcquisitionUrlEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "licenseAcquisitionUrl")
	                    .FirstOrDefault();
	            var contentKeyEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "contentKey")
	                    .FirstOrDefault();
	            var keyIdEl = doc
	                    .Descendants(xmlns + "property")
	                    .Where(p => p.Attribute("name").Value == "keyId")
	                    .FirstOrDefault();
	
	            // Update the "value" property.
	            if (licenseAcquisitionUrlEl != null)
	                licenseAcquisitionUrlEl.Attribute("value").SetValue(licenseAcquisitionUrl.ToString());
	
	            if (contentKeyEl != null)
	                contentKeyEl.Attribute("value").SetValue(Convert.ToBase64String(keyValue));
	
	            if (keyIdEl != null)
	                keyIdEl.Attribute("value").SetValue(keyId);
	
	            doc.Save(xmlFileName);
	        }
	
	        /// <summary>
	        // Encrypts clear Smooth Streaming to Smooth Streaming with PlayReady.
	        // Then, packages the PlayReady Smooth Streaming to HLS with PlayReady.
	        /// </summary>
	        /// <param name="clearSmoothAsset">Asset that contains clear Smooth Streaming.</param>
	        /// <returns>The output asset.</returns>
	        public static IAsset CreateHLSEncryptedWithPlayReady(IAsset clearSmoothStreamAsset)
	        {
	            IJob job = _context.Jobs.Create("Encrypt to HLS with PlayReady.");
	
	            // Add task 1 - Encrypt Smooth Streaming with PlayReady 
	            IAsset encryptedSmoothAsset =
	                EncryptSmoothStreamWithPlayReadyTask(job, clearSmoothStreamAsset);
	
	            // Add task 2 - Package to HLS with PlayReady.
	            PackageSmoothStreamToHLS(job, encryptedSmoothAsset);
	
	            // Submit the job and wait until it is completed.
	            job.Submit();
	            job = job.StartExecutionProgressTask(
	                j =>
	                {
	                    Console.WriteLine("Job state: {0}", j.State);
	                    Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	                },
	                CancellationToken.None).Result;
	
	            // Since we had two tasks, the OutputMediaAssets[1]
	            // contains the desired asset.
	            _context.Locators.Create(
	                LocatorType.OnDemandOrigin,
	                job.OutputMediaAssets[1],
	                AccessPermissions.Read,
	                TimeSpan.FromDays(30));
	
	            return job.OutputMediaAssets[1];
	        }
	
	        /// <summary>
	        /// Uploads a single file.
	        /// </summary>
	        /// <param name="fileDir">The location of the files.</param>
	        /// <param name="assetCreationOptions">
	        ///  You can specify the following encryption options for the AssetCreationOptions.
	        ///      None:  no encryption.  
	        ///      StorageEncrypted: storage encryption. Encrypts a clear input file 
	        ///        before it is uploaded to Azure storage. 
	        ///      CommonEncryptionProtected: for Common Encryption Protected (CENC) files. 
	        ///        For example, a set of files that are already PlayReady encrypted. 
	        ///      EnvelopeEncryptionProtected: for HLS with AES encryption files.
	        ///        NOTE: The files must have been encoded and encrypted by Transform Manager. 
	        ///     </param>
	        /// <returns>Returns an asset that contains a single file.</returns>
	        /// </summary>
	        /// <returns></returns>
	        private static IAsset IngestSingleMP4File(string fileDir, AssetCreationOptions assetCreationOptions)
	        {
	            // Use the SDK extension method to create a new asset by 
	            // uploading a mezzanine file from a local path.
	            IAsset asset = _context.Assets.CreateFromFile(
	                fileDir,
	                assetCreationOptions,
	                (af, p) =>
	                {
	                    Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	                });
	
	
	            return asset;
	
	        }
	        /// <summary>
	        /// Creates a task to encode to Adaptive Bitrate. 
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncodeSingleMP4IntoMultibitrateMP4sTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encoder.
	            IMediaProcessor encoder = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.AzureMediaEncoder);
	
	            ITask adpativeBitrateTask = job.Tasks.AddNew("MP4 to Adaptive Bitrate Task",
	               encoder,
	               "H264 Adaptive Bitrate MP4 Set 720p",
	               TaskOptions.None);
	
	            // Specify the input Asset
	            adpativeBitrateTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset abrAsset = adpativeBitrateTask.OutputAssets.AddNew("Multibitrate MP4s",
	                                    AssetCreationOptions.None);
	
	            return abrAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to convert the MP4 file(s) to a Smooth Streaming asset.
	        /// Adds the new task to a job.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset PackageMP4ToSmoothStreamingTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor packager = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Azure Media Packager does not accept string presets, so load xml configuration
	            string smoothConfig = File.ReadAllText(Path.Combine(
	                        _configurationXMLFiles,
	                        "MediaPackager_MP4toSmooth.xml"));
	
	            // Create a new Task to convert adaptive bitrate to Smooth Streaming.
	            ITask smoothStreamingTask = job.Tasks.AddNew("MP4 to Smooth Task",
	               packager,
	               smoothConfig,
	               TaskOptions.None);
	
	            // Specify the input Asset, which is the output Asset from the first task
	            smoothStreamingTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.None, which 
	            // means the output asset is in the clear (unencrypted).
	            IAsset smoothOutputAsset =
	                smoothStreamingTask.OutputAssets.AddNew("Clear Smooth Streaming",
	                    AssetCreationOptions.None);
	
	            return smoothOutputAsset;
	        }
	
	
	        /// <summary>
	        /// Converts Smooth Stream to HLS.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The Smooth Stream asset.</param>
	        /// <returns>The asset that was packaged to HLS.</returns>
	        private static IAsset PackageSmoothStreamToHLS(IJob job, IAsset smoothStreamAsset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Packager.
	            IMediaProcessor processor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaPackager);
	
	            // Read the configuration data into a string. 
	            //
	            string configuration = File.ReadAllText(
	                        Path.Combine(_configurationXMLFiles,
	                                    @"MediaPackager_SmoothToHLS.xml"));
	
	            // Create a task with the encoding details, using a configuration file.
	            ITask task = job.Tasks.AddNew("My Smooth to HLS Task",
	               processor,
	               configuration,
	               TaskOptions.ProtectedConfiguration);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(smoothStreamAsset);
	
	            // Add an output asset to contain the results of the job. 
	            IAsset outputAsset =
	                task.OutputAssets.AddNew("HLS asset", AssetCreationOptions.None);
	
	
	            return outputAsset;
	        }
	
	        /// <summary>
	        /// Creates a task to encrypt Smooth Streaming with PlayReady.
	        /// Note: Do deliver DASH, make sure to set the useSencBox and adjustSubSamples 
	        /// configuration properties to true.
	        /// </summary>
	        /// <param name="job">The job to which to add the new task.</param>
	        /// <param name="asset">The input asset.</param>
	        /// <returns>The output asset.</returns>
	        private static IAsset EncryptSmoothStreamWithPlayReadyTask(IJob job, IAsset asset)
	        {
	            // Get the SDK extension method to  get a reference to the Azure Media Encryptor.
	            IMediaProcessor playreadyProcessor = _context.MediaProcessors.GetLatestMediaProcessorByName(
	                MediaProcessorNames.WindowsAzureMediaEncryptor);
	
	            // Read the configuration XML.
	            //
	            // Note that the configuration defined in MediaEncryptor_PlayReadyProtection.xml
	            // is using keySeedValue. It is recommended that you do this only for testing 
	            // and not in production. For more information, see 
	            // http://msdn.microsoft.com/library/windowsazure/dn189154.aspx.
	            //
	            string configPlayReady = File.ReadAllText(Path.Combine(_configurationXMLFiles,
	                                        @"MediaEncryptor_PlayReadyProtection.xml"));
	
	            ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
	               playreadyProcessor,
	               configPlayReady,
	               TaskOptions.ProtectedConfiguration);
	
	            playreadyTask.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job. 
	            // This output is specified as AssetCreationOptions.CommonEncryptionProtected.
	            IAsset playreadyAsset = playreadyTask.OutputAssets.AddNew(
	                                            "PlayReady Smooth Streaming",
	                                            AssetCreationOptions.CommonEncryptionProtected);
	
	
	            return playreadyAsset;
	        }
	
	
	        /// <summary>
	        /// Configures authorization policy for the content key. 
	        /// </summary>
	        /// <param name="contentKey">The content key.</param>
	        static public void ConfigureLicenseDeliveryService(IContentKey contentKey)
	        {
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction 
	                { 
	                    Name = "Open", 
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady license template.
	            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption policyOption =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, newLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	
	    }
	}

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->