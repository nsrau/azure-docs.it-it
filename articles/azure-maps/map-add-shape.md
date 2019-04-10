---
title: Aggiungere una forma con Mappe di Azure | Microsoft Docs
description: Come aggiungere una forma a una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1e550002948fc1320b8645bf1af635536d524fe6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282390"
---
# <a name="add-a-shape-to-a-map"></a>Aggiungere una forma a una mappa

Questo articolo illustra come eseguire il rendering di geometrie sulla mappa utilizzando i livelli poligono e linea. Mappe di Azure SDK per Web supporta inoltre la creazione di geometrie cerchio come definito nel [estesa dello schema GeoJSON](extend-geojson.md#circle). Tutti gli oggetti Geometry funzionalità può essere facilmente aggiornato anche se è stato eseguito il wrapping con la [forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) classe.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Aggiungere righe alla mappa

`LineString` e `MultiLineString` funzionalità vengono usate per rappresentare i percorsi e le strutture sulla mappa.

## <a name="use-a-line"></a>Usare una riga

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un oggetto [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) viene creato e aggiunto all'origine dati.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) esegue il rendering degli oggetti linea sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). L'ultimo blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello di riga vengono creati e aggiunti alla mappa all'interno di [gestore dell'evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che la riga viene visualizzata dopo che la mappa è stato caricato completamente.

### <a name="add-symbols-along-a-line"></a>Aggiungere simboli lungo una linea

In questo esempio viene illustrato come aggiungere le icone freccia lungo una linea sulla mappa. Quando tramite un livello di simboli, impostare l'opzione "posizione" a "riga", verrà rendering i simboli lungo la linea e ruotare le icone (0 gradi = right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostra freccia lungo riga" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show freccia lungo line</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Aggiungere una sfumatura di traccia a una riga

Oltre a essere in grado di applicare un colore tratto solo a una riga è inoltre possibile compilare una riga con sfumature di colore per mostrare transizione da un segmento di linea a quella successiva. Ad esempio, le sfumature di riga sono utilizzabile per rappresentare le modifiche nel tempo e distanza o le temperature diversi in una linea degli oggetti collegati. Per applicare questa funzionalità a una riga, è necessario che l'origine dati di `lineMetrics` impostata su true l'opzione e quindi un'espressione di sfumatura di colore può essere passata al `strokeColor` opzione della riga. L'espressione della sfumatura tratto è per riferimento il `['line-progress']` espressione di dati che espone le metriche di riga calcolata per l'espressione.

<br/>

<iframe height="265" style="width: 100%;" scrolling="no" title="Riga con sfumatura tratto" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=265&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>riga con sfumatura tratto</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personalizzare un livello linea

Per il livello linea sono disponibili varie opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello linea' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opzioni del livello linea</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Aggiungere un'istanza polygon alla mappa

`Polygon` e `MultiPolygon` funzionalità vengono spesso usate per rappresentare un'area in una mappa. 

### <a name="use-a-polygon-layer"></a>Usare un livello poligono 

Esegue il rendering di un livello poligono l'area di un poligono. 

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un elemento [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) viene creato da una matrice di coordinate e aggiunto all'origine dati. 

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa all'interno di [gestore dell'evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il poligono venga visualizzato dopo che la mappa è stato caricato completamente.

### <a name="use-a-polygon-and-line-layer-together"></a>Usare un livello poligono e linea insieme

Un livello di riga è utilizzabile per eseguire il rendering del contorno di un poligono. 

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un elemento [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) viene creato da una matrice di coordinate e aggiunto all'origine dati. 

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Un [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) è una matrice di linee. Vedere le proprietà di un livello linea in [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Il terzo blocco di codice crea i livelli poligono e linea.

L'ultimo blocco di codice aggiunge i livelli poligono e linea alla mappa. L'origine dati e i livelli vengono creati e aggiunti alla mappa all'interno di [gestore dell'evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il poligono venga visualizzato dopo che la mappa è stato caricato completamente.

> [!TIP]
> Livelli di riga per impostazione predefinita verranno eseguito il rendering le coordinate di poligoni, nonché le righe in un'origine dati. Per limitare il livello di modo che solo il rendering LineString funzionalità set il `filter` proprietà del livello `['==', ['geometry-type'], 'LineString']` o `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` se si desidera includere anche le funzionalità MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Riempimento di un poligono con un modello

Oltre a riempire un poligono con un colore di un modello di immagine inoltre può essere usato. Un modello di immagine, caricare le risorse di sprite mappe immagine e quindi fare riferimento a questa immagine con il `fillPattern` proprietà del livello poligono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Motivo di riempimento poligono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>motivo di riempimento poligono</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Personalizzare un livello poligono

Il livello poligono include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Aggiungere un cerchio alla mappa

Mappe di Azure Usa una versione estesa dello schema che fornisce una definizione di un cerchio come indicato in GeoJSON [qui](extend-geojson.md#circle). Un cerchio può essere sottoposto a rendering sulla mappa mediante la creazione di un `Point` caratteristica che dispone di un `subType` proprietà con il valore `"Circle"` e un `radius` proprietà con un numero che rappresenta il raggio in metri. Ad esempio: 

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

Mappe di Azure SDK per Web converte questi `Pooint` features in `Polygon` features dietro le quinte e può essere sottoposto a rendering sulla mappa utilizzando i livelli poligono e linea, come illustrato di seguito.

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un cerchio è un [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dei [punto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e ha un `subType` proprietà impostata su `"Circle"` e un `radius` valore della proprietà in metri. Quando una funzionalità punto con un `subType` di `"Circle"` viene aggiunto a un'origine dati, convertito in un poligono circolare all'interno della mappa.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa all'interno di [gestore dell'evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il cerchio viene visualizzato dopo che la mappa è stato caricato completamente.

## <a name="make-a-geometry-easy-to-update"></a>Rendere più semplici da aggiornare una geometria

Oggetto `Shape` classe esegue il wrapping una [Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) o [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e rende più semplice aggiornare e gestirle.
`new Shape(data: Feature<data.Geometry, any>)` Costruisce un oggetto shape e la inizializza con la funzionalità specificata.

<br/>

<iframe height='500' scrolling='no' title='Aggiornare le proprietà delle forme' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Aggiornare le proprietà delle forme) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Un punto è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) della classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) (Punto). Il secondo blocco di codice inizializza il valore del raggio per l'elemento del dispositivo di scorrimento HTML e quindi costruisce ed esegue il wrapping di un oggetto punto in un oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma).

Il terzo blocco di codice crea una funzione che prende il valore dall'elemento del dispositivo di scorrimento dell'intervallo HTML e modifica il valore del raggio usando il metodo [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) della classe shape.

Nel quarto blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Il punto viene quindi aggiunto all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Il terzo blocco di codice crea un livello poligono. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati, hanlder l'evento clic e il livello poligono vengono creati e aggiunti alla mappa all'interno di [gestore dell'evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il punto viene visualizzato dopo che la mappa è stato caricato completamente.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Usare le espressioni di stile basato sui dati](data-driven-style-expressions-web-sdk.md)
