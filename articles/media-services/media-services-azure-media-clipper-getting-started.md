---
title: Introduzione ad Azure Media Clipper | Microsoft Docs
description: Introduzione ad Azure Media Clipper, uno strumento per creare clip multimediali dalle risorse
services: media-services
keywords: clip;clip secondaria;codifica;multimediale
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
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
- `language` {FACOLTATIVO, stringa}: imposta la lingua del widget. Se non specificato, il widget tenta di localizzare i messaggi in base alla lingua del browser. Se non individua la lingua del browser, il widget usa l'impostazione predefinita, che è l'inglese. Per altre informazioni, vedere la sezione Lingue supportate.
- `languages` {FACOLTATIVO, JSON}: questo parametro sostituisce il dizionario predefinito di lingue con un dizionario personalizzato definito dall'utente. Per altre informazioni, vedere la sezione Lingue supportate.
- `extraLanguages` (FACOLTATIVO, JSON): questo parametro aggiunge nuove lingue al dizionario predefinito. Per altre informazioni, vedere la sezione Lingue supportate.

## <a name="typescript-definition"></a>Definizione TypeScript
Un file di definizione [TypeScript](https://www.typescriptlang.org/) per Clipper è disponibile [qui](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>API di Azure Media Clipper
Questa sezione illustra la superficie dell'API disponibile in Clipper.

- `load(assets)`: carica un elenco di asset nel riquadro degli asset (non deve essere usato insieme a `assetsPanelLoaderCallback`). Per informazioni dettagliate su come caricare gli asset in Clipper, vedere questo [articolo](media-services-azure-media-clipper-load-assets.md).
- `setLogLevel(level)`: imposta il livello di registrazione da visualizzare nella console del browser. I valori possibili sono: `info`, `warn` e `error`.
- `setHeight(height)`: imposta l'altezza totale del widget in pixel. L'altezza minima è di 600 px senza riquadro degli asset e di 850 px con il riquadro degli asset.
- `version`: ottiene la versione del widget.

## <a name="configuring-azure-media-clipper"></a>Configurazione di Azure Media Clipper
Per la configurazione di Azure Media Clipper, vedere i passaggi successivi:
- [Loading assets into Azure Media Clipper](media-services-azure-media-clipper-load-assets.md) (Caricamento di asset in Azure Media Clipper)
- [Configurare scelte rapide da tastiera personalizzate](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Submitting clipping jobs from the Clipper](media-services-azure-media-clipper-submit-job.md) (Invio di processi di creazione clip da Clipper)

## <a name="supported-languages"></a>Lingue supportate
Il widget Clipper è disponibile in 18 lingue. Per impostare la lingua del widget, è necessario definire il parametro `language` durante l'inizializzazione. Passare la stringa di codice della lingua desiderata dall'elenco seguente:
- Cinese (semplificato): zh-hans
- Cinese (tradizionale): zh-hant
- Ceco: cs
- Olandese, fiammingo: nl
- Inglese: en
- Francese: fr
- Tedesco: de
- Ungherese: hu
- Italiano: it
- Giapponese: ja
- Coreano: ko
- Polacco: pl
- Portoghese (Brasile): pt-br
- Portoghese (Portogallo): pt-pt
- Russo: ru
- Spagnolo: es
- Svedese: sv
- Turco: tr

Per impostare un dizionario di lingua personalizzato o estendere il dizionario di lingua predefinito, è necessario definire rispettivamente il parametro`languages` o il parametro `extraLanguages`. Passare un dizionario personalizzato usando il formato JSON seguente:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

L'esempio seguente definisce le stringhe localizzate in lingua inglese:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```