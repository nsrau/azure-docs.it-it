---
title: Ottenere dati dalle forme su una mappa . Mappe di Microsoft Azure
description: In questo articolo viene illustrato come ottenere i dati delle forme disegnati su una mappa usando Il Web SDK di Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334403"
---
# <a name="get-shape-data"></a>Ottenere i dati della forma

In questo articolo viene illustrato come ottenere dati di forme disegnate sulla mappa. Usiamo la funzione **drawingManager.getSource()** all'interno di [Gestione disegni](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Ci sono vari scenari in cui si desidera estrarre i dati geojson di una forma disegnata e usarlo altrove.  


## <a name="get-data-from-drawn-shape"></a>Ottenere dati dalla forma disegnataGet data from drawn shape

La funzione seguente ottiene i dati di origine della forma disegnata e li restituisce alla schermata. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Di seguito è riportato l'esempio di codice in esecuzione completo, in cui è possibile disegnare una forma per testare la funzionalità:Below is the complete running code sample, where you can draw a shape to test the functionality:

<br/>

<iframe height="686" title="Ottenere i dati della forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la penna Ottenere i<a href='https://codepen.io/azuremaps'>@azuremaps</a>dati della <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>forma</a> di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare le funzioni aggiuntive del modulo degli strumenti di disegno:

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
