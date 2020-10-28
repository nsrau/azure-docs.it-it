---
title: Aggiungere un livello poligono a una mappa | Mappe di Microsoft Azure
description: Informazioni su come aggiungere poligoni o cerchi a maps. Scopri come usare Azure Maps Web SDK per personalizzare le forme geometriche e semplificarne l'aggiornamento e la gestione.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890998"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Aggiungere un livello poligono alla mappa

Questo articolo illustra come eseguire il rendering delle aree delle geometrie delle caratteristiche `Polygon` e `MultiPolygon` sulla mappa usando un livello poligono. Azure Maps Web SDK supporta anche la creazione di geometrie Circle come definite nello [schema GeoJSON esteso](extend-geojson.md#circle). Quando ne viene eseguito il rendering sulla mappa, questi cerchi vengono trasformati in poligoni. Tutte le geometrie delle caratteristiche possono essere facilmente aggiornate quando ne viene eseguito il wrapping con la classe [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape).

## <a name="use-a-polygon-layer"></a>Aggiungere un livello poligono 

Quando un livello poligono viene connesso a un'origine dati e caricato sulla mappa, esegue il rendering dell'area con le caratteristiche `Polygon` e `MultiPolygon`. Per creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering con un livello poligono usando la classe [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer).

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Di seguito è riportato l'esempio completo e funzionante del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Usare insieme un poligono e un livello linea

Per eseguire il rendering del contorno dei poligoni viene usato un livello linea. L'esempio di codice seguente esegue il rendering di un poligono come nell'esempio precedente, ma con l'aggiunta di un livello linea. Questo livello linea è un secondo livello connesso all'origine dati.  

<br/>

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Riempire un poligono con un motivo

Oltre che con un colore, è possibile riempire un poligono usando un motivo immagine. Caricare un motivo immagine nelle risorse sprite delle immagini delle mappe e quindi fare riferimento all'immagine con la proprietà `fillPattern` del livello poligono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Motivo di riempimento poligono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a> (Motivo di riempimento poligono) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare come motivi di riempimento. Per altre informazioni, vedere il documento [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-polygon-layer"></a>Personalizzare un livello poligono

Il livello poligono include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Mappe di Azure usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi, come illustrato [qui](extend-geojson.md#circle). Il rendering di un cerchio sulla mappa viene eseguito creando una caratteristica `Point`. Questa caratteristica `Point` ha una proprietà `subType` con valore `"Circle"` e una proprietà `radius` con un numero che rappresenta il raggio in metri. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps Web SDK converte queste caratteristiche `Point` in caratteristiche `Polygon`. Viene quindi eseguito il rendering di queste caratteristiche sulla mappa usando i livelli poligono e linea, come mostrato nell'esempio di codice seguente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Semplificare l'aggiornamento di una geometria

Una classe `Shape` esegue il wrapping di una [geometria](/javascript/api/azure-maps-control/atlas.data.geometry) o una [caratteristica](/javascript/api/azure-maps-control/atlas.data.feature) e ne semplifica l'aggiornamento e la gestione. Per creare un'istanza di una variabile Shape, passare una geometria o un set di proprietà al costruttore di forme.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

L'esempio di codice seguente mostra come eseguire il wrapping di un oggetto GeoJSON Circle con una classe Shape. Quando il valore del raggio cambia nella forma, viene eseguito automaticamente il rendering del cerchio sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Aggiornare le proprietà delle forme' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Aggiornare le proprietà delle forme) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

Risorse aggiuntive:

> [!div class="nextstepaction"]
> [Estensione della specifica GeoJSON di Mappe di Azure](extend-geojson.md#circle)