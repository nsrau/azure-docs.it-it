---
title: Scrittura di plug-in per Azure Media Player
description: Scopri come scrivere un plugin con Azure Media Player con JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727112"
---
# <a name="writing-plugins-for-azure-media-player"></a>Scrittura di plug-in per Azure Media Player #

Un plugin è JavaScript scritto per estendere o migliorare il lettore. È possibile scrivere plug-in che modificano l'aspetto di Azure Media Player, la sua funzionalità o anche interfacciarlo con altri servizi. È possibile eseguire questa operazione in due semplici passaggi:You can do this in two easy steps:

## <a name="step-1"></a>Passaggio 1 ##

Scrivi il tuo JavaScript in una funzione del genere:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Puoi scrivere il codice direttamente nella `<script>` tua pagina HTML all'interno di tag o in un file JavaScript esterno. Se si esegue quest'ultimo, assicurarsi di `<head>` includere il file JavaScript nella pagina HTML *dopo* lo script AMP.

Esempio:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Passaggio 2 ##
Inizializzare il plug-in con JavaScript in uno dei due modi seguenti:

Metodo 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Metodo 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Le opzioni del plugin non sono necessarie, includendole consente solo agli sviluppatori che utilizzano il tuo plugin di configurare il suo comportamento senza dover cambiare il codice sorgente.

Per ispirare e altri esempi sulla creazione di un plugin date un'occhiata alla nostra [galleria](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Il codice del plug-in modifica dinamicamente gli elementi nel DOM durante la durata dell'esperienza del giocatore dello spettatore, non apporta mai modifiche permanenti al codice sorgente del giocatore. Questo è dove la comprensione degli strumenti di sviluppo del browser è utile. Ad esempio, se desideri modificare l'aspetto di un elemento nel lettore, puoi trovare il suo elemento HTML in base al nome della classe e quindi aggiungere o modificare gli attributi da lì. Ecco un'ottima risorsa per la modifica degli [attributi HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Plugin integrati ###

 Ci sono attualmente due plugin al forno in AMP: il [time-tip](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) e [tasti di scelta rapida](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Questi plugin sono stati originariamente sviluppati per essere plugin modulari per il giocatore, ma ora sono inclusi nel codice sorgente del giocatore.

### <a name="plugin-gallery"></a>Galleria plugin ###

La galleria di [plugin](http//:aka.ms/ampplugins) ha diversi plugin che la comunità ha già contribuito per caratteristiche come marcatori di linea temporale, zoom, analisi e altro ancora. La pagina fornisce gli accessi ai plugin e le istruzioni su come configurarlo, nonché una demo che mostra il plugin in azione. Se crei un plugin fresco che pensi debba essere incluso nella nostra galleria, sentiti libero di inviarlo in modo che possiamo controllarlo.

## <a name="next-steps"></a>Passaggi successivi ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)