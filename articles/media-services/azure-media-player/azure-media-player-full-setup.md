---
title: Azure Media Player installazione completa
description: Informazioni su come configurare il Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 15f5918748df80cec01ccf89835a0ef51da64529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296247"
---
# <a name="azure-media-player-full-setup"></a>Configurazione completa di Azure Media Player #

Azure Media Player è semplice da configurare. Sono necessari pochi istanti per ottenere la riproduzione di base del contenuto multimediale direttamente dall'account di servizi multimediali di Azure. Gli [esempi](https://github.com/Azure-Samples/azure-media-player-samples) sono disponibili anche nella directory degli esempi della versione.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Passaggio 1: includere i file CSS e JavaScript all'inizio della pagina ##

Con Azure Media Player, è possibile accedere agli script dalla versione ospitata della rete CDN. È spesso consigliabile inserire JavaScript prima del tag body finale `<body>` anziché `<head>` , ma Azure Media Player include un'HTML5 Shiv ', che deve essere in testa per le versioni precedenti di IE per rispettare il tag video come elemento valido.

> [!NOTE]
> Se si usa già un oggetto Shiv HTML5 come [modernizzator](https://modernizr.com/) , è possibile includere il Azure Media Player JavaScript in qualsiasi posizione. Assicurarsi tuttavia che la versione di modernizzatore includa Shiv per video.

### <a name="cdn-version"></a>Versione della rete CDN ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> **NON** usare la versione `latest` nell'ambiente di produzione, perché è soggetta a modifiche su richiesta. Sostituire `latest` con una versione di Azure Media Player. Ad esempio, sostituire `latest` con `2.1.1`. È possibile eseguire query sulle versioni di Azure Media Player [qui](azure-media-player-changelog.md).

> [!NOTE]
> Dal `1.2.0` rilascio, non è più necessario includere il percorso per le tecnologie di fallback (il percorso verrà automaticamente rilevato dal percorso relativo del file di azuremediaplayer.min.js). È possibile modificare il percorso dei tecnici di fallback aggiungendo lo script seguente nel `<head>` dopo gli script precedenti.

> [!NOTE]
> A causa della natura dei plug-in Flash e Silverlight, i file SWF e XAP devono essere ospitati in un dominio senza informazioni o dati sensibili. questa operazione viene eseguita automaticamente con la versione ospitata della rete CDN di Azure.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Passaggio 2: aggiungere un tag video HTML5 alla pagina ##

Con Azure Media Player, è possibile usare un tag video HTML5 per incorporare un video. Azure Media Player leggerà quindi il tag e lo farà funzionare in tutti i browser, non solo quelli che supportano il video HTML5. Oltre al markup di base, Azure Media Player necessita di alcuni elementi aggiuntivi.

1. L' `<data-setup>` attributo nella `<video>` indica Azure Media Player per configurare automaticamente il video quando la pagina è pronta e leggere qualsiasi (in formato JSON) dall'attributo.
1. L' `id` attributo deve essere usato e univoco per ogni video presente nella stessa pagina.
1. L' `class` attributo contiene due classi:
    - `azuremediaplayer` applica gli stili necessari per Azure Media Player funzionalità dell'interfaccia utente
    - `amp-default-skin` applica l'interfaccia predefinita ai controlli HTML5
1. `<source>`Include due attributi obbligatori
    - `src` l'attributo può includere un file **. ISM/manifest* da servizi multimediali di Azure aggiunto, Azure Media Player aggiunge automaticamente gli URL per Dash, Smooth e HLS al lettore
    - `type` attribute è il tipo MIME necessario del flusso. Il tipo MIME associato a *". ISM/manifest"* è *"application/vnd. ms-SSTR + XML"*
1. L'attributo *facoltativo* nell' `<data-setup>` `<source>` indica Azure Media Player se sono presenti criteri di recapito univoci per il flusso da servizi multimediali di Azure, tra cui il tipo di crittografia (AES o PlayReady, Widevine o Fairplay) e il token.

Includere/escludere attributi, impostazioni, origini e tracce esattamente come per i video HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Per impostazione predefinita, il pulsante di riproduzione grande si trova nell'angolo superiore sinistro, quindi non copre le parti interessanti del poster. Se si preferisce centrare il pulsante Play di grandi dimensioni, è possibile aggiungere un `amp-big-play-centered` `class` elemento aggiuntivo all' `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Installazione alternativa per HTML caricato dinamicamente ###

Se la pagina Web o l'applicazione carica il tag video in modo dinamico (AJAX, appendChild e così via), in modo che possa non esistere al caricamento della pagina, è necessario configurare manualmente il lettore anziché basarsi sull'attributo di installazione dati. A tale scopo, rimuovere prima l'attributo di installazione dati dal tag, in modo da evitare confusione quando il lettore viene inizializzato. Successivamente, eseguire il codice JavaScript seguente dopo il caricamento di Azure Media Player JavaScript e dopo che il tag video è stato caricato nel DOM.

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

Il primo argomento della `amp` funzione è l'ID del tag video. Sostituirlo con il proprio.

Il secondo argomento è un oggetto Options. Consente di impostare opzioni aggiuntive, come è possibile con l'attributo di configurazione dei dati.

Il terzo argomento è un callback "Ready". Dopo l'inizializzazione di Azure Media Player, questa funzione verrà chiamata. Nel callback pronto, l'oggetto ' This ' fa riferimento all'istanza del lettore.

Anziché utilizzare un ID elemento, è anche possibile passare un riferimento all'elemento stesso.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)