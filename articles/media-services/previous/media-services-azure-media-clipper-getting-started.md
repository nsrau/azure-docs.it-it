---
title: Introduzione ad Azure Media Clipper | Microsoft Docs
description: Introduzione ad Azure Media Clipper, uno strumento per creare clip video dagli asset di Servizi multimediali di Microsoft Azure
services: media-services
keywords: clip;clip secondaria;codifica;multimediale
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Creare clip con Azure Media Clipper
Questa sezione illustra i passaggi fondamentali per iniziare a usare Azure Media Clipper. Le sezioni che seguono illustrano le specifiche relative alla configurazione di Azure Media Clipper.

- Aggiungere prima di tutto i collegamenti seguenti per Azure Media Player e Azure Media Clipper all'inizio del documento. È consigliabile specificare in modo esplicito una versione di Clipper e di Azure Media Player negli URL. Non usare la versione più recente di queste risorse nell'ambiente di produzione, perché sono soggette a modifiche su richiesta.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Aggiungere quindi le classi seguenti all'elemento div in cui si vuole creare un'istanza di Clipper.

```javascript
<div id="root" class="azure-subclipper" />
```

Se si vuole abilitare il tema scuro, aggiungere la classe dark-skin:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Creare quindi un'istanza di Clipper tramite la chiamata API seguente:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

I parametri per la chiamata al metodo di inizializzazione sono i seguenti:
- `selector` {OBBLIGATORIO, stringa}: selettore CSS dell'elemento HTML corrispondente in cui deve avvenire il rendering del widget.
- `restVersion` {OBBLIGATORIO, stringa}: versione API REST di Servizi multimediali di Azure di destinazione. La versione REST definisce il formato dell'output generato dal widget. È supportata al momento solo la versione 2.0.
- `submitSubclipCallback` {OBBLIGATORIO, promessa}: funzione di callback richiamata quando si fa clic sul pulsante di "invio" del widget. La funzione di callback deve prevedere l'output generato dal widget (una configurazione di processo di rendering o una definizione di filtro). Per altre informazioni, vedere la sezione relativa all'invio di una funzione di callback a clip secondaria.
- `logLevel` {FACOLTATIVO, {'info', 'avviso', 'errore'}}: livello di registrazione da visualizzare nella console del browser. Valore predefinito: errore
- `minimumMarkerGap` {FACOLTATIVO, int}: dimensione minima di una clip secondaria, espressa in secondi. Nota: il valore deve essere maggiore o uguale a 6, che è anche il valore predefinito.
- `singleBitrateMp4Profile` {FACOLTATIVO, oggetto JSON}: profilo mp4 a bitrate singolo da usare per la configurazione del processo di rendering generato dal widget. Se omesso, usa il [profilo MP4 a bitrate singolo predefinito](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {FACOLTATIVO, oggetto JSON}: profilo mp4 a bitrate multiplo da usare per la configurazione del processo di rendering generato dal widget. Se omesso, usa il [profilo mp4 a bitrate multiplo predefinito](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {FACOLTATIVO, oggetto JSON}: consente la personalizzazione delle scelte rapide da tastiera per il widget. Per altre informazioni, vedere l'articolo relativo alla [personalizzazione delle scelte rapide da tastiera](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {FACOLTATIVO, promessa}: funzione di callback richiamata per caricare in modo asincrono una nuova pagina di asset nel riquadro degli asset ogni volta che l'utente scorre il riquadro fino in fondo. Per altre informazioni, vedere l'articolo relativo al callback del caricatore di riquadro Asset.
- `height` {FACOLTATIVO, numero}: altezza totale del widget. L'altezza minima è di 600 px senza riquadro degli asset e di 850 px con il riquadro degli asset.
- `subclippingMode` (FACOLTATIVO, {'all', 'render', 'filter'}): modalità di creazione di clip secondarie consentita. Il valore predefinito è all.
- `filterAssetsTypes` (FACOLTATIVO, booleano): consente di mostrare/nascondere il menu a discesa dei filtri nel riquadro degli asset. Il valore predefinito è true.
- `speedLevels` (FACOLTATIVO, array): consente di impostare livelli di velocità diversi per il lettore video. Per altre informazioni, vedere la [documentazione di Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions).
- `resetOnJobDone` (FACOLTATIVO, booleano): consente a Clipper di reimpostare l'utilità di creazione clip secondarie su uno stato iniziale dopo che l'invio di un processo ha avuto esito positivo.
- `autoplayVideo` (FACOLTATIVO, booleano): consente a Clipper di riprodurre automaticamente il video su caricamento. Il valore predefinito è true.
- `language` {FACOLTATIVO, stringa}: imposta la lingua del widget. Se non specificato, il widget tenta di localizzare i messaggi in base alla lingua del browser. Se non individua la lingua del browser, il widget usa l'impostazione predefinita, che è l'inglese. Per altre informazioni, vedere la sezione [Configurazione della localizzazione](media-services-azure-media-clipper-localization.md).
- `languages` {FACOLTATIVO, JSON}: questo parametro sostituisce il dizionario predefinito di lingue con un dizionario personalizzato definito dall'utente. Per altre informazioni, vedere la sezione [Configurazione della localizzazione](media-services-azure-media-clipper-localization.md).
- `extraLanguages` (FACOLTATIVO, JSON): questo parametro aggiunge nuove lingue al dizionario predefinito. Per altre informazioni, vedere la sezione [Configurazione della localizzazione](media-services-azure-media-clipper-localization.md).

## <a name="typescript-definition"></a>Definizione TypeScript
Un file di definizione [TypeScript](https://www.typescriptlang.org/) per Clipper è disponibile [qui](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API di Azure Media Clipper
Questa sezione illustra la superficie dell'API disponibile in Clipper.

- `ready(handler)`: consente di eseguire JavaScript non appena Clipper è completamente caricato e pronto per essere utilizzato.
- `load(assets)`: carica un elenco di asset nella sequenza temporale del widget (non deve essere usato insieme a assetsPanelLoaderCallback). Per informazioni dettagliate su come caricare gli asset in Clipper, vedere questo [articolo](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)`: imposta il livello di registrazione da visualizzare nella console del browser. I valori possibili sono: `info`, `warn` e `error`.
- `setHeight(height)`: imposta l'altezza totale del widget in pixel. L'altezza minima è di 600 px senza riquadro degli asset e di 850 px con il riquadro degli asset.
- `version`: ottiene la versione del widget.

## <a name="next-steps"></a>Passaggi successivi
Per la configurazione di Azure Media Clipper, vedere i passaggi successivi:
- [Loading assets into Azure Media Clipper](media-services-azure-media-clipper-load-assets.md) (Caricamento di asset in Azure Media Clipper)
- [Configurare scelte rapide da tastiera personalizzate](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Submitting clipping jobs from the Clipper](media-services-azure-media-clipper-submit-job.md) (Invio di processi di creazione clip da Clipper)
- [Configurazione della localizzazione](media-services-azure-media-clipper-localization.md)