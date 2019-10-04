---
title: Aggiungere un livello poligono a Maps di Azure | Microsoft Docs
description: Come aggiungere un livello poligono ad Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976131"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Aggiungere un livello poligono alla mappa

Questo articolo illustra come eseguire il rendering delle aree e `Polygon` `MultiPolygon` delle geometrie delle funzionalità sulla mappa usando un livello poligono. Azure Maps Web SDK supporta anche la creazione di geometrie Circle come definito nello [schema GeoJSON esteso](extend-geojson.md#circle). Questi cerchi vengono trasformati in poligoni quando vengono sottoposti a rendering sulla mappa. Tutte le geometrie della funzionalità possono essere facilmente aggiornate anche se incapsulate con l' [Atlante. Classe Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

## <a name="use-a-polygon-layer"></a>Usare un livello poligono 

Quando un livello poligono è connesso a un'origine dati e caricato sulla mappa, viene eseguito il rendering dell'area di `Polygon` un `MultiPolygon` e delle funzionalità di. Il codice seguente illustra come creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering con un livello poligono usando la classe [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Usare insieme un poligono e un livello linea

Un livello linea può essere usato per eseguire il rendering del contorno dei poligoni. Nell'esempio di codice seguente viene eseguito il rendering di un poligono come l'esempio precedente, ma ora viene aggiunto un livello linea come secondo livello connesso all'origine dati.  

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Riempire un poligono con un modello

Oltre a riempire un poligono con un colore, è possibile usare anche un modello di immagine. Caricare un modello di immagine in Maps image sprite Resources e quindi fare riferimento a `fillPattern` questa immagine con la proprietà del livello Polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modello di riempimento poligono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>modello di riempimento</a> del poligono di penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>per mappe di Azure () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare come modelli di riempimento. Per altre informazioni, vedere il documento [come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Personalizzare un livello poligono

Il livello poligono include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Azure Maps usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi come indicato [qui](extend-geojson.md#circle). È possibile eseguire il rendering di un cerchio sulla mappa creando `Point` una funzionalità `subType` con una `"Circle"` proprietà con un valore e una `radius` proprietà con un numero che rappresenta il raggio in metri. Ad esempio:

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

Azure Maps Web SDK converte queste `Point` funzionalità in `Polygon` funzionalità dietro le quinte ed è possibile eseguirne il rendering sulla mappa usando i livelli poligono e linea, come illustrato nell'esempio di codice seguente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Semplifica l'aggiornamento di una geometria

Una `Shape` classe esegue il wrapping di una [geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) o di una [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e ne facilita l'aggiornamento e la gestione. Una forma può essere creata passando una geometria e un set di proprietà oppure passando una funzionalità come illustrato nel codice seguente.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

L'esempio di codice seguente illustra come eseguire il wrapping di un oggetto GeoJSON Circle con una classe Shape e aggiornarne facilmente la proprietà RADIUS usando un dispositivo di scorrimento. Con la modifica del valore RADIUS nella forma, il rendering del cerchio viene aggiornato automaticamente sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Aggiornare le proprietà delle forme' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Aggiornare le proprietà delle forme) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

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
> [Aggiungere un livello linea](map-add-line-layer.md)

Risorse aggiuntive:

> [!div class="nextstepaction"]
> [Estensione della specifica GeoJSON di mappe di Azure](extend-geojson.md#circle)