---
title: Aggiunta di un layer poligono a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come eseguire il rendering di un poligono e di più poligoni su una mappa in Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535053"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Aggiungere un layer poligono alla mappa

In questo articolo viene illustrato `Polygon` come `MultiPolygon` eseguire il rendering delle geometrie e delle geometrie sulla mappa utilizzando un layer poligono. Il Web SDK di Azure Maps supporta inoltre la creazione di geometrie Circle definite nello [schema GeoJSON esteso.](extend-geojson.md#circle) Questi cerchi vengono trasformati in poligoni quando vengono visualizzati sulla mappa. Tutte le geometrie di entità geografiche possono essere facilmente aggiornate quando avvolte con [l'atlante. Classe Shape.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Usare un layer poligono 

Quando un layer poligono è connesso a un'origine dati `Polygon` e `MultiPolygon` caricato sulla mappa, esegue il rendering dell'area con e delle feature. Per creare un poligono, aggiungerlo a un'origine dati ed eseguirne il rendering con un layer poligono utilizzando la classe [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

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

Di seguito è riportato l'esempio completo e in esecuzione del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Utilizzare insieme un livello poligono e un livello di linea

Un livello linea viene utilizzato per eseguire il rendering del contorno dei poligoni. L'esempio di codice seguente esegue il rendering di un poligono come l'esempio precedente, ma ora aggiunge un livello di linea. Questo livello di linea è un secondo livello connesso all'origine dati.  

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Riempire un poligono con un motivo

Oltre a riempire un poligono con un colore, è possibile utilizzare un motivo immagine per riempire il poligono. Caricare un modello di immagine nelle risorse sprite dell'immagine delle mappe e quindi fare riferimento a questa immagine con la `fillPattern` proprietà del livello poligono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Motivo di riempimento poligono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il motivo di <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>riempimento Poligono penna</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare come modelli di riempimento. Per ulteriori informazioni, vedere il documento Come utilizzare i modelli di [immagine.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Personalizzare un livello poligono

Il livello poligono include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Mappe di Azure usa una versione estesa dello schema GeoJSON che fornisce una definizione per i cerchi, come indicato [di seguito.](extend-geojson.md#circle) Viene eseguito il rendering di `Point` un cerchio sulla mappa mediante la creazione di una feature. Ha `Point` una `subType` proprietà con `"Circle"` un `radius` valore di e una proprietà con un numero che rappresenta il raggio in metri. 

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

Azure Maps Web SDK `Point` converte `Polygon` queste funzionalità in funzionalità. Quindi, il rendering di queste feature viene eseguito sulla mappa utilizzando i layer poligonali e linea, come illustrato nell'esempio di codice seguente.

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Semplificare l'aggiornamento di una geometria

Una `Shape` classe esegue il wrapping di un [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) o [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e semplifica l'aggiornamento e la gestione di queste funzionalità. Per creare un'istanza di una variabile di forma, passare una geometria o un set di proprietà al costruttore della forma.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Nell'esempio di codice riportato di seguito viene illustrato come eseguire il wrapping di un oggetto GeoJSON cerchio con una classe shape. Quando il valore del raggio cambia nella forma, il cerchio viene eseguito automaticamente sul mapping.

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
> [PolygonLayerOpzioni](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine datiCreate a data source](create-data-source-web-sdk.md)

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
> [Estensione della specifica GeoJSON di Azure MapsAzure Maps GeoJSON specification extension](extend-geojson.md#circle)