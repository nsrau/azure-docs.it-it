---
title: URL Rewriter di Azure Media Player
description: Azure Media Player riscriverà un determinato URL di Servizi multimediali di Azure per fornire flussi per SMOOTH, DASH, HLS v3 e HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91275558"
---
# <a name="url-rewriter"></a>URL Rewriter #

Per impostazione predefinita, Azure Media Player riscriverà un determinato URL di Servizi multimediali di Azure per fornire i flussi per SMOOTH, DASH, HLS v3 e HLS v4. Se ad esempio l'origine viene specificata come segue, Azure Media Player proverà a riprodurre tutti i protocolli precedenti:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Se tuttavia non si vuole usare l'URL Rewriter, è possibile aggiungere la proprietà `disableUrlRewriter` al parametro. Questo significa che le informazioni passate alle origini vengono passate direttamente al lettore senza modifica.  Ecco un esempio di aggiunta di due origini al lettore, DASH e SMOOTH Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

o

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Inoltre, se si desidera, è possibile specificare i formati di streaming specifici che dovranno essere riscritti da Azure Media Player usando il parametro `streamingFormats`. Le opzioni includono `DASH`, `SMOOTH`, `HLSv3`, `HLSv4`, `HLS`. La differenza tra HLS e HLS v3 e v4 è che il formato HLS supporta la riproduzione di contenuto FairPlay. v3 e v4 non supportano FairPlay. Questa opzione è utile se non sono disponibili criteri di distribuzione per uno specifico protocollo.  Ecco un esempio di protocollo DASH non abilitato con l'asset.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

o

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

I due esempi precedenti possono essere usati in combinazione in più circostanze, in base allo specifico asset in uso.

> [!NOTE]
> Le informazioni sulla protezione Widevine vengono mantenute solo nel protocollo DASH.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)