---
title: Aggiunta di un livello dati semplice Mappe di Microsoft Azure
description: Informazioni su come aggiungere un livello dati semplice usando il modulo I/O spaziale, fornito da Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804487"
---
# <a name="add-a-simple-data-layer"></a>Aggiungere un livello dati semplice

Il modulo IO `SimpleDataLayer` spaziale fornisce una classe. Questa classe semplifica il rendering delle feature con stile sulla mappa. Può anche eseguire il rendering di set di dati con proprietà di stile e set di dati che contengono tipi di geometria misti. Il livello dati semplice consente di ottenere questa funzionalità eseguendo il wrapping di più livelli di rendering e utilizzando espressioni di stile. Le espressioni di stile cercano le proprietà di stile comuni delle feature all'interno di questi layer con ritorno a capo. La `atlas.io.read` funzione `atlas.io.write` e la funzione utilizzano queste proprietà per leggere e scrivere gli stili in un formato di file supportato. Dopo aver aggiunto le proprietà a un formato di file supportato, il file può essere utilizzato per vari scopi. Ad esempio, il file può essere utilizzato per visualizzare le feature con stili sulla mappa.

Oltre alle funzionalità di `SimpleDataLayer` stile, fornisce una funzionalità popup incorporata con un modello popup. Il popup viene visualizzato quando si fa clic su una funzione. La funzione popup predefinita può essere disabilitata, se lo si desidera. Questo livello supporta anche i dati cluster. Quando si fa clic su un cluster, la mappa ingrandirà il cluster e lo espanderà in singoli punti e sottocluster.

La `SimpleDataLayer` classe deve essere utilizzata su set di dati di grandi dimensioni con molti tipi di geometria e molti stili applicati alle entità geografiche. Se utilizzata, questa classe aggiunge un overhead di sei livelli contenenti espressioni di stile. Quindi, ci sono casi in cui è più efficiente utilizzare i livelli di rendering di base. Ad esempio, utilizzare un layer principale per eseguire il rendering di un paio di tipi di geometria e alcuni stili su una feature

## <a name="use-a-simple-data-layer"></a>Usare un livello dati semplice

La `SimpleDataLayer` classe viene utilizzata come gli altri livelli di rendering. Il codice seguente mostra come usare un livello dati semplice in una mappa:The code below shows how to use a simple data layer in a map:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Aggiungere funzionalità all'origine dati. Quindi, il semplice livello dati capirà il modo migliore per eseguire il rendering delle feature. Gli stili per le singole entità geografiche possono essere impostati come proprietà della feature. Nel codice seguente viene illustrata `color` una feature `red`punto GeoJSON con una proprietà impostata su . 

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

Il codice seguente esegue il rendering della feature del punto precedente usando il livello dati semplice. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Usare il livello dati semplice" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Vedere La penna Usare il livello<a href='https://codepen.io/azuremaps'>@azuremaps</a>dati <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>semplice</a> di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

La vera potenza del semplice livello dati viene quando:

- Esistono diversi tipi di funzionalità in un'origine dati; O
- Le entità geografiche nel set di dati hanno diverse proprietà di stile impostate singolarmente su di esse; O
- Non sei sicuro di cosa contenga esattamente il set di dati.

Ad esempio, durante l'analisi dei feed di dati XML, è possibile che non si conoscano gli stili e i tipi di geometria esatti delle feature. L'esempio seguente mostra la potenza del livello dati semplice eseguendo il rendering delle funzionalità di un file KML. Vengono inoltre illustrate varie opzioni fornite dalla classe del livello dati semplice.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni semplici per il livello dati" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Vedere le opzioni del livello dati<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>Simple</a> di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Questo semplice livello dati utilizza la classe [modello popup](map-add-popup.md#add-popup-templates-to-the-map) per visualizzare le bollature KML o le proprietà delle entità geografiche come tabella. Per impostazione predefinita, tutto il contenuto di cui è stato eseguito il rendering nel popup verrà in modalità sandbox all'interno di un iframe come funzionalità di sicurezza. Tuttavia, esistono limitazioni:However, there are limitations:
>
> - Tutti gli script, i moduli, il blocco del puntatore e la funzionalità di navigazione superiore sono disabilitati. Quando si fa clic su si fa clic, è possibile che i collegamenti si aprano in una nuova scheda. 
> - I browser meno recenti che `srcdoc` non supportano il parametro negli iframe saranno limitati al rendering di una piccola quantità di contenuto.
> 
> Se si considerano attendibili i dati caricati nei popup e potenzialmente si desidera che questi script caricati `sandboxContent` in popup siano in grado di accedere all'applicazione, è possibile disabilitarlo impostando l'opzione dei modelli popup su false. 

## <a name="default-supported-style-properties"></a>Proprietà di stile supportate predefinite

Come accennato in precedenza, il livello dati semplice esegue il wrapping di diversi livelli di rendering principali: bolla, simbolo, linea, poligono e poligono estruso. Vengono quindi utilizzate le espressioni per cercare proprietà di stile valide nelle singole entità geografiche.

Le proprietà di stile Mappe di Azure e GitHub sono i due set principali di nomi di proprietà supportati. La maggior parte dei nomi delle proprietà delle diverse opzioni del layer delle mappe di Azure è supportata come proprietà di stile delle feature nel livello dati semplice. Sono state aggiunte espressioni ad alcune opzioni layer per supportare i nomi delle proprietà di stile comunemente utilizzati da GitHub.Expressions are been added to some layer options to support style property names that are commonly used by GitHub. Questi nomi di proprietà sono definiti dal supporto della [mappa GeoJSON](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)di GitHub e vengono utilizzati per applicare uno stile ai file GeoJSON archiviati e sottoposti a rendering all'interno della piattaforma. Tutte le proprietà di stile di GitHub sono supportate nel livello dati semplice, ad eccezione delle `marker-symbol` proprietà di stile.

Se il lettore incontra una proprietà di stile meno comune, la convertirà nella proprietà di stile di Azure Maps più vicina. Inoltre, le espressioni di stile predefinite possono `getLayers` essere sostituite utilizzando la funzione del livello dati semplice e aggiornando le opzioni su uno qualsiasi dei layer.

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà di stile predefinite supportate dal livello dati semplice. L'ordine del nome della proprietà supportata è anche la priorità della proprietà. Se per la stessa opzione layer sono definite due proprietà di stile, la prima nell'elenco ha una precedenza maggiore.

### <a name="bubble-layer-style-properties"></a>Proprietà dello stile di livello bolla

Se una feature `Point` è `MultiPoint`una o un oggetto `image` , e non dispone di una proprietà che verrebbe utilizzata come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della feature con un `BubbleLayer`oggetto .

| Opzione Livello | Nome/i di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size` <sup>2</sup>, `scale`2 , <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` I `scale` valori e sono considerati valori scalari e verranno moltiplicati per`8`

\[2\] Se viene `marker-size` specificata l'opzione GitHub, per il raggio verranno utilizzati i seguenti valori.

| Dimensioni marcatore | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Il rendering dei cluster viene eseguito anche utilizzando il livello a bolle. Per impostazione predefinita, il raggio `16`di un cluster è impostato su . Il colore del cluster varia a seconda del numero di punti nel cluster, come definito di seguito:

| N. di punti | Colore    |
|-------------|----------|
| &gt;100 USD   | `red`    |
| &gt;10 USD    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Proprietà dello stile dei simboli

Se una feature `Point` è `MultiPoint`una o un `image` oggetto , e la feature e dispone di una proprietà che verrebbe utilizzata `SymbolLayer`come icona personalizzata per eseguire il rendering del punto come simbolo, verrà eseguito il rendering della feature con un oggetto .

| Opzione Livello | Nome/i di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size``marker-size` <sup>, 1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Se viene `marker-size` specificata l'opzione GitHub, per l'opzione delle dimensioni delle icone verranno utilizzati i seguenti valori.

| Dimensioni marcatore | Dimensioni simbolo |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se la feature punto è `point_count_abbreviated` un cluster, il rendering della proprietà verrà eseguito come etichetta di testo. Non verrà eseguito il rendering di alcuna immagine.

### <a name="line-style-properties"></a>Proprietà dello stile di linea

Se la funzione `LineString` `MultiLineString`è `Polygon`, `MultiPolygon`, o , verrà `LineLayer`eseguito il rendering della funzione con un file .

| Opzione Livello | Nome/i di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Proprietà dello stile poligono

Se la funzionalità `Polygon` è `MultiPolygon`a o un , e `height` la `height` funzionalità non dispone di una proprietà `PolygonLayer`o la proprietà è zero, il rendering della funzionalità verrà eseguito con un oggetto .

| Opzione Livello | Nome/i di proprietà supportati | Valore predefinito |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Proprietà di stile poligono estrusi

Se la funzionalità `Polygon` è `MultiPolygon`un oggetto `height` o un oggetto , e dispone di `PolygonExtrusionLayer`una proprietà con un valore maggiore di 0, verrà eseguito il rendering della funzionalità con un oggetto .

| Opzione Livello | Nome/i di proprietà supportati | Valore predefinito |
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
> [Lettura e scrittura di dati spaziali](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connettersi a un servizio WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Sfruttare le operazioni di base](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Dettagli sul formato dati supportati](spatial-io-supported-data-format-details.md)
