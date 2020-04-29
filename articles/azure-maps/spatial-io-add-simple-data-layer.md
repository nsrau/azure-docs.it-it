---
title: Aggiungere un livello dati semplice | Mappe Microsoft Azure
description: Informazioni su come aggiungere un livello dati semplice usando il modulo IO spaziale, fornito da Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804487"
---
# <a name="add-a-simple-data-layer"></a>Aggiungere un livello dati semplice

Il modulo IO spaziale fornisce una `SimpleDataLayer` classe. Questa classe rende più semplice il rendering di funzionalità con stile sulla mappa. Può persino eseguire il rendering di set di dati con proprietà di stile e set di dati che contengono tipi geometry misti. Il livello dati semplice raggiunge questa funzionalità eseguendo il wrapping di più livelli di rendering e usando espressioni di stile. Le espressioni di stile cercano le proprietà di stile comuni delle funzionalità all'interno di tali livelli. La `atlas.io.read` funzione e la `atlas.io.write` funzione utilizzano queste proprietà per leggere e scrivere stili in un formato di file supportato. Dopo aver aggiunto le proprietà a un formato di file supportato, il file può essere usato per vari scopi. Ad esempio, il file può essere usato per visualizzare le funzionalità con stile sulla mappa.

Oltre alle funzionalità di applicazione di stili `SimpleDataLayer` , fornisce una funzionalità popup incorporata con un modello popup. Quando si fa clic su una funzionalità, viene visualizzata la finestra popup. Se lo si desidera, è possibile disabilitare la funzionalità popup predefinita. Questo livello supporta inoltre i dati del cluster. Quando si fa clic su un cluster, la mappa ingrandisce il cluster ed espanderla in singoli punti e sottocluster.

La `SimpleDataLayer` classe deve essere utilizzata su set di dati di grandi dimensioni con molti tipi geometry e molti stili applicati alle funzionalità. Se utilizzata, questa classe aggiunge un overhead di sei livelli contenenti espressioni di stile. Ci sono quindi casi in cui è più efficiente usare i livelli di rendering di base. Usare, ad esempio, un livello di base per eseguire il rendering di un paio di tipi geometry e alcuni stili in una funzionalità

## <a name="use-a-simple-data-layer"></a>Usare un livello dati semplice

La `SimpleDataLayer` classe viene utilizzata come gli altri livelli di rendering utilizzati. Il codice seguente illustra come usare un livello dati semplice in una mappa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Aggiungere funzionalità all'origine dati. Quindi, il livello dati semplice rileverà il modo migliore per eseguire il rendering delle funzionalità. Gli stili per le singole funzionalità possono essere impostati come proprietà della funzionalità. Il codice seguente illustra una funzionalità di punto GeoJSON con `color` una proprietà impostata `red`su. 

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

<iframe height="500" style="width: 100%;" scrolling="no" title="Usare il livello dati semplice" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Vedere la penna <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>usare il livello dati semplice</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

La vera potenza del livello dati semplice si presenta nei casi seguenti:

- Sono disponibili diversi tipi di funzionalità in un'origine dati. o
- Le funzionalità del set di dati hanno diverse proprietà di stile impostate singolarmente; o
- Non si è certi del contenuto del set di dati.

Quando si analizzano i feed di dati XML, ad esempio, è possibile che non si conoscano gli stili e i tipi di geometria esatti delle funzionalità. Nell'esempio seguente viene illustrata la potenza del livello dati semplice eseguendo il rendering delle funzionalità di un file KML. Vengono inoltre illustrate varie opzioni offerte dalla classe Simple Data Layer.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni del livello dati semplice" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Vedere le <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Opzioni di Simple Data Layer</a> di penna di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Questo livello dati semplice usa la classe [modello popup](map-add-popup.md#add-popup-templates-to-the-map) per visualizzare i palloni KML o le proprietà delle funzionalità come una tabella. Per impostazione predefinita, tutto il contenuto di cui viene eseguito il rendering nel popup verrà creato mediante sandbox all'interno di un iframe come funzionalità di sicurezza. Esistono tuttavia alcune limitazioni:
>
> - Tutti gli script, i moduli, il blocco del puntatore e la funzionalità di navigazione superiore sono disabilitati. Quando si fa clic sui collegamenti, è possibile aprirli in una nuova scheda. 
> - I browser meno recenti che non `srcdoc` supportano il parametro sugli iframe saranno limitati al rendering di una piccola quantità di contenuto.
> 
> Se si considera attendibili i dati caricati nei popup e potenzialmente si vuole che questi script caricati nei popup siano in grado di accedere all'applicazione, è possibile disabilitare questa impostazione impostando l' `sandboxContent` opzione Popup templates su false. 

## <a name="default-supported-style-properties"></a>Proprietà di stile supportate predefinite

Come indicato in precedenza, il livello dati semplice esegue il wrapping di diversi livelli di rendering principali: bolla, simbolo, linea, poligono e poligono estruso. USA quindi le espressioni per cercare le proprietà di stile valide sulle singole funzionalità.

Mappe di Azure e le proprietà di stile GitHub sono i due set principali di nomi di proprietà supportati. La maggior parte dei nomi delle proprietà delle diverse opzioni del livello mappe di Azure è supportata come proprietà di stile delle funzionalità nel livello dati semplice. Le espressioni sono state aggiunte ad alcune opzioni del livello per supportare i nomi di proprietà di stile usati comunemente da GitHub. Questi nomi di proprietà sono definiti dal [supporto della mappa GeoJSON di GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e vengono usati per applicare uno stile ai file GeoJSON archiviati e sottoposti a rendering all'interno della piattaforma. Tutte le proprietà di stile di GitHub sono supportate nel livello dati semplice, ad eccezione `marker-symbol` delle proprietà di stile.

Se il lettore si trova in una proprietà di stile meno comune, lo convertirà nella proprietà di stile Maps di Azure più vicina. Inoltre, è possibile eseguire l'override delle espressioni di stile predefinite utilizzando `getLayers` la funzione del livello dati semplice e aggiornando le opzioni in uno qualsiasi dei livelli.

Le sezioni seguenti forniscono informazioni dettagliate sulle proprietà di stile predefinite supportate dal livello dati semplice. L'ordine del nome di proprietà supportato è anche la priorità della proprietà. Se per la stessa opzione del livello sono definite due proprietà di stile, il primo nell'elenco avrà una precedenza maggiore.

### <a name="bubble-layer-style-properties"></a>Proprietà stile livello bolla

Se una funzionalità è un `Point` oggetto o `MultiPoint`e la funzionalità non dispone di una `image` proprietà che verrebbe utilizzata come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della funzionalità con un oggetto `BubbleLayer`.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] i `size` valori `scale` e sono considerati valori scalari e verranno moltiplicati per`8`

\[2\] se si specifica `marker-size` l'opzione GitHub, per il raggio verranno usati i valori seguenti.

| Dimensioni marcatore | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Viene anche eseguito il rendering dei cluster usando il livello Bubble. Per impostazione predefinita, il raggio di un cluster è `16`impostato su. Il colore del cluster varia a seconda del numero di punti del cluster, come definito di seguito:

| numero di punti | Colore    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Proprietà stile simbolo

Se una funzionalità è un `Point` oggetto o `MultiPoint`e la funzionalità e ha una `image` proprietà che verrebbe usata come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della funzionalità con un oggetto. `SymbolLayer`

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] se si specifica `marker-size` l'opzione GitHub, verranno usati i valori seguenti per l'opzione delle dimensioni dell'icona.

| Dimensioni marcatore | Dimensioni simbolo |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se la funzionalità punto è un cluster, verrà `point_count_abbreviated` eseguito il rendering della proprietà come etichetta di testo. Non verrà eseguito il rendering di un'immagine.

### <a name="line-style-properties"></a>Proprietà stile linea

Se `LineString`la funzionalità è, `MultiLineString` `Polygon`, o `MultiPolygon`, verrà eseguito il rendering della funzionalità con un oggetto. `LineLayer`

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Proprietà stile poligono

Se la funzionalità è di `Polygon` tipo o `MultiPolygon`e la funzionalità non ha una `height` proprietà o la `height` proprietà è zero, verrà eseguito il rendering della funzionalità con un oggetto. `PolygonLayer`

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Proprietà stile poligono estruso

Se la funzionalità è un `Polygon` oggetto o `MultiPolygon`e ha una `height` proprietà con un valore maggiore di 0, verrà eseguito il rendering della funzionalità con un `PolygonExtrusionLayer`oggetto.

| Opzione layer | Nomi di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

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
