---
title: Modulo degli strumenti di disegno Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come impostare i dati delle opzioni di disegno utilizzando Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334302"
---
# <a name="use-the-drawing-tools-module"></a>Usare il modulo Strumenti di disegno

Azure Maps Web SDK fornisce un modulo per gli strumenti di *disegno.* Questo modulo semplifica il disegno e la modifica delle forme sulla mappa utilizzando un dispositivo di input, ad esempio un mouse o un touch screen. La classe principale di questo modulo è il [gestore di disegni.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) Il gestore di disegni fornisce tutte le funzionalità necessarie per disegnare e modificare le forme sulla mappa. Può essere usato direttamente ed è integrato con un'interfaccia utente della barra degli strumenti personalizzata. È inoltre possibile utilizzare la classe predefinita della barra degli strumenti di [disegno.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Caricamento del modulo degli strumenti di disegno in una pagina Web

1. Creare un nuovo file HTML e [implementare la mappa come di consueto.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Caricare il modulo degli strumenti di disegno di Azure Maps.Load the Azure Maps drawing tools module. È possibile caricarlo in uno dei due modi seguenti:
    - Usare la versione della rete per la distribuzione di contenuti di Azure ospitata a livello globale del modulo dei servizi di Azure Maps.Use the globally hosted, Azure Content Delivery Network version of the Azure Maps services module. Aggiungere un riferimento al foglio di `<head>` stile JavaScript e CSS nell'elemento del file:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - In alternativa, è possibile caricare il modulo degli strumenti di disegno per il codice sorgente di Azure Maps Web SDK in locale usando il pacchetto [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) npm e quindi ospitarlo con l'app. Questo pacchetto include anche le definizioni TypeScript. Usare questo comando:
    
        > **npm installare azure-maps-drawing-tools**
    
        Quindi, aggiungere un riferimento al foglio di `<head>` stile JavaScript e CSS nell'elemento del file:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Utilizzare direttamente il gestore di disegni

Una volta caricato il modulo degli strumenti di disegno nell'applicazione, è possibile abilitare le funzionalità di disegno e modifica utilizzando [Gestione disegni](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). È possibile specificare le opzioni per il gestore del `drawingManager.setOptions()` disegno durante la creazione di un'istanza o in alternativa utilizzare la funzione.

### <a name="set-the-drawing-mode"></a>Impostare la modalità di disegno

Il codice seguente crea un'istanza del gestore di disegni e imposta l'opzione della **modalità** di disegno. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Il codice seguente è un esempio completo di come impostare una modalità di disegno del gestore del disegno. Fare clic sulla mappa per iniziare a disegnare un poligono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Disegnare un poligono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna Disegnare un<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>poligono</a> da Mappe di Azure ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Impostare il tipo di interazione

Il gestore di disegni supporta tre diversi modi di interagire con la mappa per disegnare forme.

* `click`- Le coordinate vengono aggiunte quando si fa clic con il mouse o il tocco.
* `freehand `- Le coordinate vengono aggiunte quando il mouse o il tocco viene trascinato sulla mappa. 
* `hybrid`- Le coordinate vengono aggiunte quando si fa clic o si trascina il mouse o il tocco.

Il codice seguente abilita la modalità di disegno poligono e imposta `freehand`il tipo di interazione del disegno che il gestore di disegni deve rispettare. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Questo esempio di codice implementa la funzionalità di disegno di un poligono sulla mappa. Basta tenere premuto il pulsante sinistro del mouse e trascinarlo in giro, liberamente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Disegno a mano libera" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il disegno a <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>mano</a> libera<a href='https://codepen.io/azuremaps'>@azuremaps</a>della penna di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Personalizzazione delle opzioni di disegno

Negli esempi precedenti è stato illustrato come personalizzare le opzioni di disegno durante la creazione di un'istanza di Gestione disegni. È inoltre possibile impostare le `drawingManager.setOptions()` opzioni di Gestione disegni utilizzando la funzione. Di seguito è riportato uno strumento per testare la personalizzazione di tutte le opzioni per il gestore di disegni utilizzando la funzione setOptions.

<br/>

<iframe height="685" title="Personalizzare Gestione disegni" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la penna Ottenere i<a href='https://codepen.io/azuremaps'>@azuremaps</a>dati della <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>forma</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare le funzioni aggiuntive del modulo degli strumenti di disegno:

> [!div class="nextstepaction"]
> [Aggiungere la barra degli strumenti di disegno](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Ottenere i dati della forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagire agli eventi di disegno](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e scelte rapide da tastiera](drawing-tools-interactions-keyboard-shortcuts.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestione disegni](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti Disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
