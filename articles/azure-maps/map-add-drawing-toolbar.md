---
title: Aggiungere la barra degli strumenti di disegno alla mappa | Mappe Microsoft Azure
description: Come aggiungere una barra degli strumenti di disegno a una mappa usando Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: a156896faff4262d9b8bd3cef10817a5b919749d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086149"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Aggiungere una barra degli strumenti di disegno a una mappa

Questo articolo illustra come usare il modulo strumenti di disegno e visualizzare la barra degli strumenti di disegno sulla mappa. Il controllo [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) aggiunge la barra degli strumenti di disegno sulla mappa. Si apprenderà come creare mappe con solo uno e tutti gli strumenti di disegno e come personalizzare il rendering delle forme di disegno nel gestore del disegno.

## <a name="add-drawing-toolbar"></a>Aggiungere la barra degli strumenti di disegno

Il codice seguente consente di creare un'istanza del gestore di disegno e di visualizzare la barra degli strumenti sulla mappa.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere la barra degli strumenti di disegno" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>barra degli strumenti Aggiungi disegno</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limita le opzioni visualizzate della barra degli strumenti

Il codice seguente consente di creare un'istanza del gestore di disegno e di visualizzare la barra degli strumenti solo con uno strumento di disegno poligono sulla mappa. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere uno strumento di disegno poligono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>aggiungere uno strumento di disegno poligono</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Modificare lo stile di rendering del disegno

Lo stile delle forme disegnate può essere personalizzato recuperando i livelli sottostanti del gestore di disegno utilizzando la `drawingManager.getLayers()` funzione e quindi impostando le opzioni sui singoli livelli. Gli handle di trascinamento visualizzati per le coordinate quando si modifica una forma sono marcatori HTML. Lo stile degli handle di trascinamento può essere personalizzato passando le opzioni del marcatore HTML nelle `dragHandleStyle` `secondaryDragHandleStyle` Opzioni e del gestore del disegno.  

Il codice seguente ottiene i livelli di rendering dal gestore del disegno e modifica le opzioni per modificare lo stile di rendering per il disegno. In questo caso, verrà eseguito il rendering dei punti con un'icona del marcatore blu. Le linee saranno di colore rosso e di quattro pixel. I poligoni avranno un colore di riempimento verde e un contorno arancione. Modifica quindi gli stili dei quadratini di trascinamento in modo che siano icone quadrate. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Di seguito è riportato l'esempio di codice completo dell'esecuzione della funzionalità precedente:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modificare lo stile di rendering del disegno" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere lo <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>stile di rendering del disegno di modifica</a> della penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Ottenere i dati della forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagire agli eventi di disegno](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e scelte rapide da tastiera](drawing-tools-interactions-keyboard-shortcuts.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)
