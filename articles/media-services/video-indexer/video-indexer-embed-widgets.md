---
title: Incorporare widget Dell'indicizzatore video nelle app
titleSuffix: Azure Media Services
description: Scopri come incorporare i widget Dell'indicizzatore video nelle tue app.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 4d92bd3709c5f56db0095ca1be2caa0e9c78b42f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336826"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Incorporare widget Dell'indicizzatore video nelle app

Questo articolo mostra come incorporare i widget Dell'indicizzatore video nelle app. L'indicizzatore video supporta l'incorporamento di tre tipi di widget nelle app: *Cognitive Insights*, *Player*ed *Editor*.

A partire dalla versione 2, l'URL di base del widget include l'area dell'account specificato. Ad esempio, un account nell'area Stati Uniti occidentali genera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipi di widget

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

Un widget Cognitive Insights include tutte le informazioni dettagliate visive estratte dal processo di indicizzazione del video. Il widget Analisi cognitiva supporta i seguenti parametri URL facoltativi:

|Nome|Definizione|Descrizione|
|---|---|---|
|`widgets` | Stringhe separate da virgola | Consente di controllare le informazioni dettagliate di cui si desidera eseguire il rendering.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` esegue il rendering solo delle informazioni dettagliate dell'interfaccia utente per le persone e le parole chiave.<br/>Opzioni disponibili: persone, animatedCharacters,parole chiave, etichette, sentimenti, emozioni, argomenti, fotogrammi chiave, trascrizione, ocr, altoparlanti, scene ed entità namedE.|
|`controls`|Stringhe separate da virgola|Consente di controllare i controlli di cui si desidera eseguire il rendering.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` esegue il rendering solo dell'opzione di ricerca e del pulsante di download.<br/>Opzioni disponibili: ricerca, download, preset, lingua.|
|`language`|Un breve codice lingua (nome della lingua)|Controlla il linguaggio delle informazioni dettagliate.<br/>Esempio: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>o `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Un breve codice linguistico | Controlla la lingua dell'interfaccia utente. Il valore predefinito è `en`. <br/>Esempio: `locale=de`.|
|`tab` | La scheda selezionata predefinita | Controlla la scheda **Insights** di cui viene eseguito il rendering per impostazione predefinita. <br/>Esempio: `tab=timeline` esegue il rendering delle informazioni dettagliate con la scheda **Timeline** selezionata.|

### <a name="player-widget"></a>Widget Player

È possibile utilizzare il widget Lettore per riprodurre video in streaming utilizzando la velocità in bit adattiva. Il widget Player supporta i seguenti parametri URL facoltativi.

|Nome|Definizione|Descrizione|
|---|---|---|
|`t` | Secondi dall'inizio | Fa iniziare a giocare il giocatore dal punto temporale specificato.<br/> Esempio: `t=60`. |
|`captions` | Un codice lingua | Recupera la didascalia nella lingua specificata durante il caricamento del widget per essere disponibile nel menu **Didascalie.**<br/> Esempio: `captions=en-US`. |
|`showCaptions` | Valore booleano | Carica il lettore con i sottotitoli già abilitati.<br/> Esempio: `showCaptions=true`. |
|`type`| | Attiva lo skin di un lettore audio (la parte video viene rimossa).<br/> Esempio: `type=audio`. |
|`autoplay` | Valore booleano | Indica se il lettore deve avviare la riproduzione del video al caricamento. Il valore predefinito è `true`.<br/> Esempio: `autoplay=false`. |
|`language` | Un codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`.|

### <a name="editor-widget"></a>Widget Editor

È possibile utilizzare il widget Editor per creare nuovi progetti e gestire le informazioni dettagliate di un video. Il widget Editor supporta i seguenti parametri URL facoltativi.

|Nome|Definizione|Descrizione|
|---|---|---|
|`accessToken`<sup>*</sup> | string | Fornisce l'accesso ai video che si trovano solo nell'account utilizzato per incorporare il widget.<br> Il widget Editor `accessToken` richiede il parametro. |
|`language` | Un codice lingua | Controlla la lingua del lettore. Il valore predefinito è `en-US`.<br/>Esempio: `language=de-DE`. |
|`locale` | Un breve codice linguistico | Controlla il linguaggio di informazioni dettagliate. Il valore predefinito è `en`.<br/>Esempio: `language=de`. |

<sup>*</sup>Il proprietario `accessToken` deve fornire con cautela.

## <a name="embedding-public-content"></a>Incorporamento di contenuto pubblico

1. Accedere al sito Web [Dell'indicizzatore video.](https://www.videoindexer.ai/)
2. Seleziona il video che vuoi usare.
3. Selezionare il pulsante **Incorpora** visualizzato sotto il video.

    ![Pulsante Incorpora nell'indicizzatore video](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    Dopo aver selezionato il pulsante **Incorpora,** è possibile selezionare il widget che si desidera incorporare nell'app.
4. Selezionare il tipo di widget desiderato (**Cognitive Insights**, **Player**o **Editor**).
 
5. Copiare il codice di incorporamento e quindi aggiungerlo all'app.

    ![Incorporare il codice per l'app: Indicizzatore video](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> In caso di problemi con la condivisione degli URL video, aggiungi il `location` parametro al link. Il parametro deve essere impostato sulle aree di [Azure in cui è presente l'indicizzatore video.](regions.md) Ad esempio `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Incorporamento di contenuto privato

Per incorporare un video privato, è `src` necessario passare un token di accesso nell'attributo dell'iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Per ottenere il contenuto del widget Analisi cognitiva, utilizzare uno dei metodi seguenti:

- API [Get Insights Widget.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- Il token [Ottieni token di accesso video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Aggiungerlo come parametro di query all'URL. Specificate questo `src` URL come valore per l'iframe, come mostrato in precedenza.

Per fornire funzionalità di modifica delle informazioni dettagliate nel widget incorporato, è necessario passare un token di accesso che include le autorizzazioni di modifica. Usare [Ottieni widget Informazioni dettagliate](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) o Ottieni token di accesso [video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) con `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interazione dei widget

Il widget Approfondimenti cognitivi può interagire con un video nella tua app. Questa sezione illustra come ottenere questa interazione.

![Indicizzatore video del widget Approfondimenti cognitivi](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicazioni tra le origini

Per fare in modo che i widget Indicizzatore video comunichino con altri componenti, il servizio Video Indexer:

- Utilizza il metodo `postMessage`HTML5 di comunicazione tra origini .
- Convalida il messaggio nell'origine VideoIndexer.ai.

Se implementi il tuo codice giocatore e ti integri con i widget di Cognitive Insights, è tua responsabilità convalidare l'origine del messaggio che proviene da VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorporare widget nell'app o nel blog (scelta consigliata)

Questa sezione mostra come ottenere l'interazione tra due widget dell'indicizzatore video in modo che quando un utente seleziona il controllo di informazioni dettagliate sull'app, il lettore passi al momento pertinente.

1. Copiare il codice di incorporamento del widget Player.
2. Copiare il codice di incorporamento di Cognitive Insights.
3. Aggiungere il [file Mediator](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) per gestire la comunicazione tra i due widget:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Ora, quando un utente seleziona il controllo di informazioni dettagliate sulla tua app, il giocatore passa al momento pertinente.

Per ulteriori informazioni, consultate la [demo Indicizzatore video - Incorpora entrambi i widget](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Incorporare il widget Cognitive Insights e usare Azure Media Player per riprodurre il contenuto

In questa sezione viene illustrato come ottenere l'interazione tra un widget Informazioni cognitive e un'istanza di Azure Media Player usando il [plug-in AMP.](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)

1. Aggiungere un plug-in Dell'indicizzatore video per il lettore AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Creare un'istanza di Azure Media Player con il plug-in Indicizzatore video.

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

3. Copiare il codice di incorporamento di Cognitive Insights.

È ora possibile comunicare con Azure Media Player.You can now communicate with Azure Media Player.

Per altre informazioni, vedere la demo di [Azure Media Player e VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorporare il widget Analisi cognitiva dell'indicizzatore video e usare un lettore video diverso

Se si usa un lettore video diverso da Azure Media Player, è necessario modificare manualmente il lettore video per ottenere la comunicazione.

1. Inserire il lettore video.

    Ad esempio, un lettore HTML5 standard:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Incorporare il widget Cognitive Insights.
3. Implementare la comunicazione per il lettore facendo in modo che resti in ascolto dell'evento "message". Ad esempio:

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

Per altre informazioni, vedere la demo di [Azure Media Player e VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Aggiunta di sottotitoli

Se si incorporano informazioni dettagliate sull'indicizzatore `GetVttUrl` video con il proprio Azure Media [Player,](https://aka.ms/azuremediaplayer)è possibile usare il metodo per ottenere i sottotitoli (sottotitoli). Potete anche chiamare un metodo JavaScript dal plug-in `getSubtitlesUrl` AMP dell'indicizzatore video (come illustrato in precedenza).

## <a name="customizing-embeddable-widgets"></a>Personalizzazione di widget incorporabili

### <a name="cognitive-insights-widget"></a>Widget Cognitive Insights

È possibile scegliere i tipi di informazioni dettagliate desiderate. A tale scopo, specificarli come valore per il seguente parametro URL aggiunto al codice di incorporamento `&widgets=<list of wanted widgets>`che si ottiene (dall'API o dall'app Web): .

I valori possibili sono: **persone**, **parole chiave**, **sentimenti**, **trascrizione**e **ricerca**.

Ad esempio, se si desidera incorporare un widget che contiene solo persone e informazioni dettagliate sulla ricerca, l'URL di incorporamento iframe sarà simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

Si può anche personalizzare il titolo della finestra dell'iframe, fornendo il valore `&title=<YourTitle>` all'URL dell'iframe. (Personalizza il titolo \<HTML> valore).

Ad esempio, se si vuole dare alla finestra dell'iframe il titolo "MyInsights", l'URL avrà un aspetto simile al seguente:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Si noti che questa opzione è pertinente solo nei casi in cui è necessario aprire le informazioni dettagliate in una nuova finestra.

### <a name="player-widget"></a>Widget Player

Se si incorpora il lettore Video Indexer, è possibile sceglierne le dimensioni specificando le dimensioni dell'iframe.

Ad esempio:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Per impostazione predefinita, il lettore di indicizzatori video ha generato automaticamente i sottotitoli che si basano sulla trascrizione del video. La trascrizione viene estratta dal video con la lingua di origine selezionata al momento del caricamento del video.

Se si desidera eseguire l'incorporamento `&captions=< Language | "all" | "false" >` con una lingua diversa, è possibile aggiungerla all'URL del lettore di incorporamento. Se si desidera che i sottotitoli in `all`tutte le lingue disponibili, utilizzare il valore . Se si vuole che i sottotitoli siano visualizzati per impostazione predefinita, è possibile passare `&showCaptions=true`.

L'URL di incorporamento sarà simile al seguente:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

Se si desidera disabilitare le didascalie, è possibile passare il valore del `captions` parametro come `false`.

#### <a name="autoplay"></a>Riproduzione automatica
Per impostazione predefinita, il lettore inizierà a riprodurre il video. è possibile scegliere di `&autoplay=false` non passare all'URL di incorporamento precedente.

## <a name="code-samples"></a>Esempi di codice

Vedere il repository degli esempi di codice che contiene esempi per l'API dell'indicizzatore video e widget:See the [code samples](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) repo that contains samples for Video Indexer API and Widgets:

| File/cartella                       | Descrizione                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Caricare il video dell'indicizzatore video in un lettore multimediale di Azure personalizzato.                        |
| `azure-media-player-vi-insights`  | Incorporare VI Insights con un lettore multimediale di Azure personalizzato.                             |
| `control-vi-embedded-player`      | Incorpora VI Player e controllalo dall'esterno.                                    |
| `custom-index-location`           | Incorporare VI Insights da una posizione esterna personalizzata (può essere un BLOB del cliente).     |
| `embed-both-insights`             | Utilizzo di base di VI Insights sia per il giocatore che per le intuizioni.                            |
| `embed-insights-with-AMP`         | Incorporare il widget VI Insights con un lettore multimediale di Azure personalizzato.                      |
| `customize-the-widgets`           | Incorpora widget VI con opzioni personalizzate.                                     |
| `embed-both-widgets`              | Incorpora VI Player e Insights e comunica tra di loro.                      |
| `url-generator`                   | Genera widget URL di incorporamento personalizzati in base alle opzioni specificate dall'utente.             |
| `html5-player`                    | Incorpora VI Insights con un lettore video HTML5 predefinito.                           |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e modificare le informazioni dettagliate sull'indicizzatore video, consultate Visualizzare e modificare le [informazioni dettagliate sull'indicizzatore video.](video-indexer-view-edit.md)

Vedere inoltre [L'indicizzatore Video CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
