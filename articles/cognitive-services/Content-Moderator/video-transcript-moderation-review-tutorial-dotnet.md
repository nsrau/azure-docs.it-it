---
title: Content Moderator di Azure - Moderare video e trascrizioni in .NET | Microsoft Docs
description: Come usare Content Moderator per moderare video e trascrizioni in .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: a084b50e44fe26ba2547d0f7b7ed184fb71b190c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "35374260"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Esercitazione sulla moderazione di video e trascrizioni

Le API video di Content Moderator consentono di moderare video e di creare revisioni di video nello strumento di revisione umana. 

Questa esercitazione dettagliata aiuta a comprendere come creare una soluzione completa per la moderazione di video e trascrizioni con moderazione basata su Machine Learning e la creazione di revisioni con intervento umano.

Scaricare l'[applicazione console C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) per questa esercitazione. L'applicazione console usa l'SDK e i pacchetti correlati per eseguire queste attività:

- Comprimere i video di input per un'elaborazione più veloce
- Moderare il video per ottenere immagini e fotogrammi con informazioni dettagliate
- Usare i timestamp dei fotogrammi per creare (immagini)
- Inviare timestamp e anteprime per creare le revisioni del video
- Convertire il riconoscimento vocale video in testo (trascrizione) con l'API Media Indexer
- Moderato la trascrizione con il servizio di moderazione del testo
- Aggiungere la trascrizione moderata alla revisione del video

## <a name="sample-program-outputs"></a>Output di esempio del programma

Prima di procedere, esaminiamo gli output di esempio del programma seguenti:

- [Output della console](#program-output)
- [Revisione del video](#video-review-default-view)
- [Visualizzazione della trascrizione](#video-review-transcript-view)

## <a name="prerequisites"></a>Prerequisiti

1. Eseguire la registrazione al sito Web dello [strumento di revisione Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e [creare tag personalizzati](Review-Tool-User-Guide/tags.md) che l'applicazione console C# assegna all'interno del codice. La schermata seguente illustra i tag personalizzati.

  ![Tag personalizzati per la moderazione dei video](images/video-tutorial-custom-tags.png)

1. Per eseguire l'applicazione di esempio, è necessario un account Azure e un account di Servizi multimediali di Azure. È necessario anche disporre dell'accesso all'anteprima privata di Content Moderator. Sono necessarie infine le credenziali di autenticazione di Azure Active Directory. Per informazioni dettagliate su come ottenere queste informazioni, vedere l[Avvio rapido per all'API di moderazione video](video-moderation-api.md).

1. Modificare il file `App.config` e aggiungere il nome del tenant di Active Directory, gli endpoint del servizio e le chiavi di sottoscrizione indicate da `#####`. Sono necessarie le informazioni seguenti:

|Chiave|Descrizione|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Endpoint per l'API Servizi multimediali di Azure (AMS)|
|`ClientSecret`|Chiave di sottoscrizione per Servizi multimediali di Azure|
|`ClientId`|ID client per Servizi multimediali di Azure|
|`AzureAdTenantName`|Nome del tenant di Active Directory che rappresenta l'organizzazione|
|`ContentModeratorReviewApiSubscriptionKey`|Chiave di sottoscrizione per l'API di revisione di Content Moderator|
|`ContentModeratorApiEndpoint`|Endpoint per l'API Content Moderator|
|`ContentModeratorTeamId`|ID team di Content Moderator|

## <a name="getting-started"></a>Introduzione

La classe `Program` in `Program.cs` è il punto di ingresso principale per l'applicazione di moderazione di video.

### <a name="methods-of-class-program"></a>Metodi della classe Program

|Metodo|Descrizione|
|-|-|
|`Main`|Analizza la riga di comando, raccoglie l'input utente e inizia l'elaborazione.|
|`ProcessVideo`|Comprime, carica, modera e crea revisioni video.|
|`CreateVideoStreamingRequest`|Crea un flusso per caricare un video.|
|`GetUserInputs`|Raccoglie l'input utente e viene usato quando non è presente alcuna opzione della riga di comando.|
|`Initialize`|Inizializza gli oggetti necessari per il processo di moderazione.|

### <a name="the-main-method"></a>Metodo Main

`Main()` è il punto di avvio dell'esecuzione da cui partire per comprendere il processo di moderazione video.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` gestisce gli argomenti della riga di comando seguenti:

- Percorso di una directory contenente file video MPEG-4 da inviare per la moderazione. Tutti i file `*.mp4` in questa directory e nelle relative sottodirectory vengono inviati per la moderazione.
- Facoltativamente, un flag booleano (true/false) che indica se le trascrizioni di testo devono essere generate allo scopo di moderare l'audio.

Se non sono presenti argomenti della riga di comando, `Main()` chiama `GetUserInputs()`. Questo metodo richiede all'utente di immettere il percorso di un singolo file video e di specificare se deve essere generata una trascrizione di testo.

> [!NOTE]
> L'applicazione console usa l'[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) per generare le trascrizioni dalla traccia audio del video caricato. I risultati vengono restituiti in formato WebVTT. Per altre informazioni su questo formato, vedere [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Formato tracce di testo video Web).

### <a name="initialize-and-processvideo-methods"></a>Metodi Initialize e ProcessVideo

Indipendentemente dal fatto che le opzioni del programma provengano dalla riga di comando e dall'input utente interattivo, `Main()` chiama in seguito `Initialize()` per creare le istanze seguenti:

|Classe|Descrizione|
|-|-|
|`AMSComponent`|Comprime i file video prima di inviarli per la moderazione.|
|`AMSconfigurations`|Interfaccia i dati di configurazione dell'applicazione trovati in `App.config`.|
|`VideoModerator`| Carica, codifica, crittografia e modera tramite AMS SDK.|
|`VideoReviewApi`|Gestisce le revisioni video nel servizio Content Moderator.|

Queste classi (a parte `AMSConfigurations`, che è semplice) vengono trattate in modo più dettagliato nelle sezioni successive dell'esercitazione.

I file video, infine, vengono elaborati uno alla volta chiamando il metodo `ProcessVideo()` per ognuno.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


Il metodo `ProcessVideo()` è abbastanza semplice ed esegue nell'ordine le operazioni seguenti:

- Comprime il video
- Carica il video in un asset di Servizi multimediali di Azure
- Crea un processo AMS per moderare il video
- Crea una revisione del video in Content Moderator

Nelle sezioni seguenti vengono trattati più in dettaglio alcuni processi singoli richiamati da `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Compressione del video

Per ridurre al minimo il traffico di rete, l'applicazione converte file video in formato H.264 (AVC MPEG-4) e li dimensiona sulla larghezza massima di 640 pixel. Il codec H.264 è consigliato per l'elevata efficienza (tasso di compressione). La compressione viene eseguita usando lo strumenti da riga comando `ffmpeg` gratuito, incluso nella cartella `Lib` della soluzione Visual Studio. I file di input possono essere di qualsiasi formato supportato da `ffmpeg`, inclusi i codec e formati di file video usati più di frequente.

> [!NOTE]
> Quando si avvia il programma usando le opzioni della riga di comando, specificare una directory che contiene i file video da inviare per la moderazione. Tutti i file della directory che contengono l'estensione `.mp4` vengono elaborati. Per elaborare altre estensioni di nomi file, aggiornare il metodo `Main()` in `Program.cs` per includere le estensioni desiderate.

Il codice che comprime un singolo file video è la classe `AmsComponent` in `AMSComponent.cs`. Il metodo che implementa questa funzionalità è `CompressVideo()`, illustrato di seguito.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Il codice esegue queste operazioni:

- Verifica che configurazione in `App.config` contenga tutti i dati necessari
- Verifica che il file binario `ffmpeg` sia presente
- Compilazione del nome file di output aggiungendo `_c.mp4` al nome di base del file (ad esempio `Example.mp4`  ->  `E>xample_c.mp4`)
- Compilazione di una stringa della riga di comando per eseguire la conversione
- Avvio di un'elaborazione `ffmpeg` dalla riga di comando
- Attesa del video da elaborare

> [!NOTE]
> Se i video sono già compressi tramite H.264 e sono di dimensioni appropriate, è possibile riscrivere `CompressVideo()` per saltare la compressione.

Il metodo restituisce il nome del file di output compresso.

## <a name="uploading-and-moderating-the-video"></a>Caricamento e moderazione del video

Il video deve essere archiviato in Servizi multimediali di Azure prima che possa essere elaborato dal servizio Content Moderation. La classe `Program` in `Program.cs` dispone di un metodo breve `CreateVideoStreamingRequest()` che restituisce un oggetto che rappresenta la richiesta di streaming usata per caricare il video.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

L'oggetto risultante `UploadVideoStreamRequest` viene definito in `UploadVideoStreamRequest.cs` (e il relativo padre `UploadVideoRequest` in `UploadVideoRequest.cs`). Tali classi non vengono visualizzate qui poiché si tratta di classi brevi usate solo per contenere i dati video compressi e le relative informazioni. Un'altra classe solo di dati, ovvero `UploadAssetResult` (`UploadAssetResult.cs`), viene usata per archiviare i risultati del processo di caricamento. Ora è possibile comprendere queste righe in `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Tali righe eseguono queste attività:

- Creazione di un oggetto `UploadVideoStreamRequest` per caricare il video compresso
- Impostazione del flag `GenerateVTT` della richiesta se l'utente ha chiesto una trascrizione di testo
- Chiamata a `CreateAzureMediaServicesJobToModerateVideo()` per eseguire il caricamento e ricevere il risultato

## <a name="deep-dive-into-video-moderation"></a>Approfondimento della moderazione video

Il metodo `CreateAzureMediaServicesJobToModerateVideo()` è in `VideoModerator.cs`, che contiene la maggior parte del codice che interagisce con Servizi multimediali di Azure. Il codice sorgente del metodo è illustrato nell'estratto seguente.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Il codice esegue queste operazioni:

- Creazione di un processo AMS per l'elaborazione da eseguire
- Aggiunta di attività per la codifica del file video, moderazione del file e generazione di una trascrizione del testo
- Invio del processo, caricamento del file e avvio dell'elaborazione
- Recupero dei risultati di moderazione, della trascrizione del testo (se richiesta) e di altre informazioni

## <a name="sample-video-moderation-response"></a>Risposta di moderazione video di esempio

Il risultato del processo di moderazione video (vedere l'[Avvio rapido](video-moderation-api.md)) è una struttura di dati JSON che contiene i risultati della moderazione. Tali risultati includono una suddivisione dei frammenti (immagini) all'interno del video, ognuno contenente eventi (clip) con i fotogrammi chiave contrassegnati per la revisione. A ogni fotogramma chiave viene assegnato un punteggio per la probabilità che contenga contenuto spinto o per adulti. L'esempio seguente mostra una risposta JSON:

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

Una trascrizione dell'audio del video viene anche generata quando è impostato il flag `GenerateVTT`.

> [!NOTE]
> L'applicazione console usa l'[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) per generare le trascrizioni dalla traccia audio del video caricato. I risultati vengono restituiti in formato WebVTT. Per altre informazioni su questo formato, vedere [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Formato tracce di testo video Web).


## <a name="creating-the-human-in-the-loop-review"></a>Creazione di revisione umane

Il processo di moderazione restituisce un elenco di fotogrammi chiave dal video con una trascrizione delle relative tracce audio. Il passaggio successivo consiste nel creare una revisione nello strumento di revisione di Content Moderator per moderatori risorse umane. Tornare al metodo `ProcessVideo()` in `Program.cs` per visualizzare la chiamata al metodo `CreateVideoReviewInContentModerator()`. Il è incluso nella classe `videoReviewApi`, che si trova in `VideoReviewAPI.cs`, e viene illustrato di seguito.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` chiama diversi altri metodi per eseguire queste attività:

> [!NOTE]
> L'applicazione console sa la libreria [FFmpeg](https://ffmpeg.org/) per la generazione di anteprime. Tali anteprime (immagini) corrispondono ai timestamp dei fotogrammi nell'[output della moderazione video](#sample-video-moderation-response).

|Attività|Metodi|File|
|-|-|-|
|Estrarre i fotogrammi chiave dal video e creare le immagini di anteprima|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Analizzare la trascrizione del testo, se disponibile, per individuare audio spinto o per adulti|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Preparare e inviare una richiesta di revisione del video per un controllo umano|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Visualizzazione predefinita della revisione del video

La schermata seguente visualizza i risultati dei passaggi precedenti.

![Visualizzazione predefinita della revisione del video](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Generazione della trascrizione

Fino a questo momento il codice presentato in questa esercitazione è incentrata sul contenuto visivo. La revisione del contenuto vocale è un processo separato e facoltativo che, come indicato, usa una trascrizione generata dall'audio. È ora possibile esaminare il modo in cui le trascrizioni del testo vengono create e usate nel processo di revisione. L'attività di generazione della trascrizione rientra nell'ambito del servizio [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

L'applicazione esegue queste attività:

|Attività|Metodi|File|
|-|-|-|
|Determinare se le trascrizioni del testo devono essere generate|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|In tal caso, inviare un processo di trascrizione come parte della moderazione|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Scaricare una copia locale della trascrizione|`GenerateTranscript()`|`VideoModerator.cs`|
|Contrassegnare i fotogrammi del video che contengono audio inappropriato|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Aggiungere i risultati alla revisione|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configurazione delle attività

Esaminiamo ora l'invio del processo di trascrizione. `CreateAzureMediaServicesJobToModerateVideo()` (già descritto) chiama `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

La configurazione per l'attività di trascrizione viene letta dal file `MediaIndexerConfig.json` nella cartella `Lib` della soluzione. Gli asset AMS vengono creati per il file di configurazione e per l'output del processo di trascrizione. Quando si esegue il processo AMS, questa attività crea una trascrizione del testo dalla traccia audio del file video.

> [!NOTE]
> L'applicazione di esempio esegue il riconoscimento vocale solo per la lingua inglese-Stati Uniti.

### <a name="transcript-generation"></a>Generazione della trascrizione

La trascrizione viene pubblicata come un asset AMS. Per analizzare la trascrizione per eventuale contenuto inappropriato, l'applicazione scarica l'asset da Servizi multimediali di Azure. `CreateAzureMediaServicesJobToModerateVideo()` chiama `GenerateTranscript()`, come illustrato qui, per recuperare il file.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Dopo alcune fasi di configurazione di AMS necessarie, il download effettivo viene eseguito chiamando `DownloadAssetToLocal()`, una funzione generica che consente di copiare un asset AMS in un file locale.

## <a name="transcript-moderation"></a>Moderazione delle trascrizioni

Quando la trascrizione è disponibile, viene analizzata e usata nella revisione. La creazione della revisione rientra nell'ambito di `CreateVideoReviewInContentModerator()`, che chiama `GenerateTextScreenProfanity()` per eseguire il processo. Questo metodo, a sua volta, chiama `TextScreen()`, che contiene la maggior parte delle funzionalità. 

`TextScreen()` esegue queste attività:

- Analisi della trascrizione per rilevare timestamp e didascalie
- Invio di ogni didascalia per la regolazione del testo
- Contrassegno di tutti i fotogrammi in cui potrebbe essere presente contenuto vocale inappropriato

Esaminiamo ogni attività in dettaglio.

### <a name="initialize-the-code"></a>Inizializzare il codice

Inizializzare in primo luogo tutte le variabili e le raccolte.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Analizzare la trascrizione per individuare le didascalie

Analizzare successivamente la trascrizione formattata VTT per individuare didascalie e timestamp. Lo strumento di revisione consente di visualizzare tali didascalie nella scheda relativa alla trascrizione nella schermata di revisione del video. I timestamp vengono usati per sincronizzare le didascalie con i fotogrammi video corrispondenti.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderare le didascalie con il servizio di regolazione del testo

Analizzare successivamente le didascalie del testo con l'API di Content Moderator.

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se si supera il limite, l'SDK genera un'eccezione con il codice di errore 429. 
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Suddivisione del passaggio di regolazione del testo

Il metodo `TextScreen()` è sostanziale ed è pertanto necessario analizzarlo.

1. In primo luogo, il metodo legge il file di trascrizione riga per riga e ignora le righe vuote e le righe che contengono un oggetto `NOTE` con un punteggio di attendibilità. Il metodo estrae i timestamp e gli elementi di testo dai *segnali* nel file. Un segnale rappresenta il testo della taccia audio e include le ore di inizio e di fine. Un segnale inizia con la riga di timestamp con la stringa `-->` ed è seguito da una o più righe di testo.

1. Le istanze di `CaptionScreentextResult` (definito in `TranscriptProfanity.cs`) vengono usate per contenere le informazioni analizzate da ogni segnale.  Quando viene rilevata una nuova riga di timestamp o si raggiunge la lunghezza di testo massima di 1024 caratteri, un nuovo oggetto `CaptionScreentextResult` viene aggiunto a `csrList`. 

1. Il metodo invia quindi ogni segnale all'API di moderazione del testo e chiama `ContentModeratorClient.TextModeration.DetectLanguageAsync()` e `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, oggetti definiti nell'assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Per evitare limitazioni in termini di frequenza, il metodo viene sospeso per un secondo prima di inviare ogni segnale.

1. Dopo aver ricevuto i risultati dal servizio di moderazione del testo, il metodo li analizza per verificare se soddisfano le soglie di attendibilità. Questi valori vengono stabiliti in `App.config` come `OffensiveTextThreshold`, `RacyTextThreshold` e `AdultTextThreshold`. Vengono archiviati infine anche i termini inappropriati. Tutti i fotogrammi presenti nell'intervallo di tempo del segnale vengono contrassegnati come contenenti termini offensivi, spinti e/o testo per adulti.

1. `TextScreen()` restituisce un'istanza di `TranscriptScreenTextResult` che contiene un risultato di moderazione del testo complessivo del video. Questo oggetto include flag e i punteggi per i vari tipi di contenuto inappropriato, insieme a un elenco di tutti i termini inappropriati. Il chiamante `CreateVideoReviewInContentModerator()` chiama `UploadScreenTextResult()` per collegare queste informazioni alla revisione in modo che siano disponibili per i revisori umani.
 
## <a name="video-review-transcript-view"></a>Visualizzazione trascrizione della revisione video

La schermata seguente mostra il risultato della generazione della trascrizione e i passaggi di regolazione.

![Visualizzazione trascrizione della moderazione video](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Output del programma

L'output della riga di comando seguente del programma illustra le diverse attività quando vengono completate. Il risultato della moderazione (in formato JSON) e la trascrizione del riconoscimento vocale sono anche disponibili nella stessa directory dei file video originale.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Passaggi successivi

[Scaricare la soluzione Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) e i file di esempio e le librerie richieste per l'esercitazione e avviare l'integrazione.
