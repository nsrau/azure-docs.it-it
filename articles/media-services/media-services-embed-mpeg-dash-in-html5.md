---
title: Integrazione di uno streaming video adattivo MPEG-DASH in un'applicazione HTML5 con DASH.js | Microsoft Docs
description: Questo argomento illustra come integrare un video in streaming adattivo MPEG-DASH in un'applicazione HTML5 con DASH.js.
author: Juliako
manager: SyntaxC4
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 35ba9161f70a27a215685830d1a9e7c1881cc3bb
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Integrazione di uno streaming video adattivo MPEG-DASH in un'applicazione HTML5 con DASH.js
## <a name="overview"></a>Overview
MPEG-DASH è uno standard ISO per lo streaming adattivo di contenuti video che offre vantaggi significativi agli utenti che desiderano distribuire output di streaming video adattivo di alta qualità. Con MPEG-DASH il flusso video viene automaticamente impostato a una definizione inferiore quando si verificano situazioni di congestione sulla rete. Questo riduce il rischio che il video si interrompa mentre il lettore scarica i secondi successivi da riprodurre (noto anche come buffering). Man mano che la congestione sulla rete si riduce, il lettore video torna a un flusso di qualità elevata. La possibilità di adattare la larghezza di banda richiesta riduce anche i tempi di avvio del video. I primi secondi, ad esempio, possono essere riprodotti con una qualità inferiore, rapida da scaricare, per poi passare a una qualità superiore nel momento in cui nel buffer è stato memorizzato contenuto sufficiente.

Dash.js è un lettore video MPEG-DASH open source scritto in JavaScript. È stato sviluppato per offrire un affidabile lettore multipiattaforma che possa essere liberamente riusato in applicazioni che richiedono la riproduzione video. Offre funzionalità di riproduzione MPEG-DASH in qualsiasi browser che supportalo standard W3C Media Source Extensions (MSE), ovvero Chrome,  Microsoft Edge e IE11 (altri browser hanno manifestato l'intenzione di supportare MSE). Per altre informazioni su DASH.js, vedere la pagina relativa al repository dash.js di GitHub.

## <a name="creating-a-browser-based-streaming-video-player"></a>Creazione di un lettore di streaming video basato su browser
Per creare una pagina Web semplice che visualizzi un lettore video con i controlli previsti, quali riproduzione, pausa, riavvolgimento e così via, sarà necessario:

1. Creare una pagina HTML
2. Aggiungere un tag video
3. Aggiungere il lettore dash.js
4. Inizializzare il lettore
5. Aggiungere lo stile CSS
6. Visualizzare i risultati in un browser che implementa MSE

L'inizializzazione del lettore può essere eseguita con poche righe di codice JavaScript. Usando dash.js, integrare video MPEG-DASH in applicazioni basate su browser è davvero molto semplice.

## <a name="creating-the-html-page"></a>Creazione della pagina HTML
Il primo passaggio consiste nel creare una pagina HTML standard contenente l'elemento **video** e salvare il file come basicPlayer.html, come illustrato nell'esempio seguente:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

## <a name="adding-the-dashjs-player"></a>Aggiunta del lettore DASH.js
Per aggiungere l'implementazione di riferimento dash.js all'applicazione, è necessario recuperare il file dash.all.js dalla versione 1.0 del progetto dash.js. Il file deve essere quindi salvato nella cartella JavaScript dell'applicazione. Si tratta di un file di riferimento che raccoglie tutto il codice dash.js necessario in un unico file. Esaminando l'archivio dash.js si potrà identificare i singoli file, il codice di test e molto altro, ma se si deve solo usare il file dash.js, questo sarà l'unico elemento effettivamente necessario.

Per aggiungere il lettore dash.js a un'applicazione, aggiungere un tag di script alla sezione di intestazione del file basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Successivamente, creare una funzione per inizializzare il lettore al caricamento della pagina. Aggiungere lo script seguente dopo la riga in cui si carica dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Questa funzione crea in primo luogo un elemento DashContext, che consente di configurare l'applicazione per un ambiente di runtime specifico. Da un punto di vista tecnico, definisce le classi che il framework di inserimento delle dipendenze dovrà usare durante la creazione dell'applicazione. Nella maggior parte dei casi, si userà Dash.di.DashContext.

Successivamente, inizializza la classe primaria del framework dash.js, MediaPlayer. Questa classe contiene i principali metodi necessari, ad esempio riproduzione e pausa, e gestisce sia la relazione con l'elemento video sia l'interpretazione del file Media Presentation Description (MPD) che descrive il video da riprodurre.

Per verificare che il lettore sia pronto per la riproduzione del video, viene chiamata la funzione startup() della classe MediaPlayer. Tra le altre cose, questa funzione controlla anche che siano state caricate tutte le classi necessarie (come definito dal contesto). Quando il lettore è pronto, è possibile collegare l'elemento video mediante la funzione attachView(). In questo modo, MediaPlayer può inserire il flusso video nell'elemento e, se necessario, controllare la riproduzione.

Passare l'URL del file MPD a MediaPlayer in modo che riconosca il video previsto per la riproduzione. Quando la pagina risulterà completamente caricata, sarà necessario eseguire la funzione setupVideo() appena creata. A questo scopo, è possibile usare l'evento onload dell'elemento del corpo. Modificare l'elemento <body> come segue:

    <body onload="setupVideo()">

Infine, imposta la dimensione dell'elemento video usando lo stile CSS. In un ambiente di streaming adattivo, questo passaggio è particolarmente importante poiché le dimensioni del video riprodotto possono variare mentre la riproduzione si adatta alle mutevoli condizioni di rete. Questo semplice esempio forza l'elemento video all'80% della finestra del browser disponibile aggiungendo lo stile CSS seguente alla sezione di intestazione della pagina:

    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

## <a name="playing-a-video"></a>Riproduzione di un video
Per riprodurre un video, passare nel browser al file basicPlayback.html e fare clic sul pulsante di riproduzione sul lettore video visualizzato.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Vedere anche
[Sviluppo di applicazioni di lettore video](media-services-develop-video-players.md)

[Repository dash.js di GitHub](https://github.com/Dash-Industry-Forum/dash.js) 


