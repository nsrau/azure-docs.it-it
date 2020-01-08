---
title: Ottenere dati di forme da gestione disegno in mappe di Azure | Microsoft Docs
description: Come ottenere dati di forma con Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bc8efe46453f76d353842b32bd52b41838979b49
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432820"
---
# <a name="get-shape-data"></a>Ottenere i dati della forma

Questo articolo illustra come ottenere i dati di forme disegnati sulla mappa usando la funzione [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **DrawingManager. GetSource ()** . Possono essere presenti diversi scenari in cui è possibile estrarre i dati GeoJSON di una forma disegnata e utilizzarli altrove.  


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
