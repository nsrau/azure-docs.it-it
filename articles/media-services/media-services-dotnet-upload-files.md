<properties 
	pageTitle="Caricamento di file in un account di Servizi multimediali mediante .NET" 
	description="Informazioni su come ottenere contenuti multimediali in Servizi multimediali creando e caricando asset." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="07/18/2016" 
	ms.author="juliako"/>



#Caricamento di file in un account di Servizi multimediali mediante .NET

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

In Servizi multimediali i file digitali vengono caricati (o inseriti) in un asset. L'entità **Asset** può contenere video, audio, immagini, raccolte di anteprime, tracce di testo e file di sottotitoli codificati (e anche i metadati relativi a questi file). Dopo aver caricato i file, i contenuti vengono archiviati in modo sicuro nel cloud per altre operazioni di elaborazione e streaming.

I file nell'asset sono denominati **File di asset**. L'istanza di **AssetFile** e il file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contiene metadati relativi al file multimediale, mentre quest'ultimo contiene l'effettivo contenuto multimediale.

Quando si creano asset, è possibile specificare le seguenti opzioni di crittografia.

- **None**: non viene usata alcuna crittografia. Si tratta del valore predefinito. Quando si usa questa opzione, il contenuto non è protetto durante il transito, né nell'archiviazione locale. Se si pianifica la distribuzione di un file MP4 con il download progressivo, usare questa opzione.
- **CommonEncryption**: usare questa opzione per caricare contenuti già crittografati e protetti con Common Encryption o PlayReady DRM (ad esempio, Smooth Streaming protetto con PlayReady DRM).
- **EnvelopeEncrypted**: usare questa opzione se si sta caricando contenuto HLS crittografato con AES. I file devono essere stati codificati e crittografati da Transform Manager.
- **StorageEncrypted**: crittografa il contenuto non crittografato localmente usando la crittografia AES a 256 bit, quindi li carica in Archiviazione di Azure dove vengono archiviati con crittografia in modo inattivo. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica, quindi ricrittografati facoltativamente prima di essere ricaricati di nuovo come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.

	Servizi multimediali offre una crittografia di archiviazione su disco per gli asset, non in rete come Digital Rights Management (DRM).

	Se l'asset è protetto con crittografia di archiviazione, è necessario configurare i criteri di distribuzione degli asset. Per altre informazioni, vedere [Procedura: Configurare i criteri di distribuzione degli asset](media-services-dotnet-configure-asset-delivery-policy.md).

Se si specifica che l'asset deve essere crittografato con un'opzione **CommonEncrypted** o **EnvelopeEncypted**, sarà necessario associare l'asset a **ContentKey**. Per alte informazioni, vedere [Creare entità ContentKey mediante .NET](media-services-dotnet-create-contentkey.md).

Se si specifica che l'asset deve essere crittografato con un'opzione **StorageEncrypted**, Media Services SDK per .NET creerà un'entità **StorateEncrypted** **ContentKey** per l'asset.

>[AZURE.NOTE]Servizi multimediali usa il valore della proprietà IAssetFile.Name durante la creazione degli URL per i contenuti in streaming, ad esempio http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.. Per questo motivo, la codifica percentuale non è consentita. Il valore della proprietà **Name** non può contenere i [caratteri riservati per la codifica percentuale](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) seguenti: !*'();:@&=+$,/?%#". L'estensione del nome di file, inoltre, può essere preceduta da un solo punto (.).

Questo argomento illustra come usare l'SDK di Servizi multimediali per .NET e le relative estensioni per caricare file in un asset di Servizi multimediali.

 
## Caricare un singolo file con SDK di Servizi multimediali per .NET 

Nel seguente codice di esempio viene usato l'SDK per .NET per eseguire le seguenti attività:

- Creare un asset vuoto.
- Creare un'istanza di AssetFile da associare all'asset.
- Creare un'istanza di AccessPolicy che definisce le autorizzazioni e la durata dell'accesso all'asset.
- Creare un'istanza di Locator che consente l'accesso all'asset.
- Caricare in Servizi multimediali un singolo file multimediale.

		
		static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
		{
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions); 

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            var policy = _context.AccessPolicies.Create(
                                    assetName,
                                    TimeSpan.FromDays(30),
                                    AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            locator.Delete();
            policy.Delete();

            return inputAsset;
		}

##Caricare più file con SDK di Servizi multimediali per .NET 

Il seguente codice illustra come creare un asset e caricare più file.

Il codice esegue le seguenti attività:
	
- 	Crea un'entità Asset vuota usando il metodo CreateEmptyAsset definito nel passaggio precedente.
 	
- 	Crea un'istanza di **AccessPolicy** che definisce le autorizzazioni e la durata dell'accesso all'asset.
 	
- 	Crea un'istanza di **Locator** che consente l'accesso all'asset.
 	
- 	Crea un'istanza di **BlobTransferClient**. Questo tipo rappresenta un client che agisce sugli oggetti BLOB di Azure. In questo esempio viene usato il client per monitorare lo stato del caricamento.
 	
- 	Enumera i file della directory specificata e crea un'istanza di **AssetFile** per ogni file.
 	
- 	Carica i file in Servizi multimediali usando il metodo **UploadAsync**.
 	
>[AZURE.NOTE] Usare il metodo UploadAsync in modo che non si verifichino blocchi delle chiamate e i file vengano caricati in parallelo.
 	
 	
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AccestFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }
	
	static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
	{
	    if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
	    {
	        Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
	    }
	}



Quando si carica un numero elevato di asset, prendere in considerazione quanto segue.

- Creare un nuovo oggetto **CloudMediaContext** per thread. La classe **CloudMediaContext** non è di tipo thread-safe.
 
- Aumentare il valore di NumberOfConcurrentTransfers sostituendo il valore 2 predefinito con uno maggiore, ad esempio 5. L'impostazione di questa proprietà ha effetto su tutte le istanze di **CloudMediaContext**.
 
- Mantenere su 10 l'impostazione predefinita di ParallelTransferThreadCount.
 
##<a id="ingest_in_bulk"></a>Inserimento di asset in blocco tramite SDK di Servizi multimediali per .NET 

Il caricamento di file di asset di grandi dimensioni costituisce un collo di bottiglia nella creazione di asset. L'inserimento di asset in blocco prevede il disaccoppiamento del processo di creazione di asset da quello di caricamento. Per usare un approccio di inserimento in blocco, creare un manifesto (IngestManifest) in cui vengono descritti l'asset e i relativi file associati. Usare quindi il metodo di caricamento che si preferisce per caricare i file associati nel contenitore BLOB del manifesto. Servizi multimediali di Microsoft Azure controlla il contenitore BLOB associato al manifesto. Dopo che un file è stato caricato nel contenitore BLOB, Servizi multimediali di Microsoft Azure completa la creazione dell'asset in base alla configurazione dell'asset nel manifesto (IngestManifestAsset).


Per creare una nuova entità IngestManifest, chiamare il metodo Create esposto dalla raccolta IngestManifests in CloudMediaContext. Questo metodo creerà una nuova entità IngestManifest con il nome di manifesto specificato.

	IIngestManifest manifest = context.IngestManifests.Create(name);

Creare gli asset che verranno associati all'entità IngestManifest in blocco. Configurare le opzioni di crittografia desiderate nell'asset per l'inserimento in blocco.

	// Create the assets that will be associated with this bulk ingest manifest
	IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
	IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Un'entità IngestManifestAsset associa un Asset a un IngestManifest in blocco per l'inserimento in blocco. Associa anche le entità AssetFiles che costituiranno i singoli Asset. Per creare un'entità IngestManifestAsset, usare il metodo Create nel contesto server.

Il seguente esempio illustra come aggiungere due nuove entità IngestManifestAssets che associano i due asset creati in precedenza al manifesto di inserimento in blocco. Ogni entità IngestManifestAsset associa anche un set di file che verrà caricato per ogni asset durante l'inserimento in blocco.

	string filename1 = _singleInputMp4Path;
	string filename2 = _primaryFilePath;
	string filename3 = _singleInputFilePath;
	
	IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
	IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
	
È possibile usare qualsiasi applicazione client ad alta velocità in grado di caricare i file di asset nell'URI del contenitore di archiviazione BLOB fornito dalla proprietà **IIngestManifest.BlobStorageUriForUpload** di IngestManifest. Un servizio di caricamento ad alta velocità consigliato è l'applicazione [Aspera On Demand for Azure](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). È anche possibile scrivere codice per il caricamento dei file di asset, come descritto nel seguente esempio di codice.
	
	static void UploadBlobFile(string destBlobURI, string filename)
	{
	    Task copytask = new Task(() =>
	    {
	        var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
	        CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
	        CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);
	
	        string[] splitfilename = filename.Split('\\');
	        var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);
	
	        using (var stream = System.IO.File.OpenRead(filename))
	            blob.UploadFromStream(stream);
	
	        lock (consoleWriteLock)
	        {
	            Console.WriteLine("Upload for {0} completed.", filename);
	        }
	    });
	
	    copytask.Start();
	}

Il codice per il caricamento dei file di asset per l'esempio usato in questo argomento è riportato nel seguente esempio.
	
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
	UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
	

È possibile determinare lo stato dell'inserimento in blocco per tutti gli asset associati a un'entità **IngestManifest** eseguendo il polling della proprietà Statistics di **IngestManifest**. Per aggiornare le informazioni sullo stato, è necessario usare una nuova entità **CloudMediaContext** ogni volta che si esegue il polling della proprietà Statistics.

Il seguente esempio illustra il polling di un'entità IngestManifest in base alla relativa proprietà **Id**.
	
	static void MonitorBulkManifest(string manifestID)
	{
	   bool bContinue = true;
	   while (bContinue)
	   {
	      CloudMediaContext context = GetContext();
	      IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();
	
	      if (manifest != null)
	      {
	         lock(consoleWriteLock)
	         {
	            Console.WriteLine("\nWaiting on all file uploads.");
	            Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
	            Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
	            Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);
	
	            if (manifest.Statistics.PendingFilesCount == 0)
	            {
	               Console.WriteLine("Completed\n");
	               bContinue = false;
	            }
	         }
	
	         if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
	            Thread.Sleep(60000);
	      }
	      else // Manifest is null
	         bContinue = false;
	   }
	}
	


##Caricare file mediante le estensioni dell'SDK per .NET 

Il seguente esempio mostra come caricare un singolo file mediante le estensioni dell'SDK per .NET. In questo caso viene usato il metodo **CreateFromFile**, ma è disponibile anche la versione asincrona (**CreateFromFileAsync**). Il metodo **CreateFromFile** consente di specificare il nome del file, l'opzione di crittografia e un callback per segnalare lo stato di caricamento del file.


	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}

Il seguente esempio esegue una chiamata alla funzione UploadFile e specifica la crittografia di archiviazione come opzione di creazione dell'asset.


	var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Passaggi successivi
Dopo avere caricato un asset in Servizi multimediali, è possibile passare all'argomento [Procedura: Ottenere un'istanza del processore di contenuti multimediali][].

[Procedura: Ottenere un'istanza del processore di contenuti multimediali]: media-services-get-media-processor.md
 

<!---HONumber=AcomDC_0720_2016-->