---
title: Come usare il lettore di Video.js con servizi multimediali di Azure
description: Questo articolo illustra come usare l'oggetto video HTML e JavaScript con servizi multimediali di Azure
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
ms.custom: devx-track-javascript
ms.openlocfilehash: 2730c6ce523e618110cd29b13ba2f37115e2cbd0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267684"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Come usare il lettore di Video.js con servizi multimediali di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Panoramica

Video.js è un lettore video Web creato per un ambiente HTML5. Riproduce formati multimediali adattivi, ad esempio DASH e HLS, in un browser, senza usare plug-in o Flash. Al contrario, Video.js usa le estensioni MediaSource Open Web Standards e i supporti crittografati. Supporta inoltre la riproduzione video su desktop e dispositivi mobili.

La documentazione ufficiale è reperibile all'indirizzo [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Codice di esempio
Il codice di esempio per questo articolo è disponibile in [Azure-Samples/Media-Services-3rdparty-Player-Samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementare il lettore

1. Creare un `index.html` file in cui si ospiterà il lettore. Aggiungere le righe di codice seguenti (è possibile sostituire le versioni più recenti, se applicabile):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Aggiungere un `index.js` file con il codice seguente:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Sostituire `manifestUrl` con l'URL HLS o Dash dal localizzatore di streaming dell'asset che si trova nella pagina del localizzatore di streaming nella portale di Azure.
    ![URL di streaming Locator](media/how-to-shaka-player/streaming-urls.png)

4. Sostituire `protocolType` con le opzioni seguenti:

    - "Application/x-mpegURL" per i protocolli HLS
    - "Application/Dash + XML" per i protocolli DASH

### <a name="set-up-captions"></a>Configurare le didascalie

Eseguire il `addRemoteTextTrack` metodo e sostituire:

- `subtitleKind` con `"captions"` ,, `"subtitles"` `"descriptions"` o `"metadata"`  
- `caption` con il percorso del file con estensione VTT (il file VTT deve trovarsi nello stesso host per evitare l'errore CORS)
- `subtitleLang` con il codice BCP 47 per la lingua, ad esempio `"eng"` per l'inglese o lo `"es"` spagnolo
- `subtitleLabel` con il nome visualizzato della didascalia desiderata

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Configurare l'autenticazione del token

Il token deve essere impostato nel campo autorizzazione dell'intestazione della richiesta. Per evitare problemi con CORS, questo token deve essere impostato solo in tali richieste con `'keydeliver'` nell'URL. Le righe di codice seguenti devono eseguire le operazioni seguenti:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Quindi, la funzione precedente deve essere associata all' `videojs.Hls.xhr.beforeRequest` evento.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Configurare la crittografia AES-128

Video.js supporta la crittografia AES-128 senza alcuna configurazione aggiuntiva. 

> [!NOTE] 
> Attualmente si verifica un [problema](https://github.com/videojs/video.js/issues/6717) con la crittografia e il contenuto di CMAF HLS/Dash, che non sono riproducibili.

### <a name="set-up-drm-protection"></a>Configurare la protezione DRM

Per supportare la protezione DRM, è necessario aggiungere l'estensione ufficiale [videojs-contrib-EME](https://github.com/videojs/videojs-contrib-eme) . Funziona anche una versione della rete CDN.

1. Nel `index.js` file descritto in precedenza, è necessario inizializzare l'estensione EME aggiungendo `videoJS.eme();` *prima* di aggiungere l'origine del video:

   ```javascript
    videoJS.eme();
   ```

2. A questo punto è possibile definire gli URL dei servizi DRM e gli URL delle licenze corrispondenti come segue:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Acquisizione dell'URL della licenza

Per acquisire l'URL della licenza, è possibile:

- Consultare la configurazione del provider DRM
- in alternativa, se si usa l'esempio, consultare il `output.json` documento generato quando è stato eseguito lo script *setup-vod.ps1* PowerShell per VODs o lo script *start-live.ps1* per i flussi live. Sono disponibili anche i figli all'interno di questo file.

#### <a name="using-tokenized-drm"></a>Uso di DRM in formato token

Per supportare la protezione DRM in formato token, è necessario aggiungere la riga seguente alla `src` proprietà del lettore:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare il Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Avvio rapido: Crittografare il contenuto](encrypt-content-quickstart.md)
