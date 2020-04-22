---
title: Azure Media Player Options
description: Il codice di incorporamento di Azure Media Player è semplicemente un tag video HTML5, pertanto per molte delle opzioni è possibile usare gli attributi di tag standard per impostare le opzioni.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727164"
---
# <a name="options"></a>Opzioni #

## <a name="setting-options"></a>Opzioni di impostazione ##

Il codice di incorporamento di Azure Media Player è semplicemente un tag video HTML5, pertanto per molte delle opzioni è possibile usare gli attributi di tag standard per impostare le opzioni.

`<video controls autoplay ...>`

In alternativa, è possibile usare l'attributo data-setup per fornire opzioni nel formato [JSON.](http://json.org/example.html) Questo è anche il modo in cui devi impostare opzioni che non sono standard per il tag video.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Infine, se non si utilizza l'attributo data-setup per attivare la configurazione del lettore, è possibile passare un oggetto con le opzioni del lettore come secondo argomento nella funzione di configurazione di JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Le opzioni nel costruttore vengono impostate solo alla prima inizializzazione prima dell'impostazione dell'origine.  Se si desidera modificare le opzioni nello stesso elemento inizializzato di Azure Media Player, è necessario aggiornare le opzioni prima di modificare l'origine. È possibile aggiornare le opzioni `myPlayer.options({/*updated options*/});`in JavaScript utilizzando . Si noti che saranno interessate solo le opzioni modificate, tutte le altre opzioni impostate in precedenza verranno mantenute.

## <a name="individual-options"></a>Opzioni individuali ##

> [!NOTE]
>Gli attributi dei tag video possono essere solo true o false (booleano), è sufficiente includere l'attributo (nessun segno di uguale) per attivarlo o escluderlo per disattivarlo. Ad esempio, per attivare `<video controls="true" ...>` `<video controls ...>` i controlli: WRONG RIGHT Il problema più grande in cui si incontrano gli utenti è il tentativo di impostare questi valori su false utilizzando false come valore (ad esempio controls "false") che in realtà esegue l'opposto e imposta il valore su true perché l'attributo è ancora incluso.

### <a name="controls"></a>controls ###

L'opzione controls imposta se il lettore dispone o meno di controlli con cui l'utente può interagire. Senza controlli l'unico modo per avviare la riproduzione del video è con l'attributo autoplay o tramite l'API.

`<video controls ...>` o `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se la riproduzione automatica è vera, la riproduzione del video verrà avviata non appena la pagina viene caricata (senza alcuna interazione da parte dell'utente).

> [!NOTE]
> Questa opzione non è supportata da dispositivi mobili come Windows Phone, Apple iOS e Android. I dispositivi mobili bloccano la funzionalità di riproduzione automatica per impedire l'utilizzo eccessivo dei piani dati mensili del consumatore (spesso costosi). In questo caso, è necessario un tocco/clic dell'utente.

`<video autoplay ...>`O`{ "autoplay": true }`

### <a name="poster"></a>Poster ###
L'attributo poster imposta l'immagine che viene visualizzata prima dell'inizio della riproduzione del video. Questo è spesso un fotogramma del video o una schermata del titolo personalizzata. Non appena l'utente fa clic su riproduci l'immagine andrà via.

`<video poster="myPoster.jpg" ...>` o `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

L'attributo width imposta la larghezza di visualizzazione del video.

`<video width="640" ...>` o `{ "width": 640 }`

### <a name="height"></a>height ###

L'attributo height imposta l'altezza di visualizzazione del video.

`<video height="480" ...>` o `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

I plugin JSON determina quali plug-in vengono caricati con l'istanza di AMP consente di configurare tutte le opzioni che il plug-in può avere.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Per ulteriori informazioni sullo sviluppo e l'utilizzo dei plug-in, vedere [scrittura di plug-in](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>altre opzioni ###

Altre opzioni possono essere `<video>` impostate `data-setup` sul tag usando il parametro che accetta un JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>NativeControlsForTouch (controlli nativi) ####

Questa opzione è impostata in modo esplicito su false. Impostando su false, consentirà allo skin di Azure Media Player di essere reso lo stesso tra le piattaforme.  Inoltre, contrariamente al nome, il tocco sarà ancora abilitato.

### <a name="fluid"></a>Fluido ###

Impostando questa opzione su true video element avrà la larghezza completa del contenitore padre e l'altezza verrà regolata per adattarsi a un video con proporzioni standard 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`sovrascrive esplicitamente `width` e `height` le impostazioni. Questa opzione è disponibile solo `2.0.0` nella versione di Azure Media Player e versioni successive.

### <a name="playbackspeed"></a>riproduzioneVelocità ###

`playbackSpeed`controlla il controllo playbackSpeed e l'insieme delle impostazioni della velocità di riproduzione disponibili per l'utente. `playbackSpeed`accetta un oggetto. Per abilitare il controllo della velocità `enabled` di riproduzione sulla barra di controllo, la proprietà dell'oggetto deve essere impostata su true. Un esempio di abilitazione della velocità di riproduzione nel markup:An example of enabling playback speed in markup:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Altre proprietà `playbackSpeed` dell'impostazione sono date dall'oggetto [PlaybackSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

Esempio di impostazione delle opzioni della velocità di riproduzione in JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Questa opzione è disponibile solo in Azure Media Player versione 2.0.0 e successive.

### <a name="staledatatimelimitinsec"></a>StaleDataTimeLimitInSec ###

L'opzione è un'ottimizzazione `staleDataTimeLimitInSec` che consente di configurare quanti secondi di dati non aggiornati si desidera mantenere nei buffer mediaSource. Questa opzione è disabilitata per impostazione predefinita.

### <a name="cea708captionssettings"></a>cea708CaptionsImpostazioni ###

L'impostazione abilitata su true consente di visualizzare i sottotitoli CEA in tempo reale nei live streaming e negli archivi live. L'attributo label non è obbligatorio, se non incluso il giocatore ripiegherà su un'etichetta predefinita.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Questa opzione è disponibile solo in Azure Media Player versione 2.1.1 e successive.

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)