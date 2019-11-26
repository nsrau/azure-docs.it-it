---
title: Aggiungere un livello per le immagini in Mappe di Azure | Microsoft Docs
description: How to add an Image Layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480488"
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

The image layer supports the following image formats:

- JPEG
- PNG
- BMP
- GIF (no animations)

## <a name="add-an-image-layer"></a>Aggiungere un livello per le immagini

In the following code overlays an image of a [map of Newark New Jersey from 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) on the map. An [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) is created by passing a URL to an image and coordinates for the four corners in the format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le immagini' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Penna <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Livello semplice per le immagini</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importare una sovrapposizione di terra KML

In questo esempio viene illustrato come sovrapporre informazioni di sovrapposizione terra KML come livello immagine sulla mappa. KML ground overlays provide north, south, east, and west coordinates and a counter-clockwise rotation, whereas the image layer expects coordinates for each corner of the image. La sovrapposizione di terra KML in questo esempio è la cattedrale di Chartres e proviene da [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

The following code uses the static `getCoordinatesFromEdges` function of the [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) class to calculate the four corners of the image from the north, south, east, west and rotation information from the KML ground overlay.

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