---
title: Incorporare Widget Video Indexer nelle applicazioni
titlesuffix: Azure Media Services
description: Informazioni su come incorporare Video Indexer widget nell'applicazione.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: fc0b447630b5e1ac360b1d84869cea02186672fc
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "71036633"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>Incorporare Widget Video Indexer nelle applicazioni

Questo articolo illustra come incorporare Video Indexer widget nelle applicazioni. Video Indexer supporta l'incorporamento di tre tipi di widget nell'applicazione, ovvero *cognitive Insights*, *Player*e *Editor*. 

A partire dalla versione 2, l'URL di base del widget include l'area dell'account specificato. Ad esempio, un account nell'area Stati Uniti occidentali genera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipi di widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Un widget cognitive Insights include tutte le informazioni visive estratte dal processo di indicizzazione video. Il widget cognitive Insights supporta i parametri URL facoltativi seguenti.

|name|Definizione|Description|
|---|---|---|
|`widgets`|Stringhe separate da virgola|Consente di controllare le informazioni dettagliate di cui si vuole eseguire il rendering. <br/> Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` esegue il rendering solo delle informazioni dettagliate sull'interfaccia utente per gli utenti e i marchi.<br/>Le opzioni disponibili sono: people, keywords, annotations, brands, sentiments, transcript, search.<br/>Si noti che il parametro `widgets` URL non è supportato nella versione 2.<br/>|
|`locale`|Un codice di lingua breve|Controlla il linguaggio Insights. Il valore predefinito è `en`. <br/> Esempio: `locale=de`.|
|`tab`|Scheda selezionata predefinita|Controlla la scheda **Insights** di cui è stato eseguito il rendering per impostazione predefinita. <br/> Esempio: `tab=timeline` esegue il rendering delle informazioni dettagliate con la scheda **sequenza temporale** selezionata.|

### <a name="player-widget"></a>Widget Player

È possibile usare il widget Player per eseguire lo streaming di video usando la velocità in bit adattiva. Il widget Player supporta i parametri URL facoltativi seguenti.

|name|Definizione|Description|
|---|---|---|
|`t`|Secondi dall'inizio|Consente di avviare la riproduzione del lettore dal punto di tempo specificato.<br/> Esempio: `t=60`.|
|`captions`|Codice lingua|Recupera la didascalia nella lingua specificata durante il caricamento del widget affinché sia disponibile nel menu **didascalie** .<br/> Esempio: `captions=en-US`.|
|`showCaptions`|Un valore booleano|Carica il lettore con i sottotitoli già abilitati.<br/> Esempio: `showCaptions=true`.|
|`type`||Attiva un'interfaccia del lettore audio (la parte video viene rimossa).<br/> Esempio: `type=audio`.|
|`autoplay`|Un valore booleano|Indica se il lettore deve iniziare a riprodurre il video quando viene caricato. Il valore predefinito è `true`.<br/> Esempio: `autoplay=false`.|
|`language`|Codice lingua|Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`.|

### <a name="editor-widget"></a>Widget editor

È possibile usare il widget editor per creare nuovi progetti e gestire le informazioni dettagliate di un video. Il widget editor supporta i parametri URL facoltativi seguenti.

|name|Definizione|Description|
|---|---|---|
|`accessToken`<sup>*</sup>|Stringa|Consente di accedere ai video che si trovano solo nell'account usato per incorporare il widget.<br> Il widget dell'Editor richiede il parametro `accessToken`.|
|`language`|Codice lingua|Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`.|
|`locale`|Un codice di lingua breve|Controlla il linguaggio Insights. Il valore predefinito è `en`.<br/>Esempio: `language=de`.|

<sup>*</sup> Il proprietario deve fornire `accessToken` con cautela.

## <a name="embedding-public-content"></a>Incorporamento di contenuto pubblico

1. Accedere al sito Web [video Indexer](https://www.videoindexer.ai/) .
2. Selezionare il video che si desidera utilizzare.
3. Selezionare il pulsante **incorpora** visualizzato sotto il video.

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Dopo aver selezionato il pulsante **incorpora** , è possibile selezionare il widget che si vuole incorporare nell'applicazione. 
4. Selezionare il tipo di widget desiderato (**cognitive Insights**, **Player**o **Editor**).
 
5. Copiare il codice di incorporamento e quindi aggiungerlo all'applicazione. 

    ![Widget](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> In caso di problemi di condivisione degli URL video, aggiungere il parametro `location` al collegamento. Il parametro deve essere impostato sulle [aree di Azure in cui video Indexer esiste](regions.md). Ad esempio: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Incorporamento di contenuto privato

Per incorporare un video privato, è necessario passare un token di accesso nell'attributo **src** dell'iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Per ottenere il contenuto del widget cognitive Insights, usare uno dei seguenti elementi:<br/>
- API [widget ottenere informazioni dettagliate](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Token di accesso Get video](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?). Aggiungerlo come parametro di query all'URL. Specificare questo URL come valore **src** per l'iframe, come illustrato in precedenza.

Per fornire funzionalità di modifica di Insights nel widget incorporato, è necessario passare un token di accesso che include le autorizzazioni di modifica. Usare il [widget Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) o [ottenere il token di accesso video](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?) con `&allowEdit=true`. 

## <a name="widgets-interaction"></a>Interazione dei widget

Il widget cognitive Insights può interagire con un video nell'applicazione. Questa sezione illustra come ottenere questa interazione.

![Widget](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicazioni tra le origini

Per ottenere Video Indexer widget per comunicare con altri componenti, il servizio Video Indexer:

- Usa il metodo del **messaggio**HTML5 per la comunicazione tra le origini. 
- Convalida il messaggio nell'origine VideoIndexer.ai. 

Se si implementa il proprio codice del lettore e si esegue l'integrazione con i widget cognitive Insights, è responsabilità dell'utente convalidare l'origine del messaggio che deriva da VideoIndexer.ai.

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>Incorporare i widget nell'applicazione o nel Blog (scelta consigliata) 

Questa sezione illustra come ottenere l'interazione tra due Video Indexer widget in modo che quando un utente seleziona il controllo Insight sull'applicazione, il lettore passa al momento pertinente.

1. Copiare il codice di incorporamento del widget del lettore.
2. Copiare il codice di incorporamento di cognitive Insights.
3. Aggiungere il [file Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) per gestire la comunicazione tra i due widget:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

A questo punto, quando un utente seleziona il controllo Insight sull'applicazione, il lettore passa al momento pertinente.

Per altre informazioni, vedere la [demo video Indexer-incorporare entrambi i widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporare il widget Cognitive Insights e usare Azure Media Player per riprodurre il contenuto

Questa sezione illustra come ottenere l'interazione tra un widget cognitive Insights e un'istanza di Azure Media Player usando il [plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).
 
1. Aggiungere un plug-in Video Indexer per il lettore AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Creare un'istanza di Azure Media Player con il plug-in Video Indexer.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
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

        // Init your AMP instance.
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
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Copiare il codice di incorporamento di cognitive Insights.

Dovrebbe essere ora possibile comunicare con Azure Media Player.

Per altre informazioni, vedere la [demo Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporare il widget Video Indexer cognitive Insights e usare un lettore video diverso

Se si usa un lettore video diverso da Azure Media Player, è necessario modificare manualmente il lettore video per ottenere la comunicazione. 

1. Inserire il lettore video.

    Ad esempio, un lettore HTML5 standard:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorporare il widget Cognitive Insights.
3. Implementare la comunicazione per il lettore facendo in modo che resti in ascolto dell'evento "message". ad esempio:

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Per altre informazioni, vedere la [demo Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Aggiunta di sottotitoli

Se si incorpora Video Indexer Insights con il proprio [Azure Media Player](https://aka.ms/azuremediaplayer), è possibile usare il metodo **GetVttUrl** per ottenere didascalie chiuse (sottotitoli). È anche possibile chiamare un metodo JavaScript dal plug-in Video Indexer AMP **getSubtitlesUrl** (come illustrato in precedenza). 

## <a name="customizing-embeddable-widgets"></a>Personalizzazione di widget incorporabili

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

È possibile scegliere i tipi di informazioni desiderate. A tale scopo, specificarli come valore per il parametro URL seguente aggiunto al codice di incorporamento ottenuto (dall'API o dall'applicazione Web): `&widgets=<list of wanted widgets>`.

I valori possibili sono: **people**, **Keywords**, **sentimentals**, **Transcript**e **Search**.

Se ad esempio si vuole incorporare un widget che contiene solo utenti e informazioni dettagliate di ricerca, l'URL di incorporamento iframe sarà simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Si può anche personalizzare il titolo della finestra dell'iframe, fornendo il valore `&title=<YourTitle>` all'URL dell'iframe. (Personalizza il valore HTML \<title >).
    
Ad esempio, se si vuole dare alla finestra dell'iframe il titolo "MyInsights", l'URL avrà un aspetto simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Si noti che questa opzione è pertinente solo nei casi in cui è necessario aprire le informazioni dettagliate in una nuova finestra.

### <a name="player-widget"></a>Widget Player

Se si incorpora il lettore Video Indexer, è possibile sceglierne le dimensioni specificando le dimensioni dell'iframe.

ad esempio:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Per impostazione predefinita, Video Indexer Player dispone di didascalie chiuse generate automaticamente, basate sulla trascrizione del video. La trascrizione viene estratta dal video con la lingua di origine selezionata al momento del caricamento del video.

Se si vuole incorporare con una lingua diversa, è possibile aggiungere `&captions=< Language | ”all” | “false” >` all'URL di incorporamento del lettore. Se si desiderano didascalie in tutte le didascalie delle lingue disponibili, utilizzare il valore `all`. Se si vuole che i sottotitoli siano visualizzati per impostazione predefinita, è possibile passare `&showCaptions=true`.

L'URL di incorporamento sarà simile al seguente: 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se si desidera disabilitare le didascalie, è possibile passare il valore del parametro `captions` come `false`.

#### <a name="autoplay"></a>AutoPlay
Per impostazione predefinita, il lettore inizierà a riprodurre il video. è possibile scegliere di non passare `&autoplay=false` all'URL di incorporamento precedente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e modificare Video Indexer Insights, vedere [visualizzare e modificare video Indexer Insights](video-indexer-view-edit.md).

Vedere anche [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
