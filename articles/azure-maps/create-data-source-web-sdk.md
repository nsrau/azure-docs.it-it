---
title: Creare un'origine dati in Maps di Azure | Microsoft Docs
description: Come creare un'origine dati e usarla con Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36c06182d0807ce3d255477a865023ae7b74e2cb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874918"
---
# <a name="create-a-data-source"></a>Creare un'origine dati

Azure Maps Web SDK archivia i dati nelle origini dati che ottimizzano i dati per l'esecuzione di query e il rendering. Attualmente esistono due tipi di origini dati:

**Origine dati GeoJSON**

Un'origine dati basata su GeoJSON può caricare e archiviare i dati localmente `DataSource` usando la classe. I dati GeoJSON possono essere creati o creati manualmente usando le classi helper nello spazio dei nomi [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . La `DataSource` classe fornisce funzioni per l'importazione di file GeoJSON locali o remoti. I file GeoJSON remoti devono essere ospitati in un endpoint abilitato per CORs. La `DataSource` classe fornisce funzionalità per i dati dei punti di clustering. I dati possono essere facilmente aggiunti, rimossi e aggiornati con la `DataSource` classe.


> [!TIP]
> Se si desidera sovrascrivere tutti i dati di `DataSource`un oggetto, se si effettuano `clear` chiamate `add` alle funzioni then, la mappa tenterà di eseguire nuovamente il rendering due volte che potrebbero causare un po' di ritardo. Usare invece la `setShapes` funzione che rimuoverà e sostituirà tutti i dati nell'origine dati e attiverà un solo nuovo rendering della mappa.

**Origine riquadro vettoriale**

Un'origine del riquadro vettoriale descrive come accedere a un livello tessera vettoriale e può essere creata usando la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) . Azure Maps è allineato alla [specifica del riquadro vettoriale MapBox](https://github.com/mapbox/vector-tile-spec), che è uno standard aperto. I livelli dei riquadri vettoriali sono simili ai livelli affiancati, tuttavia, invece di ogni riquadro costituito da un'immagine raster, si tratta di un file compresso (formato PBF) che contiene i dati della mappa vettoriale e uno o più livelli di cui è possibile eseguire il rendering e lo stile nel client in base allo stile di ogni livello. I dati in un riquadro vettoriale contengono funzionalità geografiche sotto forma di punti, linee e poligoni. Sono disponibili diversi vantaggi dei livelli del riquadro vettoriale rispetto ai livelli di sezione raster;

 - Una dimensione del file di un riquadro vettoriale è in genere molto più piccola rispetto a un riquadro raster equivalente. Di conseguenza, viene usata una minore larghezza di banda, ovvero una latenza più bassa e una mappa più veloce. Questo consente di migliorare l'esperienza utente.
 - Poiché i riquadri vettoriali vengono sottoposti a rendering nel client, possono adattarsi alla risoluzione del dispositivo in cui vengono visualizzati. In questo modo, le mappe sottoposte a rendering risultano molto più ben definite e con etichette chiare. 
 - Per modificare lo stile dei dati nelle mappe vettoriali non è necessario scaricare di nuovo i dati perché il nuovo stile può essere applicato al client. Al contrario, la modifica dello stile di un livello sezione raster richiede in genere il caricamento di riquadri dal server a cui è applicato il nuovo stile.
 - Poiché i dati vengono recapitati in forma vettoriale, è necessaria una minore elaborazione sul lato server per preparare i dati, il che significa che i dati più recenti possono essere resi disponibili più velocemente.

Tutti i livelli che usano un'origine vettore devono specificare `sourceLayer` un valore. 

Una volta create, le origini dati possono essere aggiunte alla mappa tramite `map.sources` la proprietà, che è un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Nel codice seguente viene illustrato come creare un `DataSource` oggetto e aggiungerlo alla mappa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Connessione di un'origine dati a un livello

Viene eseguito il rendering dei dati sulla mappa utilizzando i livelli di rendering. È possibile fare riferimento a una singola origine dati da uno o più livelli di rendering. I livelli di rendering seguenti richiedono un'origine dati da potere:

- [Bubble layer](map-add-bubble-layer.md) : esegue il rendering dei dati punto come cerchi ridimensionati sulla mappa.
- [Livello simboli](map-add-pin.md) : esegue il rendering dei dati del punto come icone e/o testo.
- [Livello mappa termica](map-add-heat-map-layer.md) : esegue il rendering dei dati punto come mappa termica della densità.
- [Livello linea](map-add-shape.md) : può essere usato per eseguire il rendering della linea e o del contorno dei poligoni. 
- [Livello poligono](map-add-shape.md) : riempie l'area di un poligono con un colore a tinta unita o un modello di immagine.

Il codice seguente illustra come creare un'origine dati, aggiungerla alla mappa e connetterla a un livello Bubble, quindi importare i dati del punto GeoJSON da una posizione remota al suo interno. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Sono disponibili livelli di rendering aggiuntivi che non si connettono a queste origini dati, ma caricano invece i dati che eseguono il rendering direttamente. 

- [Livello immagine](map-add-image-layer.md) : sovrappone una singola immagine nella parte superiore della mappa e ne associa gli angoli a un set di coordinate specificate.
- [Livello sezione](map-add-tile-layer.md) : sovrappone un livello sezione raster sopra la mappa.

## <a name="one-data-source-with-multiple-layers"></a>Un'origine dati con più livelli

È possibile connettere più livelli a un'unica origine dati. Questo può sembrare strano, ma ci sono molti scenari diversi in cui questo risulta utile. Si prenda, ad esempio, lo scenario di creazione di un'esperienza di disegno Polygon. Quando si consente a un utente di disegnare un poligono, è necessario eseguire il rendering dell'area poligono di riempimento quando l'utente aggiunge punti alla mappa. L'aggiunta di una linea con stile che delinea il poligono renderà più semplice la visualizzazione dei bordi del poligono durante il disegno. Infine, l'aggiunta di un tipo di handle, ad esempio un PIN o un marcatore, al di sopra di ogni posizione nel poligono semplifica la modifica di ogni singola posizione. Ecco un'immagine che illustra questo scenario.

![Mappa che Mostra più livelli di rendering dei dati da una singola origine dati](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Per realizzare questo scenario nella maggior parte delle piattaforme di mapping, è necessario creare un oggetto poligono, un oggetto linea e un PIN per ogni posizione nel poligono. Quando si modifica il poligono, è necessario aggiornare manualmente la linea e i pin, che possono diventare complessi rapidamente.

Con Azure Maps è sufficiente un singolo poligono in un'origine dati, come illustrato nel codice riportato di seguito.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello linea](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello poligono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)