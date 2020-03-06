---
title: Aggiungere un livello dati semplice | Mappe Microsoft Azure
description: Informazioni su come aggiungere un livello dati semplice usando il modulo IO spaziale, fornito da Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370986"
---
# <a name="add-a-simple-data-layer"></a>Aggiungere un livello dati semplice

Il modulo IO spaziale fornisce una classe `SimpleDataLayer`. Questa classe rende più semplice il rendering di funzionalità con stile sulla mappa. Può persino eseguire il rendering di set di dati con proprietà di stile e set di dati che contengono tipi geometry misti. Il livello dati semplice raggiunge questa funzionalità eseguendo il wrapping di più livelli di rendering e usando espressioni di stile. Le espressioni di stile cercano le proprietà di stile comuni delle funzionalità all'interno di tali livelli. La funzione `atlas.io.read` e la funzione `atlas.io.write` usano queste proprietà per leggere e scrivere stili in un formato di file supportato. Dopo aver aggiunto le proprietà a un formato di file supportato, il file può essere usato per vari scopi. Ad esempio, il file può essere usato per visualizzare le funzionalità con stile sulla mappa.

Oltre alle funzionalità di applicazione di stili, il `SimpleDataLayer` fornisce una funzionalità popup incorporata con un modello popup. Quando si fa clic su una funzionalità, viene visualizzata la finestra popup. Se lo si desidera, è possibile disabilitare la funzionalità popup predefinita. Questo livello supporta inoltre i dati del cluster. Quando si fa clic su un cluster, la mappa ingrandisce il cluster ed espanderla in singoli punti e sottocluster.

La classe `SimpleDataLayer` è progettata per essere utilizzata su set di dati di grandi dimensioni con molti tipi geometry e molti stili applicati alle funzionalità. Se utilizzata, questa classe aggiunge un overhead di sei livelli contenenti espressioni di stile. Ci sono quindi casi in cui è più efficiente usare i livelli di rendering di base. Usare, ad esempio, un livello di base per eseguire il rendering di un paio di tipi geometry e alcuni stili in una funzionalità

## <a name="default-supported-style-properties"></a>Proprietà di stile supportate predefinite

Come indicato in precedenza, il livello dati semplice esegue il wrapping di diversi livelli di rendering principali: bolla, simbolo, linea, poligono e poligono estruso. USA quindi le espressioni per cercare le proprietà di stile valide sulle singole funzionalità.

Mappe di Azure e le proprietà di stile GitHub sono i due set principali di nomi di proprietà supportati. La maggior parte dei nomi delle proprietà delle diverse opzioni del livello mappe di Azure è supportata come proprietà di stile delle funzionalità nel livello dati semplice. Le espressioni sono state aggiunte ad alcune opzioni del livello per supportare i nomi di proprietà di stile usati comunemente da GitHub. Questi nomi di proprietà sono definiti dal [supporto della mappa GeoJSON di GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e vengono usati per applicare uno stile ai file GeoJSON archiviati e sottoposti a rendering all'interno della piattaforma. Tutte le proprietà di stile di GitHub sono supportate nel livello dati semplice, ad eccezione delle proprietà di stile `marker-symbol`.

Se il lettore rileva una proprietà di stile meno comune, la convertirà nella proprietà dello stile Maps di Azure più vicina. Inoltre, è possibile eseguire l'override delle espressioni di stile predefinite utilizzando la funzione `getLayers` del livello dati semplice e aggiornando le opzioni in uno qualsiasi dei livelli.

Nella sezione successiva vengono fornite informazioni dettagliate sulle proprietà di stile predefinite supportate dal livello dati semplice. L'ordine del nome di proprietà supportato è anche la priorità della proprietà. Se per la stessa opzione del livello sono definite due proprietà di stile, il primo nell'elenco avrà una precedenza maggiore.

## <a name="simple-data-layer-options"></a>Opzioni del livello dati semplice

### <a name="bubble-layer-style-properties"></a>Proprietà stile livello bolla

Se una funzionalità è un `Point` o un `MultiPoint`e la funzionalità non dispone di una proprietà `image` che verrebbe utilizzata come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della funzionalità con un `BubbleLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] i valori di `size` e `scale` sono considerati valori scalari e verranno moltiplicati per `8`

\[2\] se si specifica l'opzione GitHub `marker-size`, per il raggio verranno usati i valori seguenti.

| Dimensioni marcatore | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Viene anche eseguito il rendering dei cluster usando il livello Bubble. Per impostazione predefinita, il raggio di un cluster è impostato su `16`. Il colore del cluster varia a seconda del numero di punti del cluster, come definito di seguito:

| numero di punti | Colore    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Proprietà stile simbolo

Se una funzionalità è un `Point` o un `MultiPoint`e la funzionalità e ha una proprietà `image` che verrebbe usata come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della funzionalità con un `SymbolLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] se è specificata l'opzione GitHub `marker-size`, verranno usati i valori seguenti per l'opzione delle dimensioni dell'icona.

| Dimensioni marcatore | Dimensioni simboli |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se la funzionalità punto è un cluster, verrà eseguito il rendering della proprietà `point_count_abbreviated` come etichetta di testo. Non verrà eseguito il rendering di un'immagine.

### <a name="line-style-properties"></a>Proprietà stile linea

Se la funzionalità è un `LineString`, `MultiLineString`, `Polygon`o `MultiPolygon`, verrà eseguito il rendering della funzionalità con un `LineLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Proprietà stile poligono

Se la funzionalità è un `Polygon` o un `MultiPolygon`e la funzionalità non dispone di una proprietà `height` o se la proprietà `height` è zero, verrà eseguito il rendering della funzionalità con un `PolygonLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`,'`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Proprietà stile poligono estruso

Se la funzionalità è un `Polygon` o un `MultiPolygon`e ha una proprietà `height` con un valore maggiore di 0, verrà eseguito il rendering della funzionalità con un `PolygonExtrusionLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Usare un livello dati semplice

La classe `SimpleDataLayer` viene utilizzata come gli altri livelli di rendering utilizzati. Il codice seguente illustra come usare un livello dati semplice in una mappa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Aggiungere funzionalità all'origine dati. Quindi, il livello dati semplice rileverà il modo migliore per eseguire il rendering delle funzionalità. Gli stili per le singole funzionalità possono essere impostati come proprietà della funzionalità. Il codice seguente illustra una funzionalità di punto GeoJSON con una proprietà `color` impostata su `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Il codice seguente esegue il rendering della funzionalità del punto precedente usando il livello dati semplice. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Usare il livello dati semplice" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Vedere la penna <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>usare il livello dati semplice</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

La vera potenza del livello dati semplice si presenta nei casi seguenti:

- Sono disponibili diversi tipi di funzionalità in un'origine dati. o
- Le funzionalità del set di dati hanno diverse proprietà di stile impostate singolarmente; o
- Non si è certi del contenuto del set di dati.

Quando si analizzano i feed di dati XML, ad esempio, è possibile che non si conoscano gli stili e i tipi di geometria esatti delle funzionalità. Nell'esempio seguente viene illustrata la potenza del livello dati semplice eseguendo il rendering delle funzionalità di un file KML. Vengono inoltre illustrate varie opzioni offerte dalla classe Simple Data Layer.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni del livello dati semplice" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Per le mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>, vedere le <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Opzioni relative a Simple Data Layer</a> di Pen.
</iframe>


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Lettura e scrittura dei dati spaziali](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni principali](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli del formato dati supportati](spatial-io-supported-data-format-details.md)
