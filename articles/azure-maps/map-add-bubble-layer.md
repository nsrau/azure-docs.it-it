---
title: Aggiungere un livello Bubble a una mappa | Mappe Microsoft Azure
description: Informazioni su come eseguire il rendering di punti sulle mappe come cerchi con dimensioni fisse. Vedere come usare Azure Maps Web SDK per aggiungere e personalizzare i livelli Bubble a questo scopo.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 446a70a4399b7ec0e16fed2d33c9ec711acfe200
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310833"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Aggiungere un livello bolle a una mappa

Questo articolo illustra come eseguire il rendering dei dati punto da un'origine dati come livello Bubble su una mappa. I livelli Bubble eseguono il rendering dei punti come cerchi sulla mappa con un raggio fisso di pixel. 

> [!TIP]
> Per impostazione predefinita i livelli bolle eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità di geometria dei punti, impostare la `filter` proprietà del livello su `['==', ['geometry-type'], 'Point']` o `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se si desidera includere anche le funzionalità multipoint.

## <a name="add-a-bubble-layer"></a>Aggiungere un livello per le bolle

Il codice seguente carica una matrice di punti in un'origine dati. Quindi, connette i punti dati a un [livello Bubble](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer). Il livello Bubble esegue il rendering del raggio di ogni bolla con cinque pixel e il colore di riempimento del bianco. E, il colore del tratto blu e una larghezza del tratto di sei pixel. 

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

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Origine dati BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrare etichette con un livello bolle

Questo codice illustra come usare un livello Bubble per eseguire il rendering di un punto sulla mappa. E come usare un livello di simboli per eseguire il rendering di un'etichetta. Per nascondere l'icona del livello di simboli, impostare la `image` proprietà delle opzioni dell'icona su `'none'` .

<br/>

<iframe height='500' scrolling='no' title='Origine dati su più livelli' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalizzare un livello bolle

Il livello bolle include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello bolle' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opzioni del livello bolle</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
