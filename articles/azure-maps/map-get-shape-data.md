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
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309772"
---
# <a name="get-shape-data"></a>Ottenere i dati di forma

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

<iframe height="686" title="Ottenere i dati di forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>dati di forma</a> di penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>Get di Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
