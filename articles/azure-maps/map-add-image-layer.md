---
title: Aggiunta di un layer Immagine a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come sovrapporre un'immagine su una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209716"
---
# <a name="add-an-image-layer-to-a-map"></a>Aggiungere un livello per le immagini a una mappa

Questo articolo illustra come sovrapporre un'immagine a un insieme fisso di coordinate. Ecco alcuni esempi di diversi tipi di immagini che possono essere sovrapposti sulle mappe:

* Immagini catturate dai droni
* Piani di costruzione
* Immagini cartole storiche o di altro gruppo specializzate
* Progetti di siti di lavoro
* Immagini radar meteo

> [!TIP]
> Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) è un modo semplice per sovrapporre un'immagine su una mappa. Tieni presente che i browser potrebbero avere difficoltà a caricare un'immagine di grandi dimensioni. In questo caso, è consigliabile suddividere l'immagine in riquadri e caricarli nella mappa come [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Il livello immagine supporta i seguenti formati di immagine:

- JPEG
- PNG
- BMP
- GIF (nessuna animazione)

## <a name="add-an-image-layer"></a>Aggiungere un livello per le immagini

Il codice seguente sovrappone un'immagine di una [mappa di Newark, New Jersey, del 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) sulla mappa. Un [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) viene creato passando un URL a un'immagine e `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`le coordinate per i quattro angoli nel formato .

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

Ecco l'esempio di codice in esecuzione completo del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le immagini' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Livello semplice per le immagini</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importare un file KML come sovrapposizione a terra

In questo esempio viene illustrato come aggiungere informazioni di sovrapposizione a terra KML come livello immagine sulla mappa. Le sovrapposizioni di terreno KML forniscono le coordinate nord, sud, est e ovest e una rotazione in senso antiorario. Ma, il livello dell'immagine si aspetta le coordinate per ogni angolo dell'immagine. La sovrapposizione del terreno KML in questo esempio è per la cattedrale di Chartres, ed è proviene da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Il codice usa `getCoordinatesFromEdges` la funzione statica dalla classe [ImageLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) Calcola i quattro angoli dell'immagine utilizzando le informazioni nord, sud, est, ovest e di rotazione della sovrapposizione del terreno KML.

<br/>

<iframe height='500' scrolling='no' title='Sovrapposizione di terra KML come livello per le immagini' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sovrapposizione di terra KML come livello per le immagini</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Personalizzare un livello per le immagini

Il livello immagine ha molte opzioni di stile. Ecco uno strumento per provarli.

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