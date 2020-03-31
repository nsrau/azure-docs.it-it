---
title: Aggiunta di una barra degli strumenti di disegno a una mappa Mappe di Microsoft Azure
description: Come aggiungere una barra degli strumenti di disegno a una mappa usando Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334490"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Aggiungere una barra degli strumenti strumenti di disegno a una mappa

In questo articolo viene illustrato come utilizzare il modulo Strumenti di disegno e visualizzare la barra degli strumenti di disegno sulla mappa. Il controllo [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) aggiunge la barra degli strumenti di disegno sulla mappa. Si apprenderà come creare mappe con uno solo strumenti di disegno e come personalizzare il rendering delle forme di disegno nel gestore di disegni.

## <a name="add-drawing-toolbar"></a>Aggiungere la barra degli strumenti di disegno

Il codice seguente crea un'istanza di Gestione disegni e visualizza la barra degli strumenti sulla mappa.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Di seguito è riportato l'esempio di codice in esecuzione completo delle funzionalità precedenti:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere la barra degli strumenti di disegno" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la barra degli strumenti<a href='https://codepen.io/azuremaps'>@azuremaps</a>di <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>disegno</a> Aggiunta penna da Mappe di Azure ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Limitare le opzioni della barra degli strumenti visualizzate

Il codice seguente crea un'istanza del gestore di disegni e visualizza la barra degli strumenti con solo uno strumento di disegno poligono sulla mappa. 

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

Di seguito è riportato l'esempio di codice in esecuzione completo delle funzionalità precedenti:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggiungere uno strumento di disegno poligono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere La pagina <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Per aggiungere un poligono a un poligono da</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Modificare lo stile di rendering del disegno

Lo stile delle forme disegnate può essere personalizzato recuperando i layer sottostanti del gestore di disegni utilizzando la `drawingManager.getLayers()` funzione e quindi impostando le opzioni sui singoli layer. I quadratini di ridimensionamento visualizzati per le coordinate durante la modifica di una forma sono indicatori HTML. Lo stile delle maniglie di trascinamento può essere `dragHandleStyle` `secondaryDragHandleStyle` personalizzato passando le opzioni dei marcatori HTML nelle opzioni e del gestore del disegno.  

Il codice seguente ottiene i layer di rendering dal gestore di disegni e modifica le relative opzioni per modificare lo stile di rendering per il disegno. In questo caso, i punti verranno renderizzati con un'icona a un indicatore blu. Le linee saranno rosse e larghe quattro pixel. I poligoni avranno un colore di riempimento verde e un contorno arancione. Cambia quindi gli stili delle maniglie di trascinamento in icone quadrate. 

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

Di seguito è riportato l'esempio di codice in esecuzione completo delle funzionalità precedenti:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modificare lo stile di rendering del disegno" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere lo stile di <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>rendering</a> del<a href='https://codepen.io/azuremaps'>@azuremaps</a>disegno Pen Change di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare le funzioni aggiuntive del modulo degli strumenti di disegno:

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
> [Barra degli strumenti Disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Gestione disegni](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
