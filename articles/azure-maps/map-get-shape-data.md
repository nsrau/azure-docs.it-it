---
title: Ottenere dati da forme su una mappa | Mappe Microsoft Azure
description: In questo articolo viene illustrato come ottenere i dati di forma disegnati su una mappa utilizzando il Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 6d647b81147f56d6eb81fd04cb562e34ac1497dd
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090704"
---
# <a name="get-shape-data"></a>Ottenere i dati della forma

Questo articolo illustra come ottenere dati di forme disegnate sulla mappa. Si usa la funzione **drawingManager. GetSource ()** all'interno di [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Esistono diversi scenari in cui si desidera estrarre i dati GeoJSON di una forma disegnata e utilizzarli altrove.  


## <a name="get-data-from-drawn-shape"></a>Recupera dati dalla forma disegnata

La funzione seguente ottiene i dati di origine della forma disegnata e li restituisce sullo schermo. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Di seguito è riportato l'esempio di codice completo in esecuzione, in cui è possibile disegnare una forma per testare la funzionalità:

<br/>

<iframe height="686" title="Ottenere i dati della forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Vedere la pagina relativa ai <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>dati di forma</a> di penna Get di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Reagire agli eventi di disegno](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e scelte rapide da tastiera](drawing-tools-interactions-keyboard-shortcuts.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
