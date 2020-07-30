---
title: Opzioni di Azure Media Player
description: Il Azure Media Player codice di incorporamento è semplicemente un tag video HTML5, quindi per molte delle opzioni è possibile usare gli attributi di tag standard per impostare le opzioni.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 2b7acb1ad6fbe0beb4d79ee2a833561f0829664c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423032"
---
# <a name="options"></a>Opzioni #

## <a name="setting-options"></a>Opzioni di impostazione ##

Il Azure Media Player codice di incorporamento è semplicemente un tag video HTML5, quindi per molte delle opzioni è possibile usare gli attributi di tag standard per impostare le opzioni.

`<video controls autoplay ...>`

In alternativa, è possibile usare l'attributo di installazione dati per fornire opzioni nel formato [JSON](http://json.org/example.html) . Questo è anche il modo in cui impostare le opzioni che non sono standard per il tag video.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Infine, se non si usa l'attributo di installazione dati per attivare la configurazione del lettore, è possibile passare un oggetto con le opzioni del lettore come secondo argomento nella funzione di installazione di JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Le opzioni nel costruttore vengono impostate solo alla prima inizializzazione prima dell'impostazione dell'origine.  Se si desidera modificare le opzioni nello stesso elemento Azure Media Player inizializzato, è necessario aggiornare le opzioni prima di modificare l'origine. È possibile aggiornare le opzioni in JavaScript usando `myPlayer.options({/*updated options*/});` . Si noti che saranno interessate solo le opzioni modificate, tutte le altre opzioni impostate in precedenza verranno mantenute.

## <a name="individual-options"></a>Singole opzioni ##

> [!NOTE]
>Gli attributi dei tag video possono essere solo true o false (valore booleano), è sufficiente includere l'attributo (nessun segno di uguale) per attivarlo o escluderlo per disattivarlo. Ad esempio, per attivare i controlli: errato `<video controls="true" ...>` , `<video controls ...>` il più grande problema che gli utenti eseguono sta provando a impostare questi valori su false usando false come valore (ad esempio, Controls = "false") che esegue effettivamente l'opposto e imposta il valore su true perché l'attributo è ancora incluso.

### <a name="controls"></a>controls ###

L'opzione Controls imposta un valore che indica se il lettore dispone di controlli con cui l'utente può interagire. Senza controlli l'unico modo per avviare la riproduzione video è con l'attributo AutoPlay o tramite l'API.

`<video controls ...>` o `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se AutoPlay è true, la riproduzione del video verrà avviata non appena viene caricata la pagina (senza alcuna interazione da parte dell'utente).

> [!NOTE]
> Questa opzione non è supportata dai dispositivi mobili, ad esempio Windows Phone, Apple iOS e Android. I dispositivi mobili bloccano la funzionalità AutoPlay per evitare l'utilizzo di piani dati mensili del consumer (spesso costosi). Per avviare il video in questo caso, è necessario un tocco/clic dell'utente.

`<video autoplay ...>`o`{ "autoplay": true }`

### <a name="poster"></a>poster ###
L'attributo poster imposta l'immagine visualizzata prima che inizi la riproduzione del video. Si tratta spesso di un frame del video o di una schermata del titolo personalizzata. Non appena l'utente fa clic su Riproduci, l'immagine non viene più rilasciata.

`<video poster="myPoster.jpg" ...>` o `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

L'attributo width imposta la larghezza di visualizzazione del video.

`<video width="640" ...>` o `{ "width": 640 }`

### <a name="height"></a>altezza ###

L'attributo height Imposta l'altezza di visualizzazione del video.

`<video height="480" ...>` o `{ "height": 480 }`

### <a name="plugins"></a>plugins ###

Il codice JSON dei plug-in determina quali plug-in vengono caricati con tale istanza di AMP consente di configurare le opzioni che possono avere il plug-in.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Per ulteriori informazioni sullo sviluppo e sull'utilizzo di plug-in, vedere [scrittura di plug](azure-media-player-writing-plugins.md) -in

### <a name="other-options"></a>altre opzioni ###

È possibile impostare altre opzioni nel `<video>` tag usando il `data-setup` parametro che accetta JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Questo valore è impostato in modo esplicito su false. Impostando su false, si consentirà il rendering della Azure Media Player interfaccia tra più piattaforme.  Inoltre, contrariamente al nome, il tocco verrà comunque abilitato.

### <a name="fluid"></a>fluida ###

Se si imposta questa opzione su true, l'elemento video avrà la larghezza totale del contenitore padre e l'altezza verrà adattata in modo da adattarsi a un video con proporzioni standard di 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`l'opzione sostituisce `width` le impostazioni e esplicite `height` . Questa opzione è disponibile solo nella versione Azure Media Player `2.0.0` e versioni successive.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed`l'opzione controlla il controllo playbackSpeed e il set di impostazioni della velocità di riproduzione disponibili per l'utente. `playbackSpeed`accetta un oggetto. Per abilitare il controllo della velocità di riproduzione sulla barra di controllo, `enabled` la proprietà dell'oggetto deve essere impostata su true. Esempio di abilitazione della velocità di riproduzione nel markup:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Altre proprietà dell' `playbackSpeed` impostazione sono fornite dall'oggetto [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) .

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

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

L' `staleDataTimeLimitInSec` opzione è un'ottimizzazione che consente di configurare il numero di secondi di dati non aggiornati che si vuole tenere nei buffer di MediaSource. Questa opzione è disabilitata per impostazione predefinita.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

L'impostazione enabled su true consente di visualizzare sottotitoli Live CEA nei flussi live e negli archivi Live. L'attributo Label non è obbligatorio, se non è incluso, il lettore eseguirà il fallback a un'etichetta predefinita.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Questa opzione è disponibile solo in Azure Media Player versione 2.1.1 e successive.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
