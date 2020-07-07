---
title: Demo di Azure Media Player
description: Questa pagina contiene un elenco di collegamenti a demo del Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: article
ms.date: 04/24/2020
ms.openlocfilehash: 584748b23f526e6f03b543b8298927e3f202f743
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82139303"
---
# <a name="azure-media-player-demos"></a>Demo di Azure Media Player

Di seguito è riportato un elenco di collegamenti a demo del Azure Media Player. È possibile scaricare tutti gli [esempi di Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples) da GitHub.

## <a name="demo-listing"></a>Elenco di demo

| Nome esempio | Programmazione tramite JavaScript | Static tramite l'elemento video HTML5 | Descrizione |
| ------------|----------------------------|-------------------------------------|--------------|
| Basic |
| Imposta origine | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_setsource.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_setsource.html) |Riprodurre contenuto non protetto.|
| Funzionalità |
| Inserimento di annunci VOD-VAST | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_vast_ads_vod.html) | N/D | Inserisci annunci incrociati pre-mid e post-roll in un asset VOD. |
| Playback Speed | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playback_speed.html)| N/D | Consente ai visualizzatori di controllare la velocità con cui il video viene controllato. |
| Interfaccia di svuotamento AMP | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_flush_skin.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_flush_skin.html) | Abilita la nuova interfaccia AMP. **Nota:** Lo svuotamento AMP è supportato solo nelle versioni AMP 2.1.0 + |
| Didascalie e sottotitoli | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_webvtt.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_webvtt.html) | Riproduzione con sottotitoli WebVTT.
| Sottotitoli Live CEA 708 | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_live_captions.html) | N/D | Riproduzione con didascalie in ingresso Live CEA 708 con le didascalie allineate a sinistra. |
| Streaming con fallback progressivo | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveFallback.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveFallback.html) | Configurazione di base della riproduzione adattiva con fallback per la modalità progressiva se il flusso non è supportato nella piattaforma. |
| MP4 video progressivo | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveVideo.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveVideo.html) | Riproduzione di MP4 audio progressivo. |
| MP3 progressivo audio | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_progressiveAudio.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_progressiveAudio.html) | Riproduzione di audio MP3 progressivo. |
| DD + | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_dolbyDigitalPlus.html) | N/D | Riproduzione del contenuto con DD + audio. |
| Opzioni |
| Profilo euristico | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_heuristicsProfile.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_heuristicsProfile.html) | Modifica del profilo euristico |
| Localizzazione | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_localization.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_localization.html) |
Impostazione della localizzazione |
| Menu tracce audio | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiAudio.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_multiAudio.html) |
Opzioni per mostrare come visualizzare il menu tracce audio sull'interfaccia predefinita. |
| Tasti | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_hotKeys.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_hotKeys.html) | Questo esempio illustra come configurare i tasti di scelta rapida abilitati nel lettore |
| Eventi, registrazione e diagnostica |
| Registra eventi | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_registerEvents.html) | N/D | Riproduzione con listener di eventi. |
| Registrazione | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_logging.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_logging.html) | Attivazione della registrazione dettagliata alla console. |
| Diagnostica | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_diagnostics.html) | N/D | Recupero dei dati di diagnostica. Questo esempio funziona solo in alcuni tecnici. |
| AES |
| Nessun token AES | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_notoken.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_notoken.html) | Riproduzione del contenuto AES senza token. |
| Token AES | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token.html) | Riproduzione del contenuto AES con token. |
| Simulazione di proxy HLS AES | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_withHLSProxy.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_withHLSProxy.html) | Riproduzione del contenuto AES con token, che mostra un proxy per HLS, in modo che il token possa essere usato con i dispositivi iOS. |
| Flash Force token AES | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_aes_token_forceFlash.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_aes_token_forceFlash.html) | Riproduzione di contenuto AES con token, forzando la tecnologia Flash. |
| DRM |
| DRM multiplo con PlayReady, Widevine e FairPlay | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_multiDRM_PlayReadyWidevineFairPlay_notoken.html) | Riproduzione di contenuto DRM senza token, con intestazioni PlayReady, Widevine e FairPlay. |
| Nessun token PlayReady | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken.html) | Riproduzione del contenuto di PlayReady senza token. |
| PlayReady no token Force Silverlight | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_notoken_forceSilverlight.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_notoken_forceSilverlight.html) | Riproduzione di contenuto PlayReady senza token, forzando la tecnologia silverlighti. |
| Token PlayReady | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token.html) | Riproduzione del contenuto PlayReady con token. |
| PlayReady token Force Silverlight | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_playready_token_forceSilverlight.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_playready_token_forceSilverlight.html) | Riproduzione del contenuto di PlayReady con token, forzando la tecnologia silverlighti. |
| Protocollo e tecnologia |
| Modifica techOrder | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_techOrder.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_techOrder.html) | Modifica dell'ordine tecnico |
| Forza MPEG-DASH solo in UrlRewriter | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceDash.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_forceDash.html) | Riproduzione di contenuto non protetto solo tramite il protocollo DASH. |
| Escludi MPEG-DASH in UrlRewriter | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_forceNoDash.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_forceNoDash.html) | Riproduzione di contenuto non protetto solo tramite i protocolli Smooth e HLS. |
| Criteri di recapito multipli | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_multipleDeliveryPolicy.html) | [Statico](https://amp.azure.net/libs/amp/latest/samples/videotag_multipleDeliveryPolicy.html) | Impostazione dell'origine con contenuto con più criteri di recapito da servizi multimediali di Azure |
| A livello Select |
| Selezionare la traccia del testo | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectTextTrack.html) | N/D | Selezione di una traccia WebVTT dall'elenco Track. |
| Selezione velocità in bit | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectBitrate.html) | N/D | Selezione di una velocità in bit dall'elenco di velocità in bit. Questo esempio funziona solo in alcuni tecnici. |
| Selezionare il flusso audio | [Dinamico](https://amp.azure.net/libs/amp/latest/samples/dynamic_selectAudioStream.html) | N/D | Selezione di un flusso audio dall'elenco di flussi audio disponibili. Questo esempio funziona solo in alcuni tecnici. |

## <a name="next-steps"></a>Passaggi successivi

<!---Some context for the following links goes here--->
- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)