---
title: Tecnologia di riproduzione di Azure Media Player
description: Altre informazioni sulla tecnologia di riproduzione di video o audio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 56fd644e43b704eced4f5a97b82e4b07ab1b4db9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424086"
---
# <a name="playback-technology-tech"></a>Tecnologia di riproduzione #

Per tecnologia di riproduzione si intende la specifica tecnologia del browser o del plug-in usata per riprodurre video o audio.

- **azureHtml5JS**: utilizza gli standard MSE ed EME in combinazione con l'elemento video per la riproduzione di contenuto DASH non basata su plug-in, con il supporto per il contenuto con crittografia envelope AES a 128 bit o per il contenuto con crittografia comune DRM (tramite PlayReady e Widevine se supportato dal browser) da Servizi multimediali di Azure
- **flashSS**: utilizza la tecnologia di Flash Player per riprodurre contenuto Smooth con il supporto per la decrittografia envelope AES a 128 bit da Servizi multimediali di Azure. Richiede la versione 11.4 o superiore di Flash
- **html5FairPlayHLS**: utilizza la tecnologia di riproduzione basata su browser specifica di Safari tramite HLE con l'elemento video. Questa tecnologia è necessaria per riprodurre contenuto protetto tramite FairPlay da Servizi multimediali di Azure ed è stata aggiunta a techOrder a partire dal 19/10/16
- **silverlightSS**: utilizza la tecnologia Silverlight per riprodurre contenuto Smooth con il supporto per contenuto protetto tramite PlayReady da Servizi multimediali di Azure.
- **html5**: utilizza la tecnologia di riproduzione basata su browser con l'elemento video.  Nei dispositivi Apple iOS o Android questa tecnologia consente la riproduzione di flussi HLS con il supporto di base per la crittografia envelope AES a 128 bit o contenuto DRM (tramite FairPlay se supportato dal browser).

## <a name="tech-order"></a>Ordine delle tecnologie ##

Per assicurarsi che l'asset sia riproducibile in un'ampia varietà di dispositivi, è consigliabile seguire l'ordine delle tecnologie seguenti, che costituisce l'impostazione predefinita se è possibile specificare `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` direttamente in `<video>` o a livello di codice nelle opzioni:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

o

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Matrice di compatibilità ##

Dato l'ordine delle tecnologie consigliato per lo streaming di contenuto da Servizi multimediali di Azure, è prevista la seguente matrice di compatibilità per la riproduzione

| Browser        | OS                                                       | Tecnologia prevista (Clear)  | Tecnologia prevista (AES)  | Tecnologia prevista (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | non supportato                |
| Microsoft Edge           | Xbox One<sup>1</sup> (aggiornamento di novembre 2015)                   | azureHtml5JS           | azureHtml5JS         | non supportato                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 e versioni successive    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6 e versioni successive                                                   | html5                  | html5 (nessun token)3    | non supportato                |
| Safari 8 e versioni successive      | OS X Yosemite e versioni successive                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4 e versioni successive<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (nessun token)<sup>3</sup>    | non supportato                |
| Firefox 42 e versioni successive    | Android 5.0 e versioni successive<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | non supportato                |
| IE 8           | Windows                                                  | non supportato          | non supportato        | non supportato                |

<sup>1</sup> Configurazione non supportata né testata, indicata come riferimento per completezza.

<sup>2</sup> La buona riuscita della riproduzione su dispositivi Android richiede una combinazione di funzionalità del dispositivo, supporto della grafica, rendering di codec, supporto del sistema operativo e altro ancora. Poiché Android è una piattaforma open source che consente ai produttori di smartphone di cambiare il sistema operativo Vanilla Android fornito da Google, si è verificata una certa frammentazione nello spazio Android e alcuni dispositivi potrebbero non essere supportati a causa di mancanza di funzionalità. Inoltre, alcuni dispositivi Android non includono il supporto per tutti i codec.  

<sup>3</sup> Nei casi in cui non è disponibile alcun supporto per il token, è possibile usare un proxy per aggiungere questa funzionalità. Per altre informazioni su questa soluzione, vedere il [blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

> [!NOTE]
> Se la tecnologia prevista scelta richiede l'installazione di un plug-in, ad esempio Flash, che non è installato nel computer dell'utente, Azure Media Player continuerà a controllare le funzionalità della tecnologia successiva, in combinazione con i tipi di origine e le informazioni di protezione, nell'elenco di tecnologie. Se ad esempio si prova a visualizzare un evento di streaming on demand non protetto in Safari 8 con OS X Yosemite e sia Flash che Silverlight non sono installati, Azure Media Player selezionerà la tecnologia HTML5 nativa per la riproduzione.<br/><br/>Ogni giorno emergono nuove tecnologie di browser, il che potrebbe influire su questa matrice.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)