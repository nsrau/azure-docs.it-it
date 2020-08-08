---
title: Creare un'origine dati per una mappa | Mappe Microsoft Azure
description: "Informazioni su come creare un'origine dati per una mappa. Informazioni sulle origini dati usate da Azure Maps Web SDK: origini GeoJSON e riquadri vettoriali."
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: fea2c4fab51db59c9159853e9b0bdaec0bcdbb56
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009087"
---
# <a name="create-a-data-source"></a>Creare un'origine dati

Azure Maps Web SDK archivia i dati nelle origini dati. L'utilizzo di origini dati ottimizza le operazioni di dati per l'esecuzione di query e il rendering. Attualmente esistono due tipi di origini dati:

- **Origine GeoJSON**: gestisce i dati di posizione non elaborati in formato GeoJSON localmente. Ideale per set di dati di piccole e medie dimensioni (a partire da centinaia di migliaia di forme).
- **Origine del riquadro vettoriale**: carica i dati formattati come riquadri vettoriali per la vista mappa corrente, in base al sistema di affiancamento mappe. Ideale per set di dati di grandi dimensioni (milioni o miliardi di forme).

## <a name="geojson-data-source"></a>Origine dati GeoJSON

Un'origine dati basata su GeoJSON carica e archivia i dati localmente usando la `DataSource` classe. I dati GeoJSON possono essere creati o creati manualmente usando le classi helper nello spazio dei nomi [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . La `DataSource` classe fornisce funzioni per l'importazione di file GeoJSON locali o remoti. I file GeoJSON remoti devono essere ospitati in un endpoint abilitato per CORs. La `DataSource` classe fornisce funzionalità per i dati dei punti di clustering. E, i dati possono essere facilmente aggiunti, rimossi e aggiornati con la `DataSource` classe. Il codice seguente mostra come è possibile creare i dati GeoJSON in mappe di Azure.

```Javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Una volta create, le origini dati possono essere aggiunte alla mappa tramite la `map.sources` proprietà, che è un [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Nel codice seguente viene illustrato come creare un oggetto `DataSource` e aggiungerlo alla mappa.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Il codice seguente illustra i diversi modi in cui i dati GeoJSON possono essere aggiunti a un `DataSource` .

```Javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
dataSource.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
dataSource.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
dataSource.setShapes(geoJsonData);
```

> [!TIP]
> Si supponga di voler sovrascrivere tutti i dati in un `DataSource` . Se si effettuano chiamate alle `clear` `add` funzioni then, è possibile che venga nuovamente eseguito il rendering della mappa due volte, il che potrebbe causare un po' di ritardo. Usare invece la `setShapes` funzione, che rimuoverà e sostituirà tutti i dati nell'origine dati e attiverà un solo nuovo rendering della mappa.

## <a name="vector-tile-source"></a>Origine riquadro vettoriale

Un'origine del riquadro vettoriale descrive come accedere a un livello tessera vettoriale. Usare la classe [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) per creare un'istanza di un'origine del riquadro vettoriale. I livelli dei riquadri vettoriali sono simili ai livelli affiancati, ma non sono uguali. Un livello sezione è un'immagine raster. I livelli del riquadro vettoriale sono file compressi in formato **PBF** . Questo file compresso contiene i dati della mappa vettoriale e uno o più livelli. È possibile eseguire il rendering e lo stile del file nel client, in base allo stile di ogni livello. I dati in un riquadro vettoriale contengono funzionalità geografiche sotto forma di punti, linee e poligoni. Esistono diversi vantaggi derivanti dall'uso di livelli di tessera vettoriale invece dei livelli di riquadri raster:

 - Una dimensione del file di un riquadro vettoriale è in genere molto più piccola rispetto a un riquadro raster equivalente. Di conseguenza, viene utilizzata una minore larghezza di banda. Ovvero una latenza più bassa, una mappa più veloce e un'esperienza utente migliore.
 - Poiché viene eseguito il rendering dei riquadri vettoriali nel client, si adattano alla risoluzione del dispositivo in cui vengono visualizzati. Di conseguenza, le mappe sottoposte a rendering vengono visualizzate in modo più ben definito, con etichette Clear Crystal.
 - Per modificare lo stile dei dati nelle mappe vettoriali, non è necessario scaricare di nuovo i dati, perché il nuovo stile può essere applicato nel client. Al contrario, la modifica dello stile di un livello sezione raster richiede in genere il caricamento di riquadri dal server, quindi l'applicazione del nuovo stile.
 - Poiché i dati vengono recapitati in forma vettoriale, per preparare i dati è necessaria una minore elaborazione sul lato server. Di conseguenza, i dati più recenti possono essere resi più veloci.

Mappe di Azure rispetta la [specifica del riquadro vettoriale MapBox](https://github.com/mapbox/vector-tile-spec), uno standard aperto. Azure Maps fornisce i servizi dei riquadri vettoriali seguenti come parte della piattaforma:

- Dettagli del [documentation](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)  |  [formato dei dati](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) della documentazione sui riquadri stradali
- [documentation](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)  |  [Dettagli del formato dati](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) della documentazione sugli eventi imprevisti del traffico
- Dettagli del [documentation](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)  |  [formato dati](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) della documentazione del flusso di traffico
- Azure Maps Creator consente inoltre la creazione e l'accesso ai riquadri vettoriali personalizzati tramite il [rendering del riquadro Get V2](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Quando si usano i riquadri di immagini Vector o raster del servizio di rendering di Azure Maps con SDK Web, è possibile sostituire `atlas.microsoft.com` con il segnaposto `{azMapsDomain}` . Questo segnaposto verrà sostituito con lo stesso dominio utilizzato dalla mappa e aggiungerà automaticamente anche i dettagli di autenticazione. Questo semplifica notevolmente l'autenticazione con il servizio di rendering quando si usa l'autenticazione Azure Active Directory.

Per visualizzare i dati da un'origine del riquadro vettoriale sulla mappa, connettere l'origine a uno dei livelli di rendering dei dati. Tutti i livelli che usano un'origine vettore devono specificare un `sourceLayer` valore nelle opzioni. Il codice seguente carica il servizio Tile del vettore del flusso di traffico di Azure Maps come origine del riquadro vettoriale, quindi lo Visualizza su una mappa usando un livello linea. Questa origine del riquadro vettoriale dispone di un singolo set di dati nel livello di origine denominato "flusso del traffico". Nei dati della riga di questo set di dati è presente una proprietà denominata `traffic_level` utilizzata in questo codice per selezionare il colore e ridimensionare le linee.

```javascript
//Create a vector tile source and add it to the map.
var datasource = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(datasource);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(datasource, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello linea riquadro vettoriale" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>livello linea del riquadro</a> della penna vettoriale di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

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
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere una mappa termica](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)