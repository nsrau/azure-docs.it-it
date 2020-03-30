---
title: Aggiunta di un layer di estrusione poligono a una mappa Mappe di Microsoft Azure
description: Come aggiungere un livello di estrusione poligonale a Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334553"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Aggiungere un layer di estrusione poligonale alla mappa

In questo articolo viene illustrato come utilizzare il layer `Polygon` di `MultiPolygon` estrusione poligono per eseguire il rendering di aree e feature come forme estruse. Il Web SDK di Azure Maps supporta il rendering delle geometrie Circle come definito nello [schema GeoJSON esteso.](extend-geojson.md#circle) Questi cerchi possono essere trasformati in poligoni quando vengono visualizzati sulla mappa. Tutte le geometrie di entità geografiche possono essere aggiornate facilmente quando avvolte con [l'atlante. Classe Shape.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Utilizzare un livello di estrusione poligono

Connettere il layer di [estrusione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) del poligono a un'origine dati. Quindi, caricarlo sulla mappa. Il livello di estrusione `Polygon` del `MultiPolygon` poligono esegue il rendering delle aree di e delle feature come forme estruse. Le `height` `base` proprietà e del livello di estrusione del poligono definiscono la distanza di base dal suolo e l'altezza della forma estruso in **metri.** Il codice seguente mostra come creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering utilizzando la classe di livello estrusione poligono.

> [!Note]
> Il `base` valore definito nel livello di estrusione del poligono deve essere minore o uguale a quello dell'oggetto `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono estruso" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il poligono di penna <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>estruso</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Aggiungere poligoni basati sui dati

È possibile eseguire il rendering di una mappa con choropleth utilizzando il livello di estrusione del poligono. Impostare `height` `fillColor` le proprietà e del layer di estrusione `Polygon` `MultiPolygon` sulla misura della variabile statistica nelle geometrie e . L'esempio di codice seguente mostra una mappa conopolix estruso degli Stati Uniti basata sulla misurazione della densità di popolazione per stato.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa contusorio estruso" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mappa Choropleth Pen Extruded</a> di Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Mappe di Azure usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi, come indicato [di seguito.](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle) È possibile eseguire il rendering di un cerchio `point` estruso sulla mappa creando una feature `subType` con una proprietà `Circle` e una proprietà numerata `Radius` che rappresenta il raggio in **metri.** Ad esempio:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK `Point` converte `Polygon` queste funzionalità in funzionalità sotto il cofano. È `Point` possibile eseguire il rendering di queste feature sulla mappa utilizzando il layer di estrusione poligonale, come illustrato nell'esempio di codice seguente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono dello spazio aereo dei droni" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>poligono dello spazio</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>aereo Pen Drone di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalizzare un livello di estrusione poligono

Il livello Estrusione poligono ha diverse opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> di<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [strato di estrusione poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Risorse aggiuntive:

> [!div class="nextstepaction"]
> [Estensione della specifica GeoJSON di Azure MapsAzure Maps GeoJSON specification extension](extend-geojson.md#circle)
