---
title: Aggiunta di un layer Bolla a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come aggiungere un layer Bubble a una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208557"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Aggiungere un livello bolle a una mappa

In questo articolo viene illustrato come eseguire il rendering dei dati punto da un'origine dati come layer a bolle su una mappa. I livelli a bolle eseguono il rendering dei punti come cerchi sulla mappa con un raggio pixel fisso. 

> [!TIP]
> Per impostazione predefinita i livelli bolle eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il layer in modo che `filter` esegga `['==', ['geometry-type'], 'Point']` solo `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` le feature geometria punto, impostate la proprietà del layer su o se desiderate includere anche feature MultiPoint.

## <a name="add-a-bubble-layer"></a>Aggiungere un livello per le bolle

Il codice seguente carica una matrice di punti in un'origine dati. Quindi, collega i punti dati sono a un [layer a bolle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). Il livello bolla esegue il rendering del raggio di ogni bolla con cinque pixel e un colore di riempimento di bianco. E, un colore di tratto di blu, e una larghezza del tratto di sei pixel. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Origine dati BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrare etichette con un livello bolle

Questo codice mostra come utilizzare un livello bolla per eseguire il rendering di un punto sulla mappa. Inoltre, come utilizzare un livello simbolo per eseguire il rendering di un'etichetta. Per nascondere l'icona del livello `image` simbolo, impostate la `'none'`proprietà delle opzioni dell'icona su .

<br/>

<iframe height='500' scrolling='no' title='Origine dati su più livelli' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalizzare un livello bolle

Il livello bolle include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello bolle' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opzioni del livello bolle</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine datiCreate a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
