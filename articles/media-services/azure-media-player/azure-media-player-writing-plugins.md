---
title: Scrittura di plug-in per Azure Media Player
description: Informazioni su come scrivere un plug-in con Azure Media Player con JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7902dfdf81d8e44921a5218d56effc90f433f02d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857398"
---
# <a name="writing-plugins-for-azure-media-player"></a>Scrittura di plug-in per Azure Media Player #

Un plug-in è scritto da JavaScript per estendere o migliorare il lettore. È possibile scrivere plug-in che modificano l'aspetto di Azure Media Player, la funzionalità o anche l'interfaccia it con altri servizi. Questa operazione può essere eseguita in due semplici passaggi:

## <a name="step-1"></a>Passaggio 1 ##

Scrivere il codice JavaScript in una funzione come segue:

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

È possibile scrivere il codice direttamente nella pagina HTML all'interno `<script>` di tag o in un file JavaScript esterno. Se si esegue quest'ultimo, assicurarsi di includere il file JavaScript nel `<head>` della pagina HTML *dopo* lo script amp.

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

Le opzioni di plug-in non sono necessarie, incluse consentono agli sviluppatori di usare il plug-in per configurarne il comportamento senza dover modificare il codice sorgente.

Per ispirare altri esempi sulla creazione di un plug-in, vedere la [raccolta](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Il codice del plug-in modifica dinamicamente gli elementi nel DOM durante il ciclo di vita dell'esperienza del lettore del visualizzatore, non apporta mai modifiche permanenti al codice sorgente del lettore. Questo è il momento in cui è utile conoscere gli strumenti di sviluppo del browser. Se, ad esempio, si vuole modificare l'aspetto di un elemento nel lettore, è possibile trovare il relativo elemento HTML in base al nome della classe e quindi aggiungere o modificare gli attributi da questa posizione. Ecco una grande risorsa per la [modifica degli attributi HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Plug-in integrati ###

 Attualmente sono disponibili due plug-in, ovvero il suggerimento per i [tempi](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) e i tasti di scelta [rapida](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Questi plug-in sono stati originariamente sviluppati come plug-in modulari per il lettore, ma ora sono inclusi nel codice sorgente del lettore.

### <a name="plugin-gallery"></a>Raccolta di plug-in ###

La [raccolta di plug](https://aka.ms/ampplugins) -in include diversi plug-in che la community ha già contribuito per funzionalità come marcatori di linee temporali, zoom, analisi e altro ancora. La pagina fornisce gli accessi ai plug-in e le istruzioni su come configurarla, oltre a una demo che mostra il plug-in in azione. Se si crea un plug-in interessante che si ritiene debba essere incluso nella raccolta, è possibile inviarlo per poterlo estrarre.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)
