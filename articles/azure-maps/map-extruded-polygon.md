---
title: Aggiungere un livello poligono di estrusione a Maps di Azure | Microsoft Docs
description: Come aggiungere un livello poligono di estrusione ad Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170711"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Aggiungere un livello poligono di estrusione alla mappa

Questo articolo illustra come usare il livello di estrusione poligono per eseguire il rendering delle aree delle geometrie della funzionalità `Polygon` e `MultiPolygon` come forme estruse sulla mappa. Azure Maps Web SDK supporta anche la creazione di geometrie Circle come definito nello [schema GeoJSON esteso](extend-geojson.md#circle). Questi cerchi vengono trasformati in poligoni quando vengono sottoposti a rendering sulla mappa. Tutte le geometrie della funzionalità possono essere facilmente aggiornate anche se incapsulate con l' [Atlante. Classe Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .


## <a name="use-a-polygon-extrusion-layer"></a>Usare un livello di estrusione poligono

Quando un [livello di estrusione poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) è connesso all'origine dati e caricato sulla mappa, esegue il rendering delle aree di una funzionalità `Polygon` e `MultiPolygon` come forme estruse. Le proprietà `height` e `base` del livello di estrusione poligono definiscono la distanza di base dalla superficie e dall'altezza della forma estrusa in **metri**. Il codice seguente illustra come creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering usando la classe del livello di estrusione poligono.

> [!Note]
> Il valore `base` definito nel livello di estrusione poligono deve essere minore o uguale a quello del `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono estruso" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>poligono estruso</a> di penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Aggiungere multipoligoni basati sui dati

È possibile eseguire il rendering di una mappa choropleth usando il livello di estrusione poligono, impostando le proprietà `height` e `fillColor` in proporzione alla misurazione della variabile statistica nelle geometrie della funzionalità `Polygon` e `MultiPolygon`. Nell'esempio di codice seguente viene illustrata una mappa choropleth estrusa dell'U. S in base alla misurazione della densità della popolazione per stato.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa choropleth estrusa" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mappa choropleth di Pen estrusa</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Azure Maps usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi come indicato [qui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). È possibile eseguire il rendering di un cerchio estruso sulla mappa creando una funzionalità `point` con una proprietà `subType` di `Circle` e una proprietà `Radius` numerata che rappresenta il raggio in **metri**. Esempio:

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

Azure Maps Web SDK converte queste funzionalità `Point` in funzionalità `Polygon` sotto la cappa e può essere sottoposta a rendering sulla mappa usando il livello di estrusione poligono, come illustrato nell'esempio di codice seguente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Poligono di spazio aereo drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>poligono dello spazio aereo</a> per il drone penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [livello estrusione poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Risorse aggiuntive:

> [!div class="nextstepaction"]
> [Estensione della specifica GeoJSON di mappe di Azure](extend-geojson.md#circle)
