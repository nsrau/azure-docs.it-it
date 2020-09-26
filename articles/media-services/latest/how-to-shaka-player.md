---
title: Come usare il lettore Shaka con servizi multimediali di Azure
description: Questo articolo illustra come usare il lettore Shaka con servizi multimediali di Azure
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: e4be3d4508d342c77507cf824fb036e32084b617
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329738"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Come usare il lettore Shaka con servizi multimediali di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Panoramica

Shaka Player è una libreria JavaScript open source per un supporto adattivo. Riproduce formati multimediali adattivi, ad esempio DASH e HLS, in un browser, senza usare plug-in o Flash. Al contrario, il lettore Shaka usa le estensioni dei supporti Open Web Standards e i supporti crittografati.

Si consiglia di usare [Mux.js](https://github.com/videojs/mux.js/) come, senza di esso, il lettore Shaka supporta il formato CMAF HLS, ma non il TS HLS.

La documentazione ufficiale è reperibile nella [documentazione di Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per questo articolo è disponibile in [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementazione del lettore

Seguire queste istruzioni se è necessario implementare una propria istanza del lettore:

1. Creare un `index.html` file in cui si ospiterà il lettore. Aggiungere le righe di codice seguenti (è possibile sostituire le versioni più recenti, se applicabile):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Aggiungere un file JavaScript con il codice seguente:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Sostituire `manifestUrl` con l'URL HLS o Dash dal localizzatore di streaming dell'asset, disponibile nella pagina del localizzatore di streaming nella portale di Azure.
    ![URL di streaming Locator](media/how-to-shaka-player/streaming-urls.png)

1. Eseguire un server (ad esempio con `npm http-server` ) e il lettore dovrebbe funzionare...

## <a name="set-up-captions"></a>Configurare le didascalie

### <a name="set-up-vod-captions"></a>Configurare sottotitoli VOD

Eseguire le righe di codice seguenti e sostituire `captionUrl` con la directory. VTT (il file VTT deve trovarsi nello stesso host per evitare l'errore CORS), `lang` con il codice di due lettere per la lingua e `type` con `caption` o `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Configurare sottotitoli Live Stream

Abilita didascalie in Live Stream è configurato aggiungendo la riga di codice seguente:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Configurare l'autenticazione del token

Eseguire le righe di codice seguenti e sostituire `token` con la stringa del token:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Configurare la crittografia AES-128

Il lettore Shaka non supporta attualmente la crittografia AES-128.

Un collegamento a un [problema](https://github.com/google/shaka-player/issues/850) di GitHub per seguire lo stato di questa funzionalità.

## <a name="set-up-drm-protection"></a>Configurare la protezione DRM

Shaka Player utilizza Encrypted Media Extensions (EME), che richiede un URL sicuro da utilizzare. Quindi, per testare qualsiasi contenuto protetto da DRM, è necessario usare HTTPS. Se il sito usa HTTPS, anche il manifesto e ogni segmento dovranno usare HTTPS. A causa dei requisiti di contenuto misto.

Ordine di preferenza per la gestione Shaka degli URL dei rispettivi server licenze:

1. La configurazione ClearKey, usata per il debug, deve eseguire l'override di tutti gli altri elementi. L'applicazione può comunque specificare un server licenze ClearKey.
2. Eventuali server configurati per l'applicazione, se presenti, devono eseguire l'override di qualsiasi elemento del manifesto.
3. I server licenze forniti dal manifesto vengono utilizzati solo se non viene specificato altro.

Per specificare l'URL del server licenze per Widevine o PlayReady, è possibile usare il codice seguente:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Tutto il contenuto FairPlay richiede l'impostazione di un certificato del server. Viene impostato nella configurazione del lettore:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Per ulteriori informazioni, vedere la [documentazione sulla protezione DRM di Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Passaggi successivi

* [Usare il Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Avvio rapido: Crittografare il contenuto](encrypt-content-quickstart.md)
