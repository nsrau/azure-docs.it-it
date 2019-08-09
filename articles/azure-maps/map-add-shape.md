---
title: Aggiungere una forma con Mappe di Azure | Microsoft Docs
description: Come aggiungere una forma a una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 377cfbf93e45872e34242f9cd2432195b6ba6f92
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849108"
---
# <a name="add-a-shape-to-a-map"></a>Aggiungere una forma a una mappa

Questo articolo illustra come eseguire il rendering di geometrie sulla mappa usando i livelli linea e poligono. Azure Maps Web SDK supporta anche la creazione di geometrie Circle come definito nello [schema GeoJSON esteso](extend-geojson.md#circle). Tutte le geometrie della funzionalità possono essere facilmente aggiornate anche se sottolineate con la classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Aggiungere righe alla mappa

`LineString`le `MultiLineString` funzionalità e vengono usate per rappresentare i percorsi e le strutture sulla mappa.

### <a name="add-a-line"></a>Aggiungere una linea

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un oggetto [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) viene creato e aggiunto all'origine dati.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) esegue il rendering degli oggetti linea sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). L'ultimo blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello linea vengono creati e aggiunti alla mappa all'interno del [gestore eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che la riga venga visualizzata dopo il caricamento completo della mappa.

### <a name="add-symbols-along-a-line"></a>Aggiungi simboli lungo una riga

In questo esempio viene illustrato come aggiungere icone a freccia lungo una riga sulla mappa. Quando si usa un livello di simboli, impostare l'opzione "posizionamento" su "linea", in modo da eseguire il rendering dei simboli lungo la linea e ruotare le icone (0 gradi = destra).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mostra freccia lungo la linea" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Per le mappe <a href='https://codepen.io/azuremaps/pen/drBJwX/'></a> di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>, vedere la freccia di visualizzazione della penna lungo la riga.
</iframe>

> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare con il livello dei simboli. Per ulteriori informazioni, vedere il documento [come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md) .

### <a name="line-stroke-gradient"></a>Aggiungere una sfumatura del tratto a una riga

Oltre a poter applicare un singolo colore tratto a una riga, è anche possibile riempire una linea con una sfumatura di colori per visualizzare la transizione da un segmento di linea a quello successivo. Ad esempio, è possibile usare le sfumature di linea per rappresentare le modifiche nel tempo e nella distanza oppure con temperature diverse in una linea di oggetti connessa. Per applicare questa funzionalità a una riga, è necessario che l' `lineMetrics` opzione dell'origine dati sia impostata su true, quindi un'espressione di sfumatura di colore possa essere passata `strokeColor` all'opzione della riga. L'espressione di sfumatura del tratto deve `['line-progress']` fare riferimento all'espressione di dati che espone le metriche della linea calcolata all'espressione.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Riga con sfumatura tratto" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la linea della penna <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>con sfumatura tratto</a> di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Personalizzare un livello linea

Per il livello linea sono disponibili varie opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello linea' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opzioni del livello linea</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Aggiungere un poligono alla mappa

`Polygon`le `MultiPolygon` funzionalità e vengono spesso usate per rappresentare un'area su una mappa. 

### <a name="use-a-polygon-layer"></a>Usare un livello poligono 

Un livello poligono esegue il rendering dell'area di un poligono. 

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un elemento [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) viene creato da una matrice di coordinate e aggiunto all'origine dati. 

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa all'interno del [gestore eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il poligono venga visualizzato dopo il caricamento completo della mappa.

### <a name="use-a-polygon-and-line-layer-together"></a>Usare insieme un poligono e un livello linea

Un livello linea può essere usato per eseguire il rendering del contorno di un poligono. 

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un elemento [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) viene creato da una matrice di coordinate e aggiunto all'origine dati. 

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Un [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) è una matrice di linee. Vedere le proprietà di un livello linea in [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Il terzo blocco di codice crea i livelli poligono e linea.

L'ultimo blocco di codice aggiunge i livelli poligono e linea alla mappa. L'origine dati e i livelli vengono creati e aggiunti alla mappa all'interno del [gestore eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il poligono venga visualizzato dopo il caricamento completo della mappa.

> [!TIP]
> Per impostazione predefinita, i livelli linea eseguono il rendering delle coordinate dei poligoni e delle linee in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità `filter` LineString, impostare la proprietà `['==', ['geometry-type'], 'LineString']` del `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` livello su o se si desidera includere anche le funzionalità MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Riempire un poligono con un modello

Oltre a riempire un poligono con un colore, è possibile usare anche un modello di immagine. Caricare un modello di immagine in Maps image sprite Resources e quindi fare riferimento a `fillPattern` questa immagine con la proprietà del livello Polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Modello di riempimento poligono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>modello di riempimento</a> del poligono di penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>per mappe di Azure () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK offre diversi modelli di immagine personalizzabili che è possibile usare come modelli di riempimento. Per ulteriori informazioni, vedere il documento [come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md) .

### <a name="customize-a-polygon-layer"></a>Personalizzare un livello poligono

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

Azure Maps Web SDK converte queste `Point` funzionalità in `Polygon` funzionalità dietro le quinte ed è possibile eseguirne il rendering sulla mappa usando i livelli poligono e linea, come illustrato qui.

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un cerchio è una [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) di [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e ha una `subType` proprietà impostata su `"Circle"` e un `radius` valore di proprietà in metri. Quando `"Circle"` viene aggiunta a un'origine `subType` dati, una funzionalità punto con un oggetto viene convertita in un poligono circolare all'interno della mappa.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa all'interno del [gestore eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il cerchio venga visualizzato dopo il caricamento completo della mappa.

## <a name="make-a-geometry-easy-to-update"></a>Semplifica l'aggiornamento di una geometria

Una `Shape` classe esegue il wrapping di una [geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) o di una [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e ne facilita l'aggiornamento e la gestione.
`new Shape(data: Feature<data.Geometry, any>)` costruisce un oggetto forma e lo inizializza con la funzionalità specificata.

<br/>

<iframe height='500' scrolling='no' title='Aggiornare le proprietà delle forme' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Aggiornare le proprietà delle forme) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Un punto è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) della classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) (Punto). Il secondo blocco di codice inizializza il valore del raggio per l'elemento del dispositivo di scorrimento HTML e quindi costruisce ed esegue il wrapping di un oggetto punto in un oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma).

Il terzo blocco di codice crea una funzione che prende il valore dall'elemento del dispositivo di scorrimento dell'intervallo HTML e modifica il valore del raggio usando il metodo [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) della classe shape.

Nel quarto blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Il punto viene quindi aggiunto all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Il terzo blocco di codice crea un livello poligono. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati, il gestore dell'evento click e il livello poligono vengono creati e aggiunti alla mappa all'interno del [gestore eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il punto venga visualizzato dopo il caricamento completo della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Come usare i modelli di immagine](how-to-use-image-templates-web-sdk.md)