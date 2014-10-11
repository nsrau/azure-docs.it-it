<properties linkid="develop-media-services-how-to-guides-ios-media-player-framework" urlDisplayName="iOS Media Player Framework" pageTitle="Use the iOS Media Player Framework with Azure Media Services" metaKeywords="" description="Learn how to use the Media Services iOS Media Player Framework library to create rich, dynamic apps.," metaCanonical="" services="media-services" documentationCenter="" title="How to use the Azure Media Services iOS Media Player Framework" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="mobile-ios" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Come utilizzare Media Player Framework per iOS di Servizi multimediali di Azure

La libreria Media Player Framework per iOS di Servizi multimediali di Azure consente agli sviluppatori per dispositivi iPod, iPhone e iPad di creare con facilità applicazioni client complesse e dinamiche, in grado di generare e combinare rapidamente flussi audio e video. Ad esempio, nelle applicazioni in cui vengono visualizzati contenuti sportivi è possibile inserire facilmente qualsiasi tipo di annuncio e controllarne la frequenza di visualizzazione anche quando si ritorna al contenuto principale. Questa funzionalità è utile anche per le applicazioni didattiche, ad esempio per creare lezioni in cui vengono visualizzate digressioni o intestazioni laterali prima di tornare al contenuto principale.

In genere, la compilazione di un'applicazione in grado di creare flussi di contenuto risultante da un'interazione tra l'applicazione e l'utente è un'attività relativamente complessa, in quanto è necessario creare l'intero flusso da zero e archiviarlo, anticipatamente, nel server. Media Player Framework per iOS consente invece di creare applicazioni client in grado di eseguire tutte le attività necessarie senza dover mantenere il controllo sul flusso di contenuto principale né modificarlo. È possibile:

-   Programmare flussi di contenuto in anticipo nel dispositivo client
-   Programmare annunci preroll o inserimenti
-   Programmare annunci postroll o inserimenti
-   Programmare annunci midroll o inserimenti e creare podcast annunci.
-   Controllare se l'annuncio midroll o inserimento viene riprodotto ogni volta che la sequenza temporale del contenuto viene riavvolta o se viene riprodotto una sola volta e quindi rimosso automaticamente dalla sequenza temporale.
-   Inserire contenuto in modo dinamico direttamente nella sequenza temporale a seguito di un evento qualsiasi, indipendentemente dal fatto che l'utente abbia fatto clic su un pulsante o l'applicazione abbia ricevuto una notifica da un servizio. Un programma che trasmette notizie, può inviare una notifica relativa alle ultime notizie e l'applicazione può "sospendere" il contenuto principale per caricare in modo dinamico un flusso relativo alle ultime notizie.

La combinazione di queste funzionalità con quelle di riproduzione multimediale dei dispositivi iOS consente di creare esperienze multimediali avanzate in tempi brevi e con meno risorse.

L'SDK contiene un'applicazione SamplePlayer che illustra come creare un'applicazione per iOS che utilizzi la maggior parte di queste funzionalità per creare un flusso di contenuti in tempo reale e consentire all'utente di attivare dinamicamente altro contenuto con la semplice pressione di un pulsante. In questa esercitazione vengono illustrati i componenti principali dell'applicazione SamplePlayer e viene mostrato come utilizzarli come punto di partenza per creare un'applicazione.

## Introduzione all'applicazione SamplePlayer

Nella procedura riportata di seguito viene descritto come ottenere l'applicazione e vengono presentate le aree dell'applicazione che utilizzano il framework.

1.  Clonare l'archivio Git.

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2.  Aprire il progetto disponibile in `azure-media-player-framework/src/iOS/HLSClient/`: **SamplePlayer.xcodeproj**.

3.  Di seguito è illustrata la struttura del lettore di esempio:

![Struttura del codice di esempio HLS][]

1.  Nella cartella iPad sono presenti due file con estensione xib: **SeekbarViewController** e **SamplePlayerViewController**. Consentono di creare il layout dell'interfaccia utente dell'applicazione per iPad. Analogamente, nella cartella iPhone sono presenti due file con estensione xib che consentono di definire la barra di scorrimento e il controller.

2.  La logica dell'applicazione principale risiede in **SamplePlayerViewController.m** nella cartella `Shared`. La maggior parte dei frammenti di codice descritta di seguito si trova in questo file.

## Informazioni sul layout dell'interfaccia utente

L'interfaccia del lettore è definita tramite due file con estensione xib. Nella sezione seguente verrà utilizzato il layout di un dispositivo iPad come esempio, tuttavia, il layout dell'iPhone è molto simile e i principi sono gli stessi.

### SamplePlayerViewController\_iPad.xib

![Barra degli indirizzi del lettore di esempio][]

-   In **Media URL** viene specificato l'URL utilizzato per caricare un flusso multimediale. L'applicazione dispone di un elenco prepopolato di URL multimediali che è possibile utilizzare tramite i pulsanti di selezione dell'URL. In alternativa, è possibile specificare l'URL del contenuto HLS (HTTP Live Streaming) personalizzato. Il contenuto multimediale verrà usato come primo contenuto principale.
    **Nota: non lasciare questo URL vuoto.**

-   I pulsanti **URL Selection** consentono di selezionare URL alternativi dall'elenco di URL del contenuto multimediale.

### SeekbarViewController\_iPad.xib

![Controller Seek Bar][]

-   Usare il **pulsante di riproduzione** per riprodurre e sospendere la riproduzione del contenuto multimediale.

-   Nell'elemento **Seek bar** viene proiettata l'intera sequenza temporale della riproduzione. Durante la ricerca, premere e tenere premuto il dispositivo di scorrimento, quindi trascinarlo nella posizione desiderata e infine rilasciare il pulsante di ricerca nella barra di scorrimento.

**Nota**: quando l'utente esegue una ricerca in un annuncio, viene visualizzata una nuova barra di scorrimento con la durata dell'annuncio. La barra di scorrimento principale presenta solo la durata del contenuto principale, ovvero un annuncio ha una durata di 0 nella barra di scorrimento principale.

-   Il controllo **Player time** visualizza due tempi (`Label:playerTime`), ad esempio 00:23/02:10. In questo caso, 00:23 indica il tempo di riproduzione corrente e 02:10 indica la durata totale del contenuto multimediale.

-   **I** pulsanti SkipFroward e SkipBackward al momento non funzionano come previsto. A breve verrà rilasciata una versione aggiornata.

-   Se si preme il pulsante **Schedule Now** mentre è in corso la riproduzione del contenuto principale, viene inserito un annuncio (è possibile definire l'URL di origine dell'annuncio nel code-behind). Nota: nella versione corrente non è possibile programmare un annuncio mentre è in corso la riproduzione di un altro annuncio.

### Come programmare il contenuto principale

Programmazione di un video clip da 0 a 80 secondi:

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Nel codice di esempio precedente:

-   **L'oggetto** MediaTime controlla il video clip da programmare come contenuto principale. Nell'esempio precedente, il video clip verrà programmato per avere una durata di 80 secondi (da 0 a 80 secondi).
-   **clipBeginMediaTime** indica l'ora di inizio per l'avvio della riproduzione del video. Se, ad esempio **clipBeginMediaTime** = 5 il video clip viene avviato al 5° secondo del video clip.
-   **clipEndMediaTime** indica l'ora di fine per la riproduzione di un video. Se **clipEndMediaTime** è 100, la riproduzione del video terminerà al 100° secondo del video clip.
    \*Per pianificare **MediaTime** si richiederà quindi al framework di eseguire **appendContentClip**. Nell'esempio precedente l'URL del contenuto principale è disponibile in `[NSURL URLWithString:url]` e la programmazione per questo contenuto multimediale viene impostata tramite **withMedia**:
     `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])` .

**Nota:** programmare sempre il contenuto principale prima di pianificare eventuali annunci, inclusi gli annunci preroll.

### Variante: Se è in corso la riproduzione di due video di contenuto principali, è possibile programmare la riproduzione del secondo contenuto dopo il primo utilizzando il codice seguente:

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Questa operazione consente di programmare due flussi di contenuto sulla sequenza temporale del contenuto principale. Il primo flusso è pianificato in base a `URLWithString:url` e il secondo contenuto è pianificato in base a `URLWithString:secondContent`. La riproduzione del secondo contenuto inizia a 30 secondi del flusso video e termina a 80 secondi.

## Programmazione di annunci

Nella versione corrente è supportato solo un annuncio **pauseTimeline=false** e pertanto, al termine dell'annuncio il lettore riprenderà dal punto in cui è stato lasciato il contenuto principale.

Di seguito sono riportati alcuni aspetti importanti:

-   Tutti gli elementi \*\*LinearTime.duration\*\* devono essere impostati su 0 durante la programmazione di un annuncio.
-   Quando \*\*clipEndMediaTime\*\* è più lungo rispetto alla durata dell'annuncio, l'annuncio termina dopo la fine e non viene generata alcuna eccezione. Si consiglia di verificare se la durata naturale di un annuncio è compresa nell'intervallo di rendering (\*\*clipEndMediaTime\*\*) in modo da non perdere un'opportunità di annuncio.
-   Sono supportati gli annunci preroll, midroll e postroll. Gli annunci preroll possono essere programmati solo immediatamente all'inizio di qualsiasi contenuto. Ad esempio, non è possibile programmare un annuncio preroll per il secondo contenuto in uno scenario RCE (Rough Cut Editing).
-   Sono supportati gli annunci a più riproduzioni e a riproduzione singola e possono essere usati in abbinamento ad annunci preroll, midroll o postroll.
-   Il formato dell'annuncio può essere MP4 o HLS.


### Come programmare annunci preroll, midroll, postroll e podcast annuncio

#### Programmazione di annunci preroll

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'oggetto **AdInfo** rappresenta tutte le informazioni relative al clip dell'annuncio:

-   **ClipURL** indica l'URL per l'origine del clip.
-   La proprietà **mediaTime** indica la durata della riproduzione di un annuncio. (**clipBeginMediaTime** indica l'ora di inizio di un annuncio e **clipEndMediaTime** definisce la fine dell'annuncio). Nel codice di esempio precedente, è stato programmato un annuncio per 5 secondi, con inizio al secondo 0 e fino al 5° secondo della durata dell'annuncio.
-   L'oggetto **Policy** non è attualmente usato dal framework.
-   È necessario impostare il valore **appendTo** su -1 se non si tratta di un podcast annuncio.
-   Il valore di **type** può essere di tipo preroll, midroll, postroll o podcast annuncio. Poiché ad esso non sono associati intervalli, per gli annunci preroll o postroll è necessario specificare il tipo.

#### Programmazione di annunci midroll

Se si aggiunge `adLinearTime.startTime = 23;` all'esempio di codice precedente, la riproduzione dell'annuncio viene avviata al 23° secondo della sequenza temporale del contenuto principale.

#### Programmazione di annunci postroll

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

L'unica differenza rispetto alla programmazione dell'annuncio preroll sta nell'elemento `postAdInfo.type = AdType_Postroll;`. Nel codice precedente è stato programmato un annuncio al 5° secondo come annuncio postroll.

#### Programmazione di podcast annuncio

I podcast annuncio sono costituiti da un'interruzione di un annuncio con più annunci in riproduzione back to back. Di seguito è riportato il codice per la programmazione di due annunci in un podcast annuncio.

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Prendere nota delle informazioni seguenti:

-   Per il primo clip, **appendTo** è -1. E quando si chiama `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]`, `adIndex` riceve un valore univoco che indica la fine del primo clip nel podcast annuncio. Per il secondo clip nel podcast annuncio, allineare l'inizio del secondo annuncio con la fine del primo impostando **appendTo** come `adpodInfo2.appendTo = adIndex;`, che specifica la posizione di fine del primo come la posizione di inizio del secondo clip.
-   È quindi necessario impostare il tipo come `AdType_Pod` per indicare che si tratta di un podcast annuncio.

### Come programmare un annuncio a riproduzione singola o a più riproduzioni

    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Come illustrato nell'esempio di codice precedente, se si imposta **deleteAfterPlay** su **YES**, l'annuncio viene riprodotto solo una volta. Se invece si imposta **deleteAfterPlay** su **NO**, l'annuncio viene riprodotto in modo continuo, ovvero "a più riproduzioni".

### Per altre informazioni, fare riferimento al [wiki di Media Player Framework di Azure][].

  [Struttura del codice di esempio HLS]: http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png
  [Barra degli indirizzi del lettore di esempio]: http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png
  [Controller Seek Bar]: http://mingfeiy.com/wp-content/uploads/2013/01/controller.png
  [wiki di Media Player Framework di Azure]: https://github.com/WindowsAzure/azure-media-player-framework/wiki
