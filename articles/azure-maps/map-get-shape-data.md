---
title: Ottenere dati da forme su una mappa | Mappe Microsoft Azure
description: In questo articolo viene illustrato come ottenere i dati di forma disegnati su una mappa utilizzando il Microsoft Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 63d947b85e75e3809445c5bc65577aeaed38caa1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209665"
---
# <a name="get-shape-data"></a>Ottenere i dati della forma

Questo articolo illustra come ottenere dati di forme disegnate sulla mappa. Si usa la funzione **drawingManager. GetSource ()** all'interno di [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Esistono diversi scenari in cui si desidera estrarre i dati GeoJSON di una forma disegnata e utilizzarli altrove.  


## <a name="get-data-from-drawn-shape"></a>Recupera dati dalla forma disegnata

La funzione seguente ottiene i dati di origine della forma disegnata e li restituisce sullo schermo. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Di seguito è riportato l'esempio di codice completo in esecuzione, in cui è possibile disegnare una forma per testare la funzionalità:

<br/>

<iframe height="686" title="Ottenere i dati della forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>dati delle forme</a> per la penna Get di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Reagire agli eventi di disegno](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e tasti di scelta rapida](drawing-tools-interactions-keyboard-shortcuts.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
