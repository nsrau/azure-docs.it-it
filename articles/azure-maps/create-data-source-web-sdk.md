---
title: Creazione di un'origine dati per una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come creare un'origine dati e aggiungerla a una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190223"
---
# <a name="create-a-data-source"></a>Creare un'origine dati

Azure Maps Web SDK archivia i dati nelle origini dati. L'utilizzo delle origini dati consente di ottimizzare le operazioni sui dati per l'esecuzione di query e il rendering. Attualmente sono disponibili due tipi di origini dati:

**Origine dati GeoJSON**

Un'origine dati basata su GeoJSON `DataSource` carica e archivia i dati localmente utilizzando la classe . I dati GeoJSON possono essere creati o creati manualmente usando le classi helper nello spazio dei nomi atlas.data.GeoJSON data can be manually created or created using the helper classes in the [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) namespace. La `DataSource` classe fornisce funzioni per importare file GeoJSON locali o remoti. I file GeoJSON remoti devono essere ospitati in un endpoint abilitato per i CRR. La `DataSource` classe fornisce funzionalità per i dati punto di clustering. Inoltre, i dati possono essere facilmente aggiunti, rimossi e aggiornati con la `DataSource` classe.


> [!TIP]
> Si supponga di voler sovrascrivere `DataSource`tutti i dati in un file . Se si effettuano `clear` `add` chiamate alle funzioni then, la mappa potrebbe eseguire nuovamente il rendering due volte, il che potrebbe causare un ritardo. Utilizzare invece la `setShapes` funzione , che rimuoverà e sostituirà tutti i dati nell'origine dati e attiverà solo un singolo rendering della mappa.

**Origine riquadro vettoriale**

Un'origine di riquadro vettoriale descrive come accedere a un livello di riquadro vettoriale. Utilizzare la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) per creare un'istanza di un'origine del riquadro vettoriale. I livelli di tile vettoriali sono simili ai livelli affiancati, ma non sono uguali. Un livello di piastrelle è un'immagine raster. I livelli di tile vettoriali sono un file compresso, in formato PBF. Questo file compresso contiene i dati della mappa vettoriale e uno o più layer. È possibile eseguire il rendering del file e applicare uno stile al client, in base allo stile di ogni livello. I dati in un riquadro vettoriale contengono entità geografiche sotto forma di punti, linee e poligoni. Ci sono diversi vantaggi dell'uso di livelli di piastrelle vettoriali invece di livelli di piastrelle raster:

 - Una dimensione di file di un riquadro vettoriale è in genere molto più piccola di una tessera raster equivalente. Di conseguenza, viene utilizzata una larghezza di banda inferiore. Significa una latenza più bassa, una mappa più veloce e una migliore esperienza utente.
 - Poiché il rendering dei riquadri vettoriali viene eseguito nel client, si adattano alla risoluzione del dispositivo in cui vengono visualizzati. Di conseguenza, le mappe renderizzate appaiono più ben definite, con etichette cristalline.
 - La modifica dello stile dei dati nelle mappe vettoriali non richiede nuovamente il download dei dati, poiché il nuovo stile può essere applicato al client. Al contrario, la modifica dello stile di un layer di sezioni raster richiede in genere il caricamento di riquadri dal server e quindi l'applicazione del nuovo stile.
 - Poiché i dati vengono recapitati in forma vettoriale, è necessaria meno elaborazione sul lato server per preparare i dati. Di conseguenza, i dati più recenti possono essere resi disponibili più velocemente.

Tutti i layer che utilizzano `sourceLayer` un'origine vettoriale devono specificare un valore.

Una volta create, le origini dati `map.sources` possono essere aggiunte alla mappa tramite la proprietà , che è un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Il codice seguente viene `DataSource` illustrato come creare un e aggiungerlo alla mappa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps è conforme a [Mapbox Vector Tile Specification,](https://github.com/mapbox/vector-tile-spec)uno standard aperto.

## <a name="connecting-a-data-source-to-a-layer"></a>Connessione di un'origine dati a un livelloConnecting a data source to a layer

Il rendering dei dati viene eseguito sulla mappa utilizzando i layer di rendering. Una singola origine dati può essere referenziata da uno o più livelli di rendering. I livelli di rendering seguenti richiedono un'origine dati:The following rendering layers require a data source:

- [Livello bolla:](map-add-bubble-layer.md) esegue il rendering dei dati punto come cerchi in scala sulla mappa.
- [Livello simbolo:](map-add-pin.md) esegue il rendering dei dati punto come icone o testo.
- [Livello mappa termica:](map-add-heat-map-layer.md) esegue il rendering dei dati punto come mappa termica della densità.
- [Livello linea:](map-add-shape.md) esegue il rendering di una linea e/o il contorno dei poligoni. 
- [Livello poligono:](map-add-shape.md) riempie l'area di un poligono con un motivo a tinta unita o con un motivo immagine.

Nel codice seguente viene illustrato come creare un'origine dati, aggiungerla alla mappa e connetterla a un livello bolla. Quindi, importare i dati punto GeoJSON da una posizione remota nell'origine dati. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Esistono livelli di rendering aggiuntivi che non si connettono a queste origini dati, ma caricano direttamente i dati per il rendering. 

- [Livello immagine-](map-add-image-layer.md) sovrappone una singola immagine sopra la mappa e associa i suoi angoli a un set di coordinate specificate.
- [Tile layer](map-add-tile-layer.md) - sovrappone un layer di tile raster sopra la mappa.

## <a name="one-data-source-with-multiple-layers"></a>Un'origine dati con più livelliOne data source with multiple layers

È possibile connettere più layer a una singola origine dati. Esistono molti scenari diversi in cui questa opzione è utile. Si consideri, ad esempio, lo scenario in cui un utente disegna un poligono. Dobbiamo eseguire il rendering e riempire l'area del poligono quando l'utente aggiunge punti alla mappa. L'aggiunta di una linea con stile per delineare il poligono rende più semplice visualizzare i bordi del poligono, mentre l'utente disegna. Per modificare comodamente una singola posizione nel poligono, possiamo aggiungere una maniglia, come un perno o un marcatore, sopra ogni posizione.

![Mappa che mostra più layer che eseguono il rendering dei dati da una singola origine dati](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Nella maggior parte delle piattaforme di mappatura, è necessario un oggetto poligono, un oggetto linea e un perno per ogni posizione nel poligono. Quando il poligono viene modificato, è necessario aggiornare manualmente la linea e i perni, che possono diventare rapidamente complessi.

Con Azure Maps, è sufficiente un singolo poligono in un'origine dati, come illustrato nel codice seguente.

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
> [Datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

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
> [Aggiungere un livello simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello bolla](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)