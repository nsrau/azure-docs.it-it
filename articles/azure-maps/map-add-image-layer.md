---
title: Aggiungere un livello per le immagini in Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello immagine a Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6c43ccaee473eca701d15a5a83f84814d65c6b7c
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976154"
---
# <a name="add-an-image-layer-to-a-map"></a>Aggiungere un livello per le immagini a una mappa

Questo articolo illustra come è possibile sovrapporre un'immagine a un set fisso di coordinate nella mappa. Esistono molti scenari in cui viene eseguita la sovrapposizione di un'immagine sulla mappa. Di seguito sono riportati alcuni esempi del tipo di immagini che vengono spesso sovrapposte sulle mappe:

* Immagini acquisite dai droni.
* Piantine di compilazione.
* Immagini di mappe cronologiche o specializzate di altro tipo.
* Progetti di siti di processi.
* Immagini di radar meteo.

> [!TIP]
> Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) (livello per le immagini) è un modo rapido e facile di sovrapporre un'immagine a una mappa. Tuttavia, se l'immagine è grande, il browser potrebbe caricarla con difficoltà. In questo caso, prendere in considerazione di suddividere l'immagine in riquadri e caricarli nella mappa come [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) (livello per i riquadri).

## <a name="add-an-image-layer"></a>Aggiungere un livello per le immagini

Nel codice seguente viene sovrapposta un'immagine di una [mappa di Newark New Jersey da 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa. Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) viene creato passando un URL a un'immagine e le coordinate per i quattro angoli nel formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le immagini' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Livello semplice per le immagini</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importare una sovrapposizione di terra KML

In questo esempio viene illustrato come sovrapporre informazioni di sovrapposizione terra KML come livello immagine sulla mappa. Le sovrapposizioni di base KML forniscono coordinate Nord, Sud, est e ovest e una rotazione in senso antiorario, mentre il livello immagine prevede coordinate per ogni angolo dell'immagine. La sovrapposizione di terra KML in questo esempio è la cattedrale di Chartres e proviene da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Nel codice seguente viene utilizzata la `getCoordinatesFromEdges` funzione statica della classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) per calcolare i quattro angoli dell'immagine dalle informazioni Nord, Sud, est, ovest e di rotazione della sovrimpressione di base KML.

<br/>

<iframe height='500' scrolling='no' title='Sovrapposizione di terra KML come livello per le immagini' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sovrapposizione di terra KML come livello per le immagini</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalizzare un livello per le immagini

Il livello per le immagini ha molte opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le immagini' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opzioni del livello per le immagini</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i riquadri](./map-add-tile-layer.md)