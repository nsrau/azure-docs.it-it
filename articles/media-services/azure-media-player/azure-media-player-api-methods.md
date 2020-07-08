---
title: Metodi API Azure Media Player
description: L'API Azure Media Player consente di interagire con il video tramite JavaScript, se il browser sta eseguendo il video tramite HTML5 video, Flash, Silverlight o qualsiasi altra tecnologia di riproduzione supportata.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727268"
---
# <a name="api"></a>API #

L'API Azure Media Player consente di interagire con il video tramite JavaScript, se il browser sta eseguendo il video tramite HTML5 video, Flash, Silverlight o qualsiasi altra tecnologia di riproduzione supportata.

## <a name="referencing-the-player"></a>Riferimento al lettore ##

Per usare le funzioni API, è necessario accedere all'oggetto Player. Fortunatamente è facile da ottenere. È sufficiente assicurarsi che il tag video abbia un ID. Il codice di incorporamento di esempio ha un ID `vid1` . Se in una pagina sono presenti più video, assicurarsi che ogni tag video abbia un ID univoco.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Se il lettore non è stato ancora inizializzato tramite l'attributo di installazione dati o un altro metodo, verrà inizializzato anche il lettore.

## <a name="wait-until-the-player-is-ready"></a>Attendere che il lettore sia pronto ##

Il tempo necessario Azure Media Player per configurare il video e l'API variano a seconda della tecnologia di riproduzione utilizzata. HTML5 sarà spesso più veloce da caricare rispetto a Flash o Silverlight. Per questo motivo, è necessario usare la funzione ' Ready ' del giocatore per attivare qualsiasi codice che richiede l'API del lettore.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OPPURE

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Metodi API ##

Ora che si ha accesso a un lettore pronto, è possibile controllare il video, ottenere i valori o rispondere agli eventi video. I nomi delle funzioni API Azure Media Player tentano di seguire l' [API del supporto HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). La differenza principale consiste nel fatto che le funzioni getter/setter vengono usate per le proprietà video.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrazione per gli eventi ##
Gli eventi devono essere registrati direttamente dopo l'inizializzazione del lettore per la prima volta per assicurarsi che tutti gli eventi vengano segnalati in modo appropriato all'applicazione e che vengano eseguiti al di fuori dell'evento pronto.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Passaggi successivi ##

<!---Some context for the following links goes here--->
- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)