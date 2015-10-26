<properties 
	pageTitle="Modificare le attività di codifica personalizzando i set di impostazioni delle attività" 
	description="Il codificatore di Servizi multimediali di Azure consente di passare i file dei set di impostazioni personalizzati al Codificatore multimediale di Azure. Questo argomento descrive come personalizzare i file dei set di impostazioni per eseguire le attività seguenti: sovrapporre un'immagine a un video esistente, controllare i nomi file di output generati dal codificatore, unire i video." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Modificare le attività di codifica personalizzando i set di impostazioni delle attività 

Il codificatore di Servizi multimediali di Azure consente di passare i file dei set di impostazioni personalizzati al Codificatore multimediale di Azure. Questo argomento mostra come personalizzare i file dei set di impostazioni per eseguire le attività seguenti:

- sovrapporre un'immagine a un video esistente 
- controllare i nomi file di output generati dal codificatore 
- unire i video
- codificare le presentazioni con prevalenza del parlato

##Controllo dei nomi file di output del Codificatore multimediale di Azure 

Per impostazione predefinita, Codificatore multimediale di Azure crea nomi di file di output combinando diversi attributi dell'asset di input e al processo di codifica. Ogni attributo viene identificato mediante una macro come descritto di seguito.

Di seguito è riportato un elenco completo delle macro disponibili per la denominazione dei file di output: Velocità in bit audio: velocità in bit usata per la codifica audio, specificata in kbps

- Codec audio: codec usato per la codifica audio; i valori consentiti sono AAC, WMA e DDP
- Conteggio canali: numero di canali audio codificati; i valori consentiti sono: 1, 2 o 6
- Estensione predefinita: estensione del file predefinita 
- Lingua: codice della lingua BCP-47 che rappresenta la lingua usata nel file audio. Il valore predefinito per questa impostazione è "und". 
- Nome file originale: nome del file caricato in Archiviazione di Azure
- StreamId – StreamId: ID flusso come definito dall'attributo streamID dell'elemento <StreamInfo> nel file del set di impostazioni 
- Codec video: codec usato per la codifica video; i valori consentiti sono H264 e VC1
- Velocità in bit video: velocità in bit usata per la codifica video, specificata in kbps

Queste macro possono essere combinate in qualsiasi permutazione per controllare il nome dei file generati dal Codificatore di Servizi multimediali. Ad esempio, la convenzione di denominazione predefinita è:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

La convenzione di denominazione dei file viene specificata mediante l'attributo DefaultMediaOutputFileName dell'elemento [Set di impostazioni](https://msdn.microsoft.com/library/azure/dn554334.aspx). Ad esempio:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Il codificatore inserisce caratteri di sottolineatura tra una macro e l'altra, ad esempio la configurazione sopra darebbe come risultato un nome file simile al seguente: MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4.


##Creazione di sovrimpressioni

Il codificatore di Servizi multimediali di Azure consente di sovrapporre un'immagine (jpg, bmp, gif, tif), un video o una traccia audio (*.wma, *.mp3, *.wav) a un video esistente. Questa funzionalità è simile a quella di Expression Encoder 4 (Service Pack 2).

###Sovrimpressioni con il codificatore di Servizi multimediali

È possibile specificare quando la sovrimpressione verrà presentata, per quanto tempo la sovrimpressione verrà presentata e, per le sovrimpressioni immagine/video, in quale punto dello schermo verrà visualizzata la sovrimpressione. È inoltre possibile impostare la dissolvenza in entrata e/o la dissolvenza in uscita delle sovrimpressioni. I file audio/video da sovrapporre possono essere contenuti in più asset o in un solo asset. Le sovrimpressioni vengono controllate dal file XML del set di impostazioni che viene passato al codificatore. Per una descrizione completa dello schema dei set di impostazioni, vedere gli schemi del Codificatore multimediale di Azure. Le sovrimpressioni vengono specificate nell'elemento <MediaFile>, come nel frammento di set di impostazioni seguente:

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Set di impostazioni per sovrimpressioni video o immagine

Le sovrimpressioni possono derivare da uno o più asset. Quando si creano sovrimpressioni video usando più asset, il nome file della sovrimpressione viene specificato nell'attributo OverlayFileName mediante la sintassi %n% dove n è l'indice in base zero degli asset di input per l'attività di codifica. Quando si creano sovrimpressioni video con un solo asset, il nome file della sovrimpressione viene specificato direttamente nell'attributo OverlayFileName, come nei frammenti di set di impostazioni seguenti:

Esempio 1:

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Esempio 2

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

La posizione e la dimensione della sovrimpressione video vengono controllate dall'attributo OverlayRect. Il contenuto che deve essere sovrapposto verrà ridimensionato in base a questo rettangolo. L'opacità viene controllata dall'attributo OverlayOpacity. I valori validi sono 0,0-1,0 dove 1,0 indica il 100% di opacità. La sovrimpressione verrà visualizzata all'ora specificata dall'attributo OverlayStartTime e terminerà all'ora specificata dall'attributo OverlayEndTime. Sia OverlayStartTime che OverlayEndTime devono rientrare nella sequenza temporale del video di origine. Per altre informazioni su ciascun attributo specifico della sovrimpressione, vedere gli schemi del Codificatore multimediale di Azure.

###Set di impostazioni per le sovrimpressioni audio

Le sovrimpressioni audio possono essere, ad esempio, qualsiasi formato di file audio supportato. Per un elenco completo dei formati di file audio supportati, vedere i formati supportati dal codificatore di Servizi multimediali. Anche le sovrimpressioni audio vengono specificate nell'elemento <MediaFile>, come nel frammento di set di impostazioni seguente:

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Per le sovrimpressioni audio archiviate in più asset, il nome file della sovrimpressione audio viene specificato nell'attributo AudioOverlayFileName mediante la sintassi %n% dove n è l'indice in base zero della raccolta di asset di input per l'attività di codifica. Per le sovrimpressioni audio archiviate in un solo asset, il nome file della sovrimpressione viene specificato direttamente nell'attributo AudioOverlayFileName. AudioOverlayLayoutMode determina quando verrà presentata la sovrimpressione audio. Se viene impostato su "WholeSequence", la traccia audio verrà presentata per l'intera durata del video. Se viene impostato su "Custom", gli attributi AudioOverlayStartTime e AudioOverlayEndTime determinano quando la sovrimpressione audio inizia e finisce. Sia OverlayStartTime che OverlayEndTime devono rientrare nella sequenza temporale del video di origine. Per altre informazioni su tutti gli attributi della sovrimpressione audio, vedere gli schemi del Codificatore multimediale di Azure. Le sovrimpressioni audio possono essere combinate con le sovrimpressioni video, come nel frammento di set di impostazioni seguente:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Invio di attività con le sovrimpressioni

Dopo avere creato un file di set di impostazioni, è necessario eseguire le operazioni seguenti:

- Caricare gli asset
- Caricare la configurazione del set di impostazioni. Nota: il codice riportato di seguito presuppone il set di impostazioni precedente.
- Creare un processo
- Ottenere un riferimento al codificatore di Servizi multimediali
- Creare un'attività specificando la raccolta di asset di input, la configurazione del set di impostazioni, il codificatore multimediale e l'asset di output
- Inviare il processo

Il frammento di codice seguente illustra come effettuare questi passaggi:

	static public void CreateOverlayJob()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);


		// Upload assets to overlay
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
		
		// Load the preset configuration
		string presetFileName = "OverlayPreset.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
	}



>[AZURE.NOTE]Questo frammento carica ogni asset in sequenza per motivi di semplicità. Negli ambienti di produzione gli asset vengono normalmente caricati in blocco. Per altre informazioni sul caricamento di più asset in blocco, vedere [Inserimento di asset in blocco con Media Services SDK for .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Per il codice di esempio completo, vedere [Creazione di sovrimpressioni con il codificatore di Servizi multimediali](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Condizioni di errore

Quando si modifica la stringa del set di impostazioni, è necessario verificare quanto segue:

- Per le sovrimpressioni video/immagine, il rettangolo della sovrimpressione deve corrispondere esattamente alle dimensioni del video di origine
- L'ora di inizio e di fine delle sovrimpressioni deve rientrare nella sequenza temporale del video di origine
- Se il file XML del set di impostazioni contiene un riferimento a?OverlayFileName="%n%", la raccolta InputAssets per le attività deve contenere almeno n+1 asset



##Unione dei segmenti video

Il codificatore di Servizi multimediali fornisce il supporto per unire un set di video. I video possono essere uniti in modalità end-to-end oppure è possibile specificare parti di uno o di entrambi i video da unire. Il risultato dell'unione è un singolo asset di output che contiene il video specificato dagli asset di input. I video da unire possono essere contenuti in più asset o in un solo asset. L'unione viene controllata dal file XML del set di impostazioni che viene passato al codificatore. Per una descrizione completa dello schema dei set di impostazioni, vedere [Schema del Codificatore multimediale di Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###Unione con il codificatore di Servizi multimediali

L'unione viene controllata nell'elemento <MediaFile>, come nel set di impostazioni seguente:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Per ogni video da unire, viene aggiunto un elemento <Source> all'elemento <Sources>. Ogni elemento <Source> contiene un elemento <Clips>. Ogni elemento <Clips> contiene uno o più elementi <Clip> che specificano quanta parte del video verrà unita nell'asset di output, specificando un'ora di inizio e di fine. L'elemento <Source> fa riferimento all'asset su cui agisce. Il formato del riferimento dipende dal fatto che i video da unire siano in asset separati o in un solo asset. Per unire un intero video, è sufficiente omettere l'elemento <Clips>.

###Unione di video da più asset

Quando si uniscono video da più asset, viene usato un indice in base zero per consentire all'attributo MediaFile dell'elemento <Source> di identificare l'asset a cui corrisponde l'elemento <Source>. L'indice in base zero non viene specificato, l'elemento <Source> che non specifica un attributo MediaFile fa riferimento al primo asset di input. Tutti gli altri elementi <Source> devono specificare l'indice in base zero dell'asset di input a cui fanno riferimento usando la sintassi %n% dove n è l'indice in base zero dell'asset di input. Nell'esempio precedente, il primo elemento <Source> specifica il primo asset di input, il secondo elemento <Source> specifica il secondo asset di input e così via. Non è necessario fare riferimento agli asset di input in ordine, ad esempio:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Questo esempio unisce parti del secondo, del primo e del terzo asset di input. Si noti che, poiché a ogni video fa riferimento un indice in base zero, è possibile unire due video che hanno lo stesso nome. Dopo avere creato un file di set di impostazioni, è necessario eseguire le operazioni seguenti:
 
- Caricare gli asset
- Caricare la configurazione del set di impostazioni
- Creare un processo
- Ottenere un riferimento al codificatore di Servizi multimediali
- Creare un'attività specificando gli asset di input, la configurazione del set di impostazioni, il codificatore multimediale e l'asset di output
- Inviare il processo

Il frammento di codice seguente illustra come effettuare questi passaggi:
	
	static public void StitchWithMultipleAssets()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
		
		// Upload assets to stitch
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
		
		// Load the preset configuration
		string presetFileName = "StitchingWithMultipleAssets.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
		} 


Questo frammento carica ogni asset in sequenza per motivi di semplicità. Negli ambienti di produzione gli asset vengono normalmente caricati in blocco. Per altre informazioni sul caricamento di più asset in blocco, vedere [Inserimento di asset in blocco con Media Services SDK for .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Per il codice di esempio completo, vedere [Unione con il codificatore di Servizi multimediali](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###Unione di video da un solo asset

Quando si uniscono video in un singolo asset, ogni video deve avere un nome univoco. I video sono specificati dal valore relativo al nome file dell'attributo MediaFile. Ad esempio:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Questo set di impostazioni unisce parti di video1.mp4, video2.wmv e video3.wmv nell'asset di output. Il processo e le attività vengono creati esattamente come per l'unione di video a partire da più asset, ma in questo caso è sufficiente caricare un singolo asset, come mostrato nel codice seguente:

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Codifica di presentazioni o flussi audio con prevalenza del parlato
 
Quando si codificano video contenenti tracce audio con prevalenza del parlato, i set di impostazioni predefiniti del codificatore possono causare l'amplificazione del rumore di fondo nell'asset codificato. Questo comportamento è determinato dall'impostazione su true dell'attributo NormalizeAudio.

###Codificare le presentazioni con prevalenza del parlato

Per impedire l'amplificazione del rumore di fondo, effettuare le seguenti operazioni:

1. Copiare in un file XML il contenuto del set di impostazioni del codificatore in uso. I set di impostazioni del codificatore sono disponibili in: Schemi del Codificatore multimediale di Azure
1. Eliminare l'attributo NormalizeAudio, che si trova vicino alla parte superiore del file del set di impostazioni sotto l'elemento <MediaFile>:
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Salvare il file del set di impostazioni modificato nell'unità disco rigido locale e usare codice simile al seguente per eseguire la codifica con il set di impostazioni personalizzato:
		
		// Upload file and create asset
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
		 
		string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
		string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
		 
		IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
		
		Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
		
		// Get a media processor reference, and pass to it the name of the processor to use for the specific task.
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
		 
		// Read the configuration data into a string. 
		string configuration = File.ReadAllText(inputPresetFile);
		 
		// Create a task with the encoding details, using a string preset.
		ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
		 
		// Specify the input asset to be encoded.
		task.InputAssets.Add(asset);
		 
		// Add an output asset to contain the results of the job.
		task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
		 
		// Launch the job. 
		job.Submit();

##Percorsi di formazione di Media Services

È possibile visualizzare i percorsi di apprendimento AMS qui:

- [Flusso di lavoro AMS Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flusso di lavoro AMS Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Vedere anche

[Schema XML del Codificatore multimediale di Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=Oct15_HO3-->