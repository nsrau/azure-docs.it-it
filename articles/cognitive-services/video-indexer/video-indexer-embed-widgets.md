---
title: 'Esempio: Incorporare i widget di Video Indexer nelle applicazioni'
titlesuffix: Azure Cognitive Services
description: Informazioni su come incorporare i widget di Video Indexer nell'applicazione.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: sample
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3d9e00a97355c03da5360846d4cd9f27a8540ed6
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017433"
---
# <a name="example-embed-video-indexer-widgets-into-your-applications"></a>Esempio: Incorporare i widget di Video Indexer nelle applicazioni

Questo articolo illustra come incorporare i widget di Video Indexer nelle applicazioni. Video Indexer supporta l'incorporamento nelle applicazioni di due tipi di widget: **Cognitive Insights** e **Player**. 
## <a name="widget-types"></a>Tipi di widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Un widget **Cognitive Insights** include tutte le informazioni dettagliate visive estratte dal processo di indicizzazione del video. Il widget delle informazioni dettagliate supporta i parametri URL facoltativi seguenti:

|NOME|Definizione|DESCRIZIONE|
|---|---|---|
|widgets|Stringhe separate da virgola|Consente di controllare le informazioni dettagliate di cui si vuole eseguire il rendering. <br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` esegue il rendering solo delle informazioni dettagliate su persone e marchi<br/>Le opzioni disponibili sono: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Non supportato tramite URL con version=2<br/><br/>**Nota:** il parametro URL **widgets** non è supportato se si usa **version=2**. |
|version|Versioni del widget **Cognitive Insights**|Per ottenere gli aggiornamenti più recenti per il widget Cognitive Insights, aggiungere il parametro di query `?version=2` all'URL di incorporamento. Ad esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?version=2` <br/> Per ottenere la versione precedente, rimuovere semplicemente `version=2` dall'URL.

### <a name="player-widget"></a>Widget Player

Un widget **Player** consente di eseguire lo streaming del video a bitrate adattivo. Il widget del lettore supporta i parametri URL facoltativi seguenti:

|NOME|Definizione|DESCRIZIONE|
|---|---|---|
|t|Secondi dall'inizio|Fa partire il lettore dal tempo specificato.<br/>Esempio: t=60|
|captions|Codice lingua|Recupera i sottotitoli nella lingua specificata durante il caricamento del widget in modo che siano disponibili nel menu dei sottotitoli.<br/>Esempio: captions=it-IT|
|showCaptions|Valore booleano|Carica il lettore con i sottotitoli già abilitati.<br/>Esempio: showCaptions=true|
|type||Attiva un'interfaccia del lettore audio (la parte video viene rimossa).<br/>Esempio: type=audio|
|autoplay|Valore booleano|Indica se il lettore deve iniziare la riproduzione del video al caricamento (il valore predefinito è true).<br/>Esempio: autoplay=false|
|Linguaggio|Codice lingua|Controlla la lingua del lettore (il valore predefinito è en-US)<br/>Esempio: language=de-DE|

## <a name="embedding-public-content"></a>Incorporamento di contenuto pubblico

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Fare clic sul video da usare.
3. Fare clic sul pulsante di incorporamento che compare sotto il video.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Dopo aver fatto clic sul pulsante verrà visualizzata una finestra modale di incorporamento in cui è possibile scegliere il widget da incorporare nell'applicazione.
    La selezione di un widget (**Player** oppure **Cognitive Insights**) genera il codice di incorporamento da incollare nell'applicazione.
 
4. Scegliere il tipo di widget desiderato (**Cognitive Insights** oppure **Player**).
5. Copiare il codice di incorporamento e aggiungerlo all'applicazione. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

## <a name="embedding-private-content"></a>Incorporamento di contenuto privato

È possibile ottenere incorporare i codici di incorporamento da popup di incorporamento (come illustrato nella sezione precedente) solo per i video **pubblici**. 

Se si vuole incorporare un video **privato**, è necessario passare un token di accesso nell'attributo **src** dell'**iframe**:

     https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>
    
Usare l'API [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) per recuperare il contenuto del widget Cognitive Insights oppure usare [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) e aggiungerlo come parametro di query all'URL, come mostrato sopra. Specificare questo URL come valore **src** dell'**iframe**.

Se si vogliono fornire funzionalità di modifica delle informazioni dettagliate (come quelle disponibili nell'applicazione Web) nel widget incorporato, sarà necessario passare un token di accesso con autorizzazioni di modifica. Usare [**Get Insights Widget**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-insights-widget?) o [**Get Video Access Token**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) con **&allowEdit=true**. 

## <a name="widgets-interaction"></a>Interazione dei widget

Il widget **Cognitive Insights** può interagire con un video nell'applicazione. Questa sezione illustra come ottenere questa interazione.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicazioni tra le origini

Per fare in modo che i widget di Video Indexer comunichino con altri componenti, il servizio Video Indexer esegue le operazioni seguenti:

- Usa il metodo HTML5 di comunicazione tra le origini **postMessage** e 
- Convalida il messaggio nell'origine VideoIndexer.ai. 

Se si sceglie di implementare il codice del proprio lettore ed eseguire l'integrazione con i widget **Cognitive Insights**, è responsabilità dell'utente convalidare l'origine del messaggio proveniente da VideoIndexer.ai.

### <a name="embed-both-types-of-widgets-in-your-application--blog-recommended"></a>Incorporare entrambi i tipi di widget nell'applicazione/blog (scelta consigliata) 

Questa sezione illustra come ottenere l'interazione tra due widget di Video Indexer in modo che, quando un utente fa clic sul controllo delle informazioni dettagliate nell'applicazione, il lettore passi al momento pertinente.

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script> 

1. Copiare il codice di incorporamento del widget **Player**.
2. Copiare il codice di incorporamento di **Cognitive Insights**.
3. Aggiungere il [**file Mediator**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) per gestire la comunicazione tra i due widget:

    <script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>

Ora quando un utente fa clic sul controllo delle informazioni dettagliate nell'applicazione, il lettore passa al momento pertinente.

Per altre informazioni, vedere [questa demo](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporare il widget Cognitive Insights e usare Azure Media Player per riprodurre il contenuto

Questa sezione illustra come ottenere l'interazione tra un widget **Cognitive Insights** e un'istanza di Azure Media Player usando il [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Aggiungere un plug-in di Video Indexer per il lettore AMP.

        <script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>


2. Creare un'istanza di Azure Media Player con il plug-in di Video Indexer.

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. Copiare il codice di incorporamento di **Cognitive Insights**.

Ora dovrebbe essere possibile comunicare con Azure Media Player.

Per altre informazioni, vedere [questa demo](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>Incorporare il widget Cognitive Insights di Video Indexer e usare un lettore personalizzato (può trattarsi di qualsiasi lettore)

Se si usa il proprio lettore, è necessario modificarlo personalmente per ottenere la comunicazione. 

1. Inserire il lettore video.

    Ad esempio, un lettore HTML5 standard

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorporare il widget Cognitive Insights.
3. Implementare la comunicazione per il lettore facendo in modo che resti in ascolto dell'evento "message". Ad esempio: 

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>


Per altre informazioni, vedere [questa demo](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Aggiunta di sottotitoli

Se si incorporano informazioni dettagliate di Video Indexer in un lettore AMP, è possibile usare il metodo **GetVttUrl** per ottenere i sottotitoli codificati. Si può anche chiamare un metodo javascript dal plug-in AMP di Video Indexer **getSubtitlesUrl** (come illustrato in precedenza). 

## <a name="customizing-embeddable-widgets"></a>Personalizzazione di widget incorporabili

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights
È possibile scegliere i tipi desiderati di informazioni dettagliate specificandoli come valore del parametro URL seguente, aggiunto al codice di incorporamento ottenuto dall'API o dall'applicazione Web:

**&widgets=** \<elenco dei widget desiderati>

I valori possibili sono: people, keywords, sentiments, transcript, search.

Ad esempio, se si vuole incorporare un widget che contiene solo informazioni dettagliate su persone e ricerca, l'URL di incorporamento dell'iframe avrà un aspetto simile al seguente https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search

Si può anche personalizzare il titolo della finestra dell'iframe, fornendo il valore **&title=**<YourTitle> all'URL dell'iframe. (In questo modo verrà personalizzato il valore html \<title>).
Ad esempio, se si vuole dare alla finestra dell'iframe il titolo "MyInsights", l'URL sarà simile al seguente: https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights. Si noti che questa opzione è pertinente solo nei casi in cui è necessario aprire le informazioni dettagliate in una nuova finestra.

### <a name="player-widget"></a>Widget Player
Se si incorpora il lettore Video Indexer, è possibile sceglierne le dimensioni specificando le dimensioni dell'iframe.

Ad esempio: 

    <iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />

Per impostazione predefinita, il lettore Video Indexer avrà sottotitoli codificati generati automaticamente, basati sulla trascrizione estratta dal video, nella lingua di origine che è stata selezionata al momento del caricamento del video.

Se si vuole incorporare il lettore con una lingua diversa, è possibile aggiungere **&captions=< Language | "all" | "false" >** all'URL di incorporamento del lettore o inserire "all" come valore se si vogliono avere i sottotitoli in tutte le lingue disponibili.
Se si vuole che i sottotitoli siano visualizzati per impostazione predefinita, è possibile passare **&showCaptions=true**

L'URL di incorporamento sarà simile al seguente: https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian. Per disabilitare i sottotitoli, è possibile passare "false" come valore per il parametro captions.

Per impostazione predefinita il lettore all'avvio inizia a riprodurre il video. Si può modificare questo comportamento passando &autoplay=false all'URL di incorporamento precedente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e modificare le informazioni dettagliate di Video Indexer, vedere [questo](video-indexer-view-edit.md) articolo.

Inoltre, consultare [Indicizzatore video codepen](https://codepen.io/videoindexer/pen/eGxebZ).
