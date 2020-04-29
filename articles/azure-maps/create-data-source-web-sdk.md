---
title: Creare un'origine dati per una mappa | Mappe Microsoft Azure
description: In questo articolo si apprenderà come creare un'origine dati e aggiungerla a una mappa usando il Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190223"
---
# <a name="create-a-data-source"></a>Creare un'origine dati

Azure Maps Web SDK archivia i dati nelle origini dati. L'utilizzo di origini dati ottimizza le operazioni di dati per l'esecuzione di query e il rendering. Attualmente esistono due tipi di origini dati:

**Origine dati GeoJSON**

Un'origine dati basata su GeoJSON carica e archivia i dati localmente `DataSource` usando la classe. I dati GeoJSON possono essere creati o creati manualmente usando le classi helper nello spazio dei nomi [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . La `DataSource` classe fornisce funzioni per l'importazione di file GeoJSON locali o remoti. I file GeoJSON remoti devono essere ospitati in un endpoint abilitato per CORs. La `DataSource` classe fornisce funzionalità per i dati dei punti di clustering. E, i dati possono essere facilmente aggiunti, rimossi e aggiornati con la `DataSource` classe.


> [!TIP]
> Si supponga di voler sovrascrivere tutti i dati in `DataSource`un. Se si effettuano chiamate alle `clear` funzioni `add` then, è possibile che venga nuovamente eseguito il rendering della mappa due volte, il che potrebbe causare un po' di ritardo. Usare invece la `setShapes` funzione, che rimuoverà e sostituirà tutti i dati nell'origine dati e attiverà un solo nuovo rendering della mappa.

**Origine riquadro vettoriale**

Un'origine del riquadro vettoriale descrive come accedere a un livello tessera vettoriale. Usare la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) per creare un'istanza di un'origine del riquadro vettoriale. I livelli dei riquadri vettoriali sono simili ai livelli affiancati, ma non sono uguali. Un livello sezione è un'immagine raster. I livelli del riquadro vettoriale sono file compressi in formato PBF. Questo file compresso contiene i dati della mappa vettoriale e uno o più livelli. È possibile eseguire il rendering e lo stile del file nel client, in base allo stile di ogni livello. I dati in un riquadro vettoriale contengono funzionalità geografiche sotto forma di punti, linee e poligoni. Esistono diversi vantaggi derivanti dall'uso di livelli di tessera vettoriale invece dei livelli di riquadri raster:

 - Una dimensione del file di un riquadro vettoriale è in genere molto più piccola rispetto a un riquadro raster equivalente. Di conseguenza, viene utilizzata una minore larghezza di banda. Ovvero una latenza più bassa, una mappa più veloce e un'esperienza utente migliore.
 - Poiché viene eseguito il rendering dei riquadri vettoriali nel client, si adattano alla risoluzione del dispositivo in cui vengono visualizzati. Di conseguenza, le mappe sottoposte a rendering vengono visualizzate in modo più ben definito, con etichette Clear Crystal.
 - Per modificare lo stile dei dati nelle mappe vettoriali, non è necessario scaricare di nuovo i dati, perché il nuovo stile può essere applicato nel client. Al contrario, la modifica dello stile di un livello sezione raster richiede in genere il caricamento di riquadri dal server, quindi l'applicazione del nuovo stile.
 - Poiché i dati vengono recapitati in forma vettoriale, per preparare i dati è necessaria una minore elaborazione sul lato server. Di conseguenza, i dati più recenti possono essere resi più veloci.

Tutti i livelli che usano un'origine vettore devono specificare `sourceLayer` un valore.

Una volta create, le origini dati possono essere aggiunte alla mappa tramite `map.sources` la proprietà, che è un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Nel codice seguente viene illustrato come creare un `DataSource` oggetto e aggiungerlo alla mappa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Mappe di Azure rispetta la [specifica del riquadro vettoriale MapBox](https://github.com/mapbox/vector-tile-spec), uno standard aperto.

## <a name="connecting-a-data-source-to-a-layer"></a>Connessione di un'origine dati a un livello

Viene eseguito il rendering dei dati sulla mappa utilizzando i livelli di rendering. È possibile fare riferimento a una singola origine dati da uno o più livelli di rendering. Per i seguenti livelli di rendering è necessaria un'origine dati:

- [Bubble layer](map-add-bubble-layer.md) : esegue il rendering dei dati punto come cerchi ridimensionati sulla mappa.
- [Livello simboli](map-add-pin.md) : esegue il rendering dei dati del punto come icone o testo.
- [Livello mappa termica](map-add-heat-map-layer.md) : esegue il rendering dei dati punto come mappa termica della densità.
- [Livello linea](map-add-shape.md) : esegue il rendering di una linea e o esegue il rendering della struttura dei poligoni. 
- [Livello poligono](map-add-shape.md) : riempie l'area di un poligono con un colore a tinta unita o un modello di immagine.

Il codice seguente illustra come creare un'origine dati, aggiungerla alla mappa e connetterla a un livello Bubble. Quindi, importare i dati del punto GeoJSON da una posizione remota nell'origine dati. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Sono disponibili livelli di rendering aggiuntivi che non si connettono a queste origini dati, ma caricano direttamente i dati per il rendering. 

- [Livello immagine](map-add-image-layer.md) : sovrappone una singola immagine nella parte superiore della mappa e ne associa gli angoli a un set di coordinate specificate.
- [Livello sezione](map-add-tile-layer.md) : sovrappone un livello sezione raster sopra la mappa.

## <a name="one-data-source-with-multiple-layers"></a>Un'origine dati con più livelli

È possibile connettere più livelli a un'unica origine dati. Questa opzione è utile in molti scenari diversi. Si consideri, ad esempio, lo scenario in cui un utente disegna un poligono. È necessario eseguire il rendering e riempire l'area del poligono quando l'utente aggiunge punti alla mappa. L'aggiunta di una linea con stile per delineare il poligono consente di visualizzare più facilmente i bordi del poligono, quando viene disegnato dall'utente. Per modificare agevolmente una singola posizione nel poligono, è possibile aggiungere un handle, ad esempio un PIN o un marcatore, al di sopra di ogni posizione.

![Mappa che Mostra più livelli di rendering dei dati da una singola origine dati](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Nella maggior parte delle piattaforme di mapping sono necessari un oggetto poligono, un oggetto linea e un PIN per ogni posizione nel poligono. Quando si modifica il poligono, è necessario aggiornare manualmente la linea e i pin, che possono diventare rapidamente complessi.

Con le mappe di Azure, è sufficiente un singolo poligono in un'origine dati, come illustrato nel codice riportato di seguito.

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
> [Aggiungere un livello di simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello Bubble](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)