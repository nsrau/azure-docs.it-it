---
title: Aggiunta di un layer mappa termica a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come aggiungere un layer mappa termica a una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208540"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati. Vengono utilizzati per rappresentare la densità dei dati utilizzando una gamma di colori e mostrare i dati "punti caldi" su una mappa. Le mappe termiche sono un ottimo modo per eseguire il rendering di set di dati con un numero elevato di punti. 

Il rendering di decine di migliaia di punti come simboli può coprire la maggior parte dell'area della mappa. Questo caso comporta probabilmente la sovrapposizione di molti simboli. Rendere difficile ottenere una migliore comprensione dei dati. Tuttavia, la visualizzazione di questo stesso set di dati come mappa termica semplifica la visualizzazione della densità e della densità relativa di ogni punto dati.

È possibile utilizzare le mappe termiche in molti scenari diversi, tra cui:You can use heat maps in many different scenarios, including:

- **Dati di temperatura**: Fornisce approssimazioni per la temperatura tra due punti dati.
- **Dati per i sensori**di rumore : Mostra non solo l'intensità del rumore in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in qualsiasi sito potrebbe non essere elevato. Se l'area di copertura del rumore da più sensori si sovrappone, è possibile che quest'area sovrapposta possa riscontrare livelli di rumore più elevati. Di conseguenza, l'area sovrapposta sarebbe visibile nella mappa termica.
- **Traccia GPS**: Include la velocità come mappa dell'altezza ponderata, dove l'intensità di ogni punto dati si basa sulla velocità. Ad esempio, questa funzionalità fornisce un modo per vedere dove un veicolo stava accelerando.

> [!TIP]
> Per impostazione predefinita, i layer della mappa termica eseguono il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il layer in modo che eseggi solo le feature geometria punto, impostate la `filter` proprietà del layer su `['==', ['geometry-type'], 'Point']`. Se si desidera includere anche feature MultiPoint, impostare la `filter` proprietà del layer su `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come `HeatMapLayer` mappa termica, passare l'origine dati in un'istanza della classe e aggiungerla alla mappa.

Nel codice seguente, ogni punto di calore ha un raggio di 10 pixel a tutti i livelli di zoom. Per garantire una migliore esperienza utente, la mappa termica si trova al di sotto del layer dell'etichetta. Le etichette rimangono chiaramente visibili. I dati di questo esempio provengono dal [programma USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). È per i terremoti significativi che si sono verificati negli ultimi 30 giorni.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Ecco l'esempio di codice in esecuzione completo del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalizzare il layer della mappa termica

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il layer della mappa termica offre diverse opzioni per la personalizzazione, tra cui:

* `radius`: definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. È possibile impostare il raggio come numero fisso o come espressione. Utilizzando un'espressione, è possibile scalare il raggio in base al livello di zoom e rappresentare un'area spaziale coerente sulla mappa (ad esempio, un raggio di 5 miglia).
* `color`: specifica la modalità di colorazione della mappa termica. Un gradiente di colore è una caratteristica comune delle mappe termiche. È possibile ottenere l'effetto con un'espressione. `interpolate` È inoltre possibile `step` utilizzare un'espressione per colorare la mappa termica, suddividendo visivamente la densità in intervalli simili a una mappa di stile di contorno o radar. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. 

  Specificare i valori di colore per `heatmap-density` le mappe termiche come espressione sul valore. Il colore dell'area in cui non sono presenti dati è definito in corrispondenza dell'indice 0 dell'espressione "Interpolazione" o del colore predefinito di un'espressione "Stepped". È possibile utilizzare questo valore per definire un colore di sfondo. Spesso, questo valore è impostato su trasparente o nero semitrasparente. 
   
  Ecco alcuni esempi di espressioni di colore:

  | Espressione colore di interpolazione | Espressione di colore a gradini | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, 'verde',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, 'giallo',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, 'rosso'<br/>\] |   

- `opacity`: specifica quanto è opaco o trasparente il layer della mappa termica.
- `intensity`: applica un moltiplicatore al peso di ogni punto dati per aumentare l'intensità complessiva della mappa termica. Causa una differenza nel peso dei punti dati, semplificando la visualizzazione.
- `weight`: per impostazione predefinita, tutti i punti dati hanno un peso pari a 1 e sono ponderati allo stesso modo. L'opzione di peso funge da moltiplicatore ed è possibile impostarla come numero o espressione. Se un numero è impostato come peso, è l'equivalenza di posizionare ogni punto dati sulla mappa due volte. Ad esempio, se il peso è 2, la densità raddoppia. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. 

  Tuttavia, se si utilizza un'espressione, il peso di ogni punto dati può essere basato sulle proprietà di ogni punto dati. Si supponga, ad esempio, che ogni punto dati rappresenti un terremoto. Il valore di magnitudo è stato una metrica importante per ogni punto dati sismico. I terremoti accadono tutto il tempo, ma la maggior parte hanno una bassa magnitudo, e non sono notati. Utilizzare il valore di grandezza in un'espressione per assegnare il peso a ogni punto dati. Utilizzando il valore di magnitudo per assegnare il peso, si ottiene una migliore rappresentazione del significato dei terremoti all'interno della mappa termica.
- `source`e `source-layer`: consente di aggiornare l'origine dati.

Ecco uno strumento per testare le diverse opzioni del layer della mappa termica.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mappa termica zoomabile coerente

Per impostazione predefinita, i raggi dei punti dati sottoposti a rendering nel layer della mappa termica hanno un raggio pixel fisso per tutti i livelli di zoom. Quando si esegue lo zoom sulla mappa, i dati vengono aggregati insieme e il layer della mappa termica ha un aspetto diverso. 

Utilizzare `zoom` un'espressione per scalare il raggio per ogni livello di zoom, in modo che ogni punto dati copra la stessa area fisica della mappa. Questa espressione rende il layer della mappa termica più statico e coerente. Ogni livello di zoom della mappa ha il doppio dei pixel verticalmente e orizzontalmente rispetto al livello di zoom precedente. 

Ridimensionando il raggio in modo che raddoppi con ogni livello di zoom si crei una mappa termica che sembra coerente su tutti i livelli di zoom. Per applicare questo `zoom` ridimensionamento, `exponential interpolation` usare con un'espressione di base 2, come illustrato nell'esempio seguente. Ingrandire la mappa per vedere come la mappa termica viene ridimensionata con il livello di zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica zoomabile coerente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mappa termica ingrandibile</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>della penna di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando si abilita il clustering nell'origine dati, i punti vicini l'uno all'altro vengono raggruppati come punti cluster. È possibile utilizzare il numero di punti di ogni cluster come espressione di peso per la mappa termica. Ciò può ridurre significativamente il numero di punti di cui eseguire il rendering. Il numero di punti di `point_count` un cluster viene archiviato in una proprietà della feature punto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se il raggio di clustering è solo di pochi pixel, ci sarebbe una piccola differenza visiva nel rendering. Un raggio maggiore raggruppa più punti in ogni cluster e migliora le prestazioni della mappa termica.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine datiCreate a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
