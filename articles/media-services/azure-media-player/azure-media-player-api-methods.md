---
title: Azure Media Player API Methods
description: L'API di Azure Media Player consente di interagire con il video tramite JavaScript, sia che il browser stia riproducendo il video tramite video HTML5, Flash, Silverlight o qualsiasi altra tecnologia di riproduzione supportata.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727268"
---
# <a name="api"></a>API #

L'API di Azure Media Player consente di interagire con il video tramite JavaScript, sia che il browser stia riproducendo il video tramite video HTML5, Flash, Silverlight o qualsiasi altra tecnologia di riproduzione supportata.

## <a name="referencing-the-player"></a>Riferimento al giocatore ##

Per utilizzare le funzioni API, è necessario accedere all'oggetto lettore. Per fortuna è facile da ottenere. Devi solo assicurarti che il tag video abbia un ID. Il codice di incorporamento `vid1`di esempio ha un ID di . Se hai più video su una pagina, assicurati che ogni tag video abbia un ID univoco.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Se il lettore non è stato ancora inizializzato tramite l'attributo di impostazione dei dati o un altro metodo, questo inizializzerà anche il lettore.

## <a name="wait-until-the-player-is-ready"></a>Attendere che il giocatore sia pronto ##

Il tempo necessario ad Azure Media Player per configurare il video e l'API varia a seconda della tecnologia di riproduzione in uso. HTML5 sarà spesso molto più veloce da caricare rispetto a Flash o Silverlight. Per questo motivo, la funzione 'ready' del giocatore deve essere utilizzata per attivare qualsiasi codice che richiede l'API del giocatore.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Metodi dell'API ##

Ora che hai accesso a un lettore pronto, puoi controllare il video, ottenere valori o rispondere agli eventi video. I nomi delle funzioni dell'API di Azure Media Player tentano di seguire [l'API multimediale HTML5.](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html) La differenza principale è che le funzioni getter/setter vengono utilizzate per le proprietà video.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrazione per eventi ##
Gli eventi devono essere registrati direttamente dopo l'inizializzazione del lettore per la prima volta per garantire che tutti gli eventi siano segnalati in modo appropriato all'applicazione e devono essere eseguiti al di fuori dell'evento ready.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Passaggi successivi ##

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)