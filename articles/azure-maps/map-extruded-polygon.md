---
title: Aggiungere un livello di estrusione poligono a una mappa | Mappe Microsoft Azure
description: Come aggiungere un livello di estrusione poligono al Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: eedbbc0126adacc2a9bdc151aa6dbc27c7ba0750
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310255"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Aggiungere un livello di estrusione poligono alla mappa

Questo articolo illustra come usare il livello di estrusione poligono per eseguire il rendering delle aree `Polygon` e delle `MultiPolygon` geometrie delle funzionalità come forme estruse. Azure Maps Web SDK supporta il rendering delle geometrie del cerchio come definito nello [schema GeoJSON esteso](extend-geojson.md#circle). Questi cerchi possono essere trasformati in poligoni quando vengono sottoposti a rendering sulla mappa. Tutte le geometrie della funzionalità possono essere aggiornate facilmente quando viene eseguito il wrapped con l' [Atlante. Classe Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) .

## <a name="use-a-polygon-extrusion-layer"></a>Usare un livello di estrusione poligono

Connettere il [livello di estrusione poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer) a un'origine dati. Quindi, è stato caricato sulla mappa. Il livello di estrusione poligono esegue il rendering delle aree di un `Polygon` e delle `MultiPolygon` funzionalità come forme estruse. Le  `height` `base` proprietà e del livello di estrusione poligono definiscono la distanza di base dalla superficie e dall'altezza della forma estrusa in **metri**. Il codice seguente illustra come creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering usando la classe del livello di estrusione poligono.

> [!Note]
> Il `base` valore definito nel livello di estrusione poligono deve essere minore o uguale a quello di `height` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono estruso" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>poligono estruso</a> di penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Aggiungere poligoni basati sui dati

È possibile eseguire il rendering di una mappa choropleth utilizzando il livello di estrusione poligono. Impostare le `height` `fillColor` proprietà e del livello di estrusione sulla misura della variabile statistica nelle `Polygon` `MultiPolygon` geometrie della funzionalità e. Nell'esempio di codice seguente viene illustrata una mappa choropleth estrusa dell'U. S in base alla misurazione della densità della popolazione per stato.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa choropleth estrusa" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mappa choropleth di Pen estrusa</a> da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Azure Maps usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi come indicato [qui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). È possibile eseguire il rendering di un cerchio estruso sulla mappa creando una `point` funzionalità con una `subType` proprietà di `Circle` e una proprietà numerata `Radius` che rappresenta il raggio in **metri**. Ad esempio:

```javascript
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

Azure Maps Web SDK converte queste `Point` funzionalità in `Polygon` funzionalità dietro le quinte. `Point`È possibile eseguire il rendering di queste funzionalità sulla mappa utilizzando il livello di estrusione poligono, come illustrato nell'esempio di codice seguente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono di spazio aereo drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>poligono di spazio</a> aereo per la penna di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalizzare un livello di estrusione poligono

Il livello di estrusione poligono presenta diverse opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [livello estrusione poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer)

Risorse aggiuntive:

> [!div class="nextstepaction"]
> [Estensione della specifica GeoJSON di Mappe di Azure](extend-geojson.md#circle)
