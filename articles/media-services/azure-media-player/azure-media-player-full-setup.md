---
title: Configurazione completa di Azure Media Player
description: Informazioni su come configurare Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727242"
---
# <a name="azure-media-player-full-setup"></a>Configurazione completa di Azure Media Player #

Azure Media Player è semplice da configurare. Bastano pochi istanti per ottenere la riproduzione di base del contenuto multimediale direttamente dall'account di Servizi multimediali di Azure.Only takes only few only to get basic playback of media content right from your Azure Media Services account. [Gli esempi](https://github.com/Azure-Samples/azure-media-player-samples) vengono forniti anche nella directory degli esempi della versione.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Passaggio 1: includi i file JavaScript e CSS nell'intestazione della pagina ##

Con Azure Media Player è possibile accedere agli script dalla versione ospitata della rete CDN. È spesso consigliabile inserire JavaScript prima del `<body>` tag `<head>`del corpo finale anziché , ma Azure Media Player include un 'HTML5 Shiv', che deve essere nella testa per le versioni precedenti di IE per rispettare il tag video come elemento valido.

> [!NOTE]
> Se si usa già un HTML5 shiv come [Modernizr,](http://modernizr.com/) puoi includere il codice JavaScript di Azure Media Player ovunque. Tuttavia assicurati che la versione di Modernizr includa lo shiv per il video.

### <a name="cdn-version"></a>Versione CDN ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> Non **utilizzare** la `latest` versione nell'ambiente di produzione, in quanto è soggetta a modifiche su richiesta. Sostituire `latest` con una versione di Azure Media Player. Ad esempio, sostituire `latest` con `2.1.1`. Le versioni di Azure Media Player possono essere interrogate da [qui](azure-media-player-changelog.md).

> [!NOTE]
> Dal `1.2.0` rilascio, non è più necessario includere il percorso per i tecnici di fallback (ritirerà automaticamente il percorso dal percorso relativo del file azuremediaplayer.min.js). È possibile modificare la posizione dei tecnici di fallback `<head>` aggiungendo lo script seguente negli script successivi.

> [!NOTE]
> A causa della natura dei plug-in Flash e Silverlight, i file swf e xap devono essere ospitati in un dominio senza informazioni o dati sensibili, che vengono automaticamente occupati automaticamente con la versione ospitata della rete CDN di Azure.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Passaggio 2: aggiungi un tag video HTML5 alla tua pagina ##

Con Azure Media Player è possibile usare un tag video HTML5 per incorporare un video. Azure Media Player leggerà quindi il tag e lo farà funzionare in tutti i browser, non solo in quelli che supportano il video HTML5. Oltre al markup di base, Azure Media Player richiede alcuni elementi aggiuntivi.

1. `<data-setup>` L'attributo `<video>` nell'attributo indica ad Azure Media Player di configurare automaticamente il video quando la pagina è pronta e di leggere qualsiasi (in formato JSON) dall'attributo.
1. L'attributo: `id` Deve essere utilizzato e univoco per ogni video sulla stessa pagina.
1. L'attributo `class` contiene due classi:
    - `azuremediaplayer`applica gli stili necessari per la funzionalità dell'interfaccia utente di Azure Media Player
    - `amp-default-skin`applica l'interfaccia predefinita ai controlli HTML5
1. L'include `<source>` due attributi obbligatori
    - `src`l'attributo può includere un file*con estensione ism/manifesto* da Servizi multimediali di Azure, azure Media Player aggiunge automaticamente gli URL per DASH, SMOOTH e HLS al lettore
    - `type`attributo è il tipo MIME richiesto del flusso. Il tipo MIME associato a *".ism/manifest"* è *"application/vnd.ms-sstr-xml"*
1. L'attributo `<source>` *facoltativo* `<data-setup>` in Azure Media Player indica ad Azure Media Player se sono presenti criteri di recapito univoci per il flusso da Servizi multimediali di Azure, inclusi, ma non solo, il tipo di crittografia (AES o PlayReady, Widevine o FairPlay) e il token.

Includi/escludi attributi, impostazioni, sorgenti e tracce esattamente come faresti per i video HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Per impostazione predefinita, il pulsante di riproduzione grande si trova nell'angolo in alto a sinistra in modo da non coprire le parti interessanti del poster. Se preferisci centrare il pulsante di riproduzione `amp-big-play-centered` `class` grande, puoi aggiungere un'ulteriore opzione al tuo `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Installazione alternativa per HTML caricato dinamicamente ###

Se la pagina Web o l'applicazione carica il tag video in modo dinamico (ajax, appendChild e così via), in modo che non esista al caricamento della pagina, è consigliabile configurare manualmente il lettore anziché basarsi sull'attributo di impostazione dei dati. A tale scopo, rimuovere innanzitutto l'attributo di impostazione dei dati dal tag in modo che non vi sia confusione quando il lettore viene inizializzato. Successivamente, eseguire il codice JavaScript seguente qualche tempo dopo il caricamento di Azure Media Player JavaScript e dopo che il tag video è stato caricato nel DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

Il primo argomento `amp` nella funzione è l'ID del tag video. Sostituirlo con il proprio.

Il secondo argomento è un oggetto options. Consente di impostare opzioni aggiuntive come possibile con l'attributo data-setup.

Il terzo argomento è un callback 'ready'. Una volta inizializzata, Azure Media Player chiamerà questa funzione. Nel callback ready, l'oggetto 'this' fa riferimento all'istanza del lettore.

Anziché utilizzare un ID elemento, è anche possibile passare un riferimento all'elemento stesso.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)