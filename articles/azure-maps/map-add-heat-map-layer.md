---
title: Aggiungere un livello per le mappe termiche a Mappe di Azure | Microsoft Docs
description: How to add a heat map layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484304"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.  For example, rendering tens of thousands of points within the map view as symbols, covers most of the map area and would result in many symbols overlapping each other, making it difficult to gain much insight into the data. Tuttavia, visualizzare questo stesso set di dati come una mappa termica semplifica la visualizzzione dei punti in cui i dati sono più densi sistemi e la densità relativa ad altre aree. Esistono molti scenari in cui le mappe termiche vengono usate. Di seguito sono riportati alcuni esempi:

- I dati sulla temperatura vengono visualizzati in genere come mappa termica in quanto fornisce approssimazioni per quanto riguarda la temperatura tra due punti di dati.
- Il rendering dei dati per i sensori di rumore come mappa termica non solo mostra l'intensità del rumore nel punto in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in un sito potrebbe non essere elevato, ma se l'area di copertura acustica di più sensori si sovrappone, è possibile che quest'area di sovrapposizione abbia livelli di rumore più elevati e quindi, sia visibile nella mappa termica.
- Visualizing a GPS trace that includes the speed as a weighted height map where the intensity of each data point is based on the speed is a great way to see where the vehicle was speeding.

> [!TIP]
> Per impostazione predefinita, i livelli della mappa termica eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. To limit the layer so that it only renders point geometry features, set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come una mappa termica, è sufficiente trasferire l'origine dati in un'istanza della classe `HeatMapLayer` e aggiungerla alla mappa come mostrato di seguito.

In the following code, each heat point has a radius of 10 pixels at all zoom levels. When adding the heat map layer to the map, this sample inserts it below the label layer to create a better user experience as the labels are clearly visible above the heat map. The data in this sample is sourced from the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) and represents significant earthquakes that have occurred in the last 30 days.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Personalizzazione del livello per le mappe termiche

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello per le mapphe termiche offre diverse opzioni per la personalizzazione;

* `radius`: Defines a pixel radius in which to render each data point. Il raggio può essere impostato come un numero fisso o un'espressione. Using an expression, it's possible to scale the radius based on the zoom level, that appears to represent a consistent spatial area on the map (for example, 5-mile radius).
* `color`: Specifies how the heat map is colorized. A color gradient is often used for heat maps and can be achieved with an `interpolate` expression. Using a `step` expression for colorizing the heat map breaks up the density visually into ranges that more so resembles a contour or radar style map. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. I valori di colore per le mappe termiche sono specificati come espressione sul valore `heatmap-density`. The color at index 0 in an interpolation expression or the default color of a step expression, defines the color of the area where there's no data and can be used to define a background color. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. Ecco alcuni esempi di espressioni di colore;

| Interpolation Color Expression | Espressione colore graduale | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`: Specifies how opaque or transparent the heat map layer is.
- `intensity`: Applies a multiplier to the weight of each data point to increase the overall intensity of the heatmap and helps to make the small differences in the weight of data points become easier to visualize.
- `weight`: By default, all data points have a weight of 1, thus all data points are weighted equally. L'opzione peso funge da moltiplicatore e può essere impostata come numero o espressione. Se un numero è impostato come peso, ad esempio 2, equivarrebbe a collocare due volte ogni punto dati sulla mappa, raddoppiando così la densità. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. However, if an expression is used, the weight of each data point can be based on the properties of each data point. Per esempio, nei dati sui terremoti, ogni punto dati rappresenta un terremoto. Una metrica importante di ogni punto dati di un terremoto è il valore della magnitudo. I terremoti si verificano continuamente, ma la maggior parte di essi hanno una bassa magnitudo e non vengono nemmeno percepiti. Using the magnitude value in an expression to assign the weight to each data point will allow more significant earthquakes to be better represented within the heat map.
- Oltre alle opzioni del livello base (zoom minimo/massimo, visualizzazione e filtro), esiste anche l'opzione `source`, se si vuole aggiornare l'origine dati, e l'opzione `source-layer`, se l'origine dati è un'origine della mappa vettoriale.

Ecco uno strumento per testare le diverse opzioni del livello per le mappe termiche.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

By default, the radii of data points rendered in the heat map layer have a fixed pixel radius for all zoom levels. As the map is zoomed, the data aggregates together and the heat map layer looks different. A `zoom` expression can be used to scale the radius for each zoom level such that each data point covers the same physical area of the map. This will make the heat map layer look more static and consistent. Each zoom level of the map has twice as many pixels vertically and horizontally as the previous zoom level. Scaling the radius such that it doubles with each zoom level will create a heat map that looks consistent on all zoom levels. This can be accomplished by using the `zoom` with a base 2 `exponential interpolation` expression as shown in the sample below. Zoom the map to see how the heat map scales with the zoom level.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Abilitando il clustering sull'origine dati, i punti che sono vicini l'uno all'altro sono raggruppati insieme come punto in cluster. Il numero di punti di ogni cluster può essere utilizzato come espressione del peso per la mappa termica e riduce significativamente il numero di punti sui cui eseguire il rendering. The point count of a cluster is stored in a `point_count` property of the point feature as shown below. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se il raggio di clustering è di pochi pixel si otterrà poca differenza visiva nel rendering. Un raggio più ampio raggrupperà più punti in ogni cluster e migliorerà le prestazioni della mappa termica, ma le differenze saranno tanto più evidenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)