---
title: 'Esercitazione: Moderare video e trascrizioni in .NET - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione aiuta a comprendere come creare una soluzione completa per la moderazione di video e trascrizioni con moderazione basata su Machine Learning e la creazione di revisioni con intervento umano.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 2fbd4270221cb23a4f99a0f8155bb1de76472f31
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976979"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Esercitazione: Moderazione di video e trascrizioni

Questa esercitazione mostra come creare una soluzione completa per la moderazione di video e trascrizioni con moderazione basata su Machine Learning e l'integrazione di revisioni con intervento umano.

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Comprimere i video di input per un'elaborazione più veloce
> - Moderare il video per ottenere immagini e fotogrammi con informazioni dettagliate
> - Usare i timestamp dei fotogrammi per creare (immagini)
> - Inviare timestamp e anteprime per creare le revisioni del video
> - Convertire il riconoscimento vocale video in testo (trascrizione) con l'API Media Indexer
> - Moderato la trascrizione con il servizio di moderazione del testo
> - Aggiungere la trascrizione moderata alla revisione del video

## <a name="prerequisites"></a>Prerequisiti

- Registrarsi sul sito Web dello [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e creare tag personalizzati. Vedere [Usare i tag](Review-Tool-User-Guide/tags.md) per assistenza su questo passaggio.

    ![screenshot dei tag personalizzati per la moderazione dei video](images/video-tutorial-custom-tags.png)
- Per eseguire l'applicazione di esempio, è necessario un account Azure, una risorsa di Servizi multimediali di Microsoft Azure, una risorsa di Azure Content Moderator e le credenziali di Azure Active Directory. Per istruzioni su come ottenere queste risorse, vedere la guida [API Moderazione video](video-moderation-api.md).
- Scaricare l'[applicazione console revisione di video](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) da GitHub.

## <a name="enter-credentials"></a>Immettere le credenziali

Modificare il file `App.config` e aggiungere il nome del tenant di Active Directory, gli endpoint del servizio e le chiavi di sottoscrizione indicate da `#####`. Sono necessarie le informazioni seguenti:

|Chiave|DESCRIZIONE|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Endpoint per l'API Servizi multimediali di Azure (AMS)|
|`ClientSecret`|Chiave di sottoscrizione per Servizi multimediali di Azure|
|`ClientId`|ID client per Servizi multimediali di Azure|
|`AzureAdTenantName`|Nome del tenant di Active Directory che rappresenta l'organizzazione|
|`ContentModeratorReviewApiSubscriptionKey`|Chiave di sottoscrizione per l'API di revisione di Content Moderator|
|`ContentModeratorApiEndpoint`|Endpoint per l'API Content Moderator|
|`ContentModeratorTeamId`|ID team di Content Moderator|

## <a name="examine-the-main-code"></a>Esaminare il codice principale

La classe `Program` in `Program.cs` è il punto di ingresso principale per l'applicazione di moderazione di video.

### <a name="methods-of-program-class"></a>Metodi della classe Program

|Metodo|DESCRIZIONE|
|-|-|
|`Main`|Analizza la riga di comando, raccoglie l'input utente e inizia l'elaborazione.|
|`ProcessVideo`|Comprime, carica, modera e crea revisioni video.|
|`CreateVideoStreamingRequest`|Crea un flusso per caricare un video.|
|`GetUserInputs`|Raccoglie l'input utente e viene usato quando non è presente alcuna opzione della riga di comando.|
|`Initialize`|Inizializza gli oggetti necessari per il processo di moderazione.|

### <a name="the-main-method"></a>Metodo Main

`Main()` è il punto di avvio dell'esecuzione da cui partire per comprendere il processo di moderazione video.

[!code-csharp[Main](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=20-24,42-52,54-74)]

`Main()` gestisce gli argomenti della riga di comando seguenti:

- Percorso di una directory contenente file video MPEG-4 da inviare per la moderazione. Tutti i file `*.mp4` in questa directory e nelle relative sottodirectory vengono inviati per la moderazione.
- Facoltativamente, un flag booleano (true/false) che indica se le trascrizioni di testo devono essere generate allo scopo di moderare l'audio.

Se non sono presenti argomenti della riga di comando, `Main()` chiama `GetUserInputs()`. Questo metodo richiede all'utente di immettere il percorso di un singolo file video e di specificare se deve essere generata una trascrizione di testo.

> [!NOTE]
> L'applicazione console usa l'[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) per generare le trascrizioni dalla traccia audio del video caricato. I risultati vengono restituiti in formato WebVTT. Per altre informazioni su questo formato, vedere [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Formato tracce di testo video Web).

### <a name="initialize-and-processvideo-methods"></a>Metodi Initialize e ProcessVideo

Indipendentemente dal fatto che le opzioni del programma provengano dalla riga di comando e dall'input utente interattivo, `Main()` chiama in seguito `Initialize()` per creare le istanze seguenti:

|Classe|DESCRIZIONE|
|-|-|
|`AMSComponent`|Comprime i file video prima di inviarli per la moderazione.|
|`AMSconfigurations`|Interfaccia i dati di configurazione dell'applicazione trovati in `App.config`.|
|`VideoModerator`| Carica, codifica, crittografia e modera tramite AMS SDK.|
|`VideoReviewApi`|Gestisce le revisioni video nel servizio Content Moderator.|

Queste classi (a parte `AMSConfigurations`, che è semplice) vengono trattate in modo più dettagliato nelle sezioni successive dell'esercitazione.

I file video, infine, vengono elaborati uno alla volta chiamando il metodo `ProcessVideo()` per ognuno.

[!code-csharp[ProcessVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=76-118)]

Il metodo `ProcessVideo()` è abbastanza semplice ed esegue nell'ordine le operazioni seguenti:

- Comprime il video
- Carica il video in un asset di Servizi multimediali di Azure
- Crea un processo AMS per moderare il video
- Crea una revisione del video in Content Moderator

Nelle sezioni seguenti vengono trattati più in dettaglio alcuni processi singoli richiamati da `ProcessVideo()`. 

## <a name="compress-the-video"></a>Compressione del video

Per ridurre al minimo il traffico di rete, l'applicazione converte file video in formato H.264 (AVC MPEG-4) e li dimensiona sulla larghezza massima di 640 pixel. Il codec H.264 è consigliato per l'elevata efficienza (tasso di compressione). La compressione viene eseguita usando lo strumenti da riga comando `ffmpeg` gratuito, incluso nella cartella `Lib` della soluzione Visual Studio. I file di input possono essere di qualsiasi formato supportato da `ffmpeg`, inclusi i codec e formati di file video usati più di frequente.

> [!NOTE]
> Quando si avvia il programma usando le opzioni della riga di comando, specificare una directory che contiene i file video da inviare per la moderazione. Tutti i file della directory che contengono l'estensione `.mp4` vengono elaborati. Per elaborare altre estensioni di nomi file, aggiornare il metodo `Main()` in `Program.cs` per includere le estensioni desiderate.

Il codice che comprime un singolo file video è la classe `AmsComponent` in `AMSComponent.cs`. Il metodo che implementa questa funzionalità è `CompressVideo()`, illustrato di seguito.

[!code-csharp[CompressVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/AMSComponent.cs?range=31-59)]

Il codice esegue queste operazioni:

- Verifica che configurazione in `App.config` contenga tutti i dati necessari
- Verifica che il file binario `ffmpeg` sia presente
- Compilazione del nome file di output aggiungendo `_c.mp4` al nome di base del file (ad esempio `Example.mp4`  ->  `Example_c.mp4`)
- Compilazione di una stringa della riga di comando per eseguire la conversione
- Avvio di un'elaborazione `ffmpeg` dalla riga di comando
- Attesa del video da elaborare

> [!NOTE]
> Se i video sono già compressi tramite H.264 e sono di dimensioni appropriate, è possibile riscrivere `CompressVideo()` per saltare la compressione.

Il metodo restituisce il nome del file di output compresso.

## <a name="upload-and-moderate-the-video"></a>Caricamento e moderazione del video

Il video deve essere archiviato in Servizi multimediali di Azure prima che possa essere elaborato dal servizio Content Moderation. La classe `Program` in `Program.cs` dispone di un metodo breve `CreateVideoStreamingRequest()` che restituisce un oggetto che rappresenta la richiesta di streaming usata per caricare il video.

[!code-csharp[CreateVideoStreamingRequest](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=120-133)]

L'oggetto risultante `UploadVideoStreamRequest` viene definito in `UploadVideoStreamRequest.cs` (e il relativo padre `UploadVideoRequest` in `UploadVideoRequest.cs`). Tali classi non vengono visualizzate qui poiché si tratta di classi brevi usate solo per contenere i dati video compressi e le relative informazioni. Un'altra classe solo di dati, ovvero `UploadAssetResult` (`UploadAssetResult.cs`), viene usata per archiviare i risultati del processo di caricamento. Ora è possibile comprendere queste righe in `ProcessVideo()`:

[!code-csharp[ProcessVideoSnippet](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/Program.cs?range=91-104)]

Tali righe eseguono queste attività:

- Creazione di un oggetto `UploadVideoStreamRequest` per caricare il video compresso
- Impostazione del flag `GenerateVTT` della richiesta se l'utente ha chiesto una trascrizione di testo
- Chiamata a `CreateAzureMediaServicesJobToModerateVideo()` per eseguire il caricamento e ricevere il risultato

## <a name="examine-video-moderation-code"></a>Analisi del codice di moderazione video

Il metodo `CreateAzureMediaServicesJobToModerateVideo()` è in `VideoModerator.cs`, che contiene la maggior parte del codice che interagisce con Servizi multimediali di Azure. Il codice sorgente del metodo è illustrato nell'estratto seguente.

[!code-csharp[CreateAzureMediaServicesJobToModerateVideo](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=230-283)]

Il codice esegue queste operazioni:

- Creazione di un processo AMS per l'elaborazione da eseguire
- Aggiunta di attività per la codifica del file video, moderazione del file e generazione di una trascrizione del testo
- Invio del processo, caricamento del file e avvio dell'elaborazione
- Recupero dei risultati di moderazione, della trascrizione del testo (se richiesta) e di altre informazioni

## <a name="sample-video-moderation-output"></a>Output di moderazione video di esempio

Il risultato del processo di moderazione video (vedere l'[Avvio rapido](video-moderation-api.md)) è una struttura di dati JSON che contiene i risultati della moderazione. Tali risultati includono una suddivisione dei frammenti (immagini) all'interno del video, ognuno contenente eventi (clip) con i fotogrammi chiave contrassegnati per la revisione. A ogni fotogramma chiave viene assegnato un punteggio per la probabilità che contenga contenuto spinto o per adulti. L'esempio seguente mostra una risposta JSON:

```json
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
```

Una trascrizione dell'audio del video viene anche generata quando è impostato il flag `GenerateVTT`.

> [!NOTE]
> L'applicazione console usa l'[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) per generare le trascrizioni dalla traccia audio del video caricato. I risultati vengono restituiti in formato WebVTT. Per altre informazioni su questo formato, vedere [Web Video Text Tracks Format](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Formato tracce di testo video Web).

## <a name="create-a-human-review"></a>Creare una revisione umana

Il processo di moderazione restituisce un elenco di fotogrammi chiave dal video con una trascrizione delle relative tracce audio. Il passaggio successivo consiste nel creare una revisione nello strumento di revisione di Content Moderator per moderatori risorse umane. Tornare al metodo `ProcessVideo()` in `Program.cs` per visualizzare la chiamata al metodo `CreateVideoReviewInContentModerator()`. Il è incluso nella classe `videoReviewApi`, che si trova in `VideoReviewAPI.cs`, e viene illustrato di seguito.

[!code-csharp[CreateVideoReviewInContentModerator](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=42-69)]

`CreateVideoReviewInContentModerator()` chiama diversi altri metodi per eseguire queste attività:

> [!NOTE]
> L'applicazione console sa la libreria [FFmpeg](https://ffmpeg.org/) per la generazione di anteprime. Tali anteprime (immagini) corrispondono ai timestamp dei fotogrammi nell'output della moderazione video.

|Attività|Metodi|File|
|-|-|-|
|Estrarre i fotogrammi chiave dal video e creare le immagini di anteprima|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Analizzare la trascrizione del testo, se disponibile, per individuare audio spinto o per adulti|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Preparare e inviare una richiesta di revisione del video per un controllo umano|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

La schermata seguente visualizza i risultati dei passaggi precedenti.

![Visualizzazione predefinita della revisione del video](images/video-tutorial-default-view.PNG)

## <a name="process-the-transcript"></a>Generazione della trascrizione

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

[!code-csharp[ConfigureTranscriptTask](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=295-304)]

La configurazione per l'attività di trascrizione viene letta dal file `MediaIndexerConfig.json` nella cartella `Lib` della soluzione. Gli asset AMS vengono creati per il file di configurazione e per l'output del processo di trascrizione. Quando si esegue il processo AMS, questa attività crea una trascrizione del testo dalla traccia audio del file video.

> [!NOTE]
> L'applicazione di esempio esegue il riconoscimento vocale solo per la lingua inglese-Stati Uniti.

### <a name="transcript-generation"></a>Generazione della trascrizione

La trascrizione viene pubblicata come un asset AMS. Per analizzare la trascrizione per eventuale contenuto inappropriato, l'applicazione scarica l'asset da Servizi multimediali di Azure. `CreateAzureMediaServicesJobToModerateVideo()` chiama `GenerateTranscript()`, come illustrato qui, per recuperare il file.

[!code-csharp[GenerateTranscript](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoModerator.cs?range=351-370)]

Dopo alcune fasi di configurazione di AMS necessarie, il download effettivo viene eseguito chiamando `DownloadAssetToLocal()`, una funzione generica che consente di copiare un asset AMS in un file locale.

## <a name="moderate-the-transcript"></a>Moderazione della trascrizione

Quando la trascrizione è disponibile, viene analizzata e usata nella revisione. La creazione della revisione rientra nell'ambito di `CreateVideoReviewInContentModerator()`, che chiama `GenerateTextScreenProfanity()` per eseguire il processo. Questo metodo, a sua volta, chiama `TextScreen()`, che contiene la maggior parte delle funzionalità.

`TextScreen()` esegue queste attività:

- Analisi della trascrizione per rilevare timestamp e didascalie
- Invio di ogni didascalia per la regolazione del testo
- Contrassegno di tutti i fotogrammi in cui potrebbe essere presente contenuto vocale inappropriato

Esaminiamo ogni attività in dettaglio.

### <a name="initialize-the-code"></a>Inizializzare il codice

Inizializzare in primo luogo tutte le variabili e le raccolte.

[!code-csharp[TextScreen](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=515-527)]

### <a name="parse-the-transcript-for-captions"></a>Analizzare la trascrizione per individuare le didascalie

Analizzare successivamente la trascrizione formattata VTT per individuare didascalie e timestamp. Lo strumento di revisione consente di visualizzare tali didascalie nella scheda relativa alla trascrizione nella schermata di revisione del video. I timestamp vengono usati per sincronizzare le didascalie con i fotogrammi video corrispondenti.

[!code-csharp[TextScreen2](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=528-567)]

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderare le didascalie con il servizio di regolazione del testo

Analizzare successivamente le didascalie del testo con l'API di Content Moderator.

> [!NOTE]
> La chiave del servizio Content Moderator ha un limite di frequenza di richieste al secondo (RPS). Se si supera il limite, l'SDK genera un'eccezione con il codice di errore 429.
>
> Una chiave di livello gratuito prevede un unico limite di frequenza RPS.

[!code-csharp[TextScreen3](~/VideoReviewConsoleApp/Microsoft.ContentModerator.AMSComponent/AMSComponentClient/VideoReviewAPI.cs?range=568-653)]

### <a name="text-moderation-breakdown"></a>Suddivisione moderazione testo

Il metodo `TextScreen()` è sostanziale ed è pertanto necessario analizzarlo.

1. In primo luogo, il metodo legge il file di trascrizione riga per riga e ignora le righe vuote e le righe che contengono un oggetto `NOTE` con un punteggio di attendibilità. Il metodo estrae i timestamp e gli elementi di testo dai *segnali* nel file. Un segnale rappresenta il testo della taccia audio e include le ore di inizio e di fine. Un segnale inizia con la riga di timestamp con la stringa `-->` ed è seguito da una o più righe di testo.

1. Le istanze di `CaptionScreentextResult` (definito in `TranscriptProfanity.cs`) vengono usate per contenere le informazioni analizzate da ogni segnale.  Quando viene rilevata una nuova riga di timestamp o si raggiunge la lunghezza di testo massima di 1024 caratteri, un nuovo oggetto `CaptionScreentextResult` viene aggiunto a `csrList`. 

1. Il metodo invia quindi ogni segnale all'API di moderazione del testo e chiama `ContentModeratorClient.TextModeration.DetectLanguageAsync()` e `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, oggetti definiti nell'assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Per evitare limitazioni in termini di frequenza, il metodo viene sospeso per un secondo prima di inviare ogni segnale.

1. Dopo aver ricevuto i risultati dal servizio di moderazione del testo, il metodo li analizza per verificare se soddisfano le soglie di attendibilità. Questi valori vengono stabiliti in `App.config` come `OffensiveTextThreshold`, `RacyTextThreshold` e `AdultTextThreshold`. Vengono archiviati infine anche i termini inappropriati. Tutti i fotogrammi presenti nell'intervallo di tempo del segnale vengono contrassegnati come contenenti termini offensivi, spinti e/o testo per adulti.

1. `TextScreen()` restituisce un'istanza di `TranscriptScreenTextResult` che contiene un risultato di moderazione del testo complessivo del video. Questo oggetto include flag e i punteggi per i vari tipi di contenuto inappropriato, insieme a un elenco di tutti i termini inappropriati. Il chiamante `CreateVideoReviewInContentModerator()` chiama `UploadScreenTextResult()` per collegare queste informazioni alla revisione in modo che siano disponibili per i revisori umani.

La schermata seguente mostra il risultato della generazione della trascrizione e i passaggi di regolazione.

![Visualizzazione trascrizione della moderazione video](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Output del programma

L'output della riga di comando seguente del programma illustra le diverse attività quando vengono completate. Il risultato della moderazione (in formato JSON) e la trascrizione del riconoscimento vocale sono anche disponibili nella stessa directory dei file video originale.

```console
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
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo configurato un'applicazione che modera il contenuto dei video&mdash;, incluso il contenuto delle trascrizioni&mdash;e crea le revisioni nello strumento di revisione. Nel prossimo articolo si apprenderanno altre informazioni sulla moderazione dei video.

> [!div class="nextstepaction"]
> [Moderazione video](./video-moderation-human-review.md)
