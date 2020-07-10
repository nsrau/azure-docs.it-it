---
title: Incorporare Widget Video Indexer nelle app
titleSuffix: Azure Media Services
description: Informazioni su come incorporare Video Indexer widget nelle app.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: d76f3afa3a831f402f93322ecec350bfdb0c788d
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166026"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Incorporare Widget Video Indexer nelle app

Questo articolo illustra come è possibile incorporare Video Indexer widget nelle app. Video Indexer supporta l'incorporamento di tre tipi di widget nelle app, ovvero *cognitive Insights*, *Player*e *Editor*.

A partire dalla versione 2, l'URL di base del widget include l'area dell'account specificato. Ad esempio, un account nell'area Stati Uniti occidentali genera: `https://www.videoindexer.ai/embed/insights/.../?location=westus2`.

## <a name="widget-types"></a>Tipi di widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Un widget Cognitive Insights include tutte le informazioni dettagliate visive estratte dal processo di indicizzazione del video. Il widget cognitive Insights supporta i parametri URL facoltativi seguenti:

|Name (Nome)|Definizione|Descrizione|
|---|---|---|
|`widgets` | Stringhe separate da virgola | Consente di controllare le informazioni dettagliate di cui si vuole eseguire il rendering.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` esegue il rendering solo di utenti e parole chiave interfaccia utente Insights.<br/>Opzioni disponibili: People, animatedCharacters, keywords, labels, sentimentals, Emotions, topics, KeyFrames, transcript, OCR, Speakers, Scenes e namedEntities.|
|`controls`|Stringhe separate da virgola|Consente di controllare i controlli di cui si desidera eseguire il rendering.<br/>Esempio: Visualizza `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` solo l'opzione di ricerca e il pulsante di download.<br/>Opzioni disponibili: ricerca, download, set di impostazioni, lingua.|
|`language`|Un codice di lingua breve (nome del linguaggio)|Controlla il linguaggio Insights.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>o `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Un codice di lingua breve | Controlla la lingua dell'interfaccia utente. Il valore predefinito è `en`. <br/>Esempio: `locale=de`.|
|`tab` | Scheda selezionata predefinita | Controlla la scheda **Insights** di cui è stato eseguito il rendering per impostazione predefinita. <br/>Esempio: `tab=timeline` esegue il rendering delle informazioni dettagliate con la scheda **sequenza temporale** selezionata.|
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati, vedere [come ottenere il nome dell'area](regions.md). Se l'account è in anteprima, `trial` per il valore del percorso è necessario usare. `trial`è il valore predefinito per il `location` parametro.| 

### <a name="player-widget"></a>Widget Player

È possibile usare il widget Player per eseguire lo streaming di video usando la velocità in bit adattiva. Il widget Player supporta i parametri URL facoltativi seguenti.

|Name (Nome)|Definizione|Descrizione|
|---|---|---|
|`t` | Secondi dall'inizio | Consente di avviare la riproduzione del lettore dal punto di tempo specificato.<br/> Esempio: `t=60`. |
|`captions` | Codice lingua | Recupera la didascalia nella lingua specificata durante il caricamento del widget affinché sia disponibile nel menu **didascalie** .<br/> Esempio: `captions=en-US`. |
|`showCaptions` | Un valore booleano | Carica il lettore con i sottotitoli già abilitati.<br/> Esempio: `showCaptions=true`. |
|`type`| | Attiva un'interfaccia del lettore audio (la parte video viene rimossa).<br/> Esempio: `type=audio`. |
|`autoplay` | Un valore booleano | Indica se il lettore deve iniziare a riprodurre il video quando viene caricato. Il valore predefinito è `true`.<br/> Esempio: `autoplay=false`. |
|`language`/`locale` | Codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`.|
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati, vedere [come ottenere il nome dell'area](regions.md). Se l'account è in anteprima, `trial` per il valore del percorso è necessario usare. `trial`è il valore predefinito per il `location` parametro.| 

### <a name="editor-widget"></a>Widget editor

È possibile usare il widget editor per creare nuovi progetti e gestire le informazioni dettagliate di un video. Il widget editor supporta i parametri URL facoltativi seguenti.

|Name (Nome)|Definizione|Descrizione|
|---|---|---|
|`accessToken`<sup>*</sup> | string | Consente di accedere ai video che si trovano solo nell'account usato per incorporare il widget.<br> Il widget dell'Editor richiede il `accessToken` parametro. |
|`language` | Codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`. |
|`locale` | Un codice di lingua breve | Controlla il linguaggio Insights. Il valore predefinito è `en`.<br/>Esempio: `language=de`. |
|`location` ||Il `location` parametro deve essere incluso nei collegamenti incorporati, vedere [come ottenere il nome dell'area](regions.md). Se l'account è in anteprima, `trial` per il valore del percorso è necessario usare. `trial`è il valore predefinito per il `location` parametro.| 

<sup>*</sup>Il proprietario deve prestare `accessToken` attenzione.

## <a name="embedding-videos"></a>Incorporamento di video

Questa sezione illustra come incorporare il contenuto pubblico e privato nelle app.

Il `location` parametro deve essere incluso nei collegamenti incorporati, vedere [come ottenere il nome dell'area](regions.md). Se l'account è in anteprima, `trial` per il valore del percorso è necessario usare. `trial`è il valore predefinito per il `location` parametro. Ad esempio: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> La condivisione di un collegamento per il widget **Player** o **Insights** includerà il token di accesso e consentirà di concedere le autorizzazioni di sola lettura all'account.

### <a name="public-content"></a>Contenuto pubblico

1. Accedere al sito Web [video Indexer](https://www.videoindexer.ai/) .
1. Selezionare il video che si desidera utilizzare e premere **Play**.
1. Selezionare il tipo di widget desiderato (**cognitive Insights**, **Player**o **Editor**).
1. Fare clic su ** &lt; / &gt; incorpora**.
5. Copiare il codice di incorporamento (viene visualizzato in **copia il codice incorporato** nella finestra di dialogo di **condivisione & incorporamento** ).
6. Aggiungere il codice all'app.

### <a name="private-content"></a>Contenuto privato

Per incorporare un video privato, è necessario passare un token di accesso nell' `src` attributo dell'iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Per ottenere il contenuto del widget cognitive Insights, usare uno dei metodi seguenti:

- API [widget ottenere informazioni dettagliate](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Token di accesso Get video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Aggiungerlo come parametro di query all'URL. Specificare questo URL come `src` valore per l'iframe, come illustrato in precedenza.

Per fornire funzionalità di modifica di Insights nel widget incorporato, è necessario passare un token di accesso che include le autorizzazioni di modifica. Usare il [widget Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) o [ottenere il token di accesso video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) con `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interazione dei widget

Il widget cognitive Insights può interagire con un video nell'app. Questa sezione illustra come ottenere questa interazione.

![Video Indexer widget cognitive Insights](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicazioni tra le origini

Per ottenere Video Indexer widget per comunicare con altri componenti, il servizio Video Indexer:

- Usa il metodo HTML5 per la comunicazione tra le origini `postMessage` .
- Convalida il messaggio nell'origine VideoIndexer.ai.

Se si implementa il proprio codice del lettore e si esegue l'integrazione con i widget cognitive Insights, è responsabilità dell'utente convalidare l'origine del messaggio che deriva da VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorporare i widget nell'app o nel Blog (scelta consigliata)

Questa sezione illustra come ottenere l'interazione tra due Video Indexer widget in modo che quando un utente seleziona il controllo Insight sull'app, il giocatore passa al momento pertinente.

1. Copiare il codice di incorporamento del widget Player.
2. Copiare il codice di incorporamento di Cognitive Insights.
3. Aggiungere il [file Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) per gestire la comunicazione tra i due widget:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

A questo punto, quando un utente seleziona il controllo Insight sull'app, il lettore passa al momento pertinente.

Per altre informazioni, vedere la [demo video Indexer-incorporare entrambi i widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporare il widget Cognitive Insights e usare Azure Media Player per riprodurre il contenuto

Questa sezione illustra come ottenere l'interazione tra un widget cognitive Insights e un'istanza di Azure Media Player usando il [plug-in amp](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Aggiungere un plug-in Video Indexer per il lettore AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Creare un'istanza di Azure Media Player con il plug-in Video Indexer.

    ```javascript
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
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Copiare il codice di incorporamento di Cognitive Insights.

È ora possibile comunicare con Azure Media Player.

Per altre informazioni, vedere la [demo Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporare il widget Video Indexer cognitive Insights e usare un lettore video diverso

Se si usa un lettore video diverso da Azure Media Player, è necessario modificare manualmente il lettore video per ottenere la comunicazione.

1. Inserire il lettore video.

    Ad esempio, un lettore HTML5 standard:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Incorporare il widget Cognitive Insights.
3. Implementare la comunicazione per il lettore facendo in modo che resti in ascolto dell'evento "message". Ad esempio:

    ```javascript
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
    ```

Per altre informazioni, vedere la [demo Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Aggiunta di sottotitoli

Se si incorpora Video Indexer Insights con il proprio [Azure Media Player](https://aka.ms/azuremediaplayer), è possibile usare il `GetVttUrl` metodo per ottenere didascalie chiuse (sottotitoli). È anche possibile chiamare un metodo JavaScript dal plug-in Video Indexer AMP `getSubtitlesUrl` (come illustrato in precedenza).

## <a name="customizing-embeddable-widgets"></a>Personalizzazione di widget incorporabili

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

È possibile scegliere i tipi di informazioni desiderate. A tale scopo, specificarli come valore per il parametro URL seguente aggiunto al codice di incorporamento ottenuto (dall'API o dall'app Web): `&widgets=<list of wanted widgets>` .

I valori possibili sono: `people` ,,, `animatedCharacters` `keywords` `labels` , `sentiments` , `emotions` , `topics` , `keyframes` , `transcript` , `ocr` , `speakers` , `scenes` e `namedEntities` .

Se, ad esempio, si vuole incorporare un widget che contiene solo persone e parole chiave Insights, l'URL di incorporamento di iframe sarà simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Si può anche personalizzare il titolo della finestra dell'iframe, fornendo il valore `&title=<YourTitle>` all'URL dell'iframe. (Personalizza il `<title>` valore HTML).
   
Ad esempio, se si vuole dare alla finestra dell'iframe il titolo "MyInsights", l'URL avrà un aspetto simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Si noti che questa opzione è pertinente solo nei casi in cui è necessario aprire le informazioni dettagliate in una nuova finestra.

### <a name="player-widget"></a>Widget Player

Se si incorpora il lettore Video Indexer, è possibile sceglierne le dimensioni specificando le dimensioni dell'iframe.

Ad esempio:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Per impostazione predefinita, Video Indexer Player dispone di didascalie chiuse AutoGenerate basate sulla trascrizione del video. La trascrizione viene estratta dal video con la lingua di origine selezionata al momento del caricamento del video.

Se si vuole incorporare con una lingua diversa, è possibile aggiungere `&captions=<Language Code>` all'URL di incorporamento del lettore. Se si desidera che le didascalie vengano visualizzate per impostazione predefinita, è possibile passare &showCaptions = true.

L'URL di incorporamento sarà simile al seguente:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Riproduzione automatica

Per impostazione predefinita, il lettore inizierà a riprodurre il video. è possibile scegliere di non passare `&autoplay=false` all'URL di incorporamento precedente.

## <a name="code-samples"></a>Esempi di codice

Vedere il repository di [esempi di codice](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) che contiene esempi per video Indexer API e widget:

| File/cartella                       | Descrizione                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Caricare video dell'indicizzatore video in un Azure Media Player personalizzato.                        |
| `azure-media-player-vi-insights`  | Incorporare VI Insights con una Azure Media Player personalizzata.                             |
| `control-vi-embedded-player`      | Incorporare VI Player e controllarlo dall'esterno.                                    |
| `custom-index-location`           | Incorporare VI Insights da una posizione esterna personalizzata (può essere un BLOB del cliente).     |
| `embed-both-insights`             | Utilizzo di base di VI Insights sia Player che Insights.                            |
| `embed-insights-with-AMP`         | Incorpora il widget informazioni dettagliate con una Azure Media Player personalizzata.                      |
| `customize-the-widgets`           | Incorporare i widget con opzioni personalizzate.                                     |
| `embed-both-widgets`              | Incorpora VI Player e Insights e comunica tra loro.                      |
| `url-generator`                   | Genera un URL di incorporamento personalizzato widget basato sulle opzioni specificate dall'utente.             |
| `html5-player`                    | Incorpora VI Insights con un lettore video HTML5 predefinito.                           |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e modificare Video Indexer Insights, vedere [visualizzare e modificare video Indexer Insights](video-indexer-view-edit.md).

Vedere anche [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
