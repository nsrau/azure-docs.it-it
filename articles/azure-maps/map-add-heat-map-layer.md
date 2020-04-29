---
title: Aggiungere un livello mappa termica a una mappa | Mappe Microsoft Azure
description: In questo articolo si apprenderà come aggiungere un livello mappa termica a una mappa usando il Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804555"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati. Vengono usati per rappresentare la densità dei dati usando un intervallo di colori e visualizzare i dati "aree sensibili" su una mappa. Le mappe termiche sono un ottimo modo per eseguire il rendering dei set di impostazioni con un numero elevato di punti. 

Il rendering di decine di migliaia di punti come simboli può coprire la maggior parte dell'area della mappa. Questo caso probabilmente comporta la sovrapposizione di molti simboli. Rendendo difficile acquisire una migliore comprensione dei dati. Tuttavia, la visualizzazione di questo stesso set di dati come mappa termica semplifica la visualizzazione della densità e della densità relativa di ogni punto dati.

È possibile usare le mappe termiche in molti scenari diversi, tra cui:

- **Data temperature**: fornisce le approssimazioni per la temperatura tra due punti dati.
- **Dati per i sensori di rumore**: Mostra non solo l'intensità del rumore in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione a distanza. Il livello di disturbo in un sito potrebbe non essere elevato. Se l'area di copertura del rumore da più sensori si sovrappone, è possibile che questa area sovrapposta possa riscontrare livelli di rumore più elevati. Di conseguenza, l'area sovrapposta è visibile nella mappa termica.
- **Traccia GPS**: include la velocità come mappa con altezza ponderata, in cui l'intensità di ogni punto dati è basata sulla velocità. Questa funzionalità, ad esempio, consente di visualizzare la posizione in cui un veicolo è stato velocizzato.

> [!TIP]
> Per impostazione predefinita, i livelli mappa termica eseguono il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità di geometria dei `filter` punti, impostare la proprietà `['==', ['geometry-type'], 'Point']`del livello su. Se si desidera includere anche le funzionalità MultiPoint, impostare la `filter` proprietà del livello su. `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come mappa termica, passare l'origine dati in un'istanza `HeatMapLayer` della classe e aggiungerla alla mappa.

Nel codice seguente, ogni punto di riscaldamento ha un raggio di 10 pixel a tutti i livelli di zoom. Per garantire un'esperienza utente migliore, la mappa termica si trova al di sotto del livello Label. Le etichette vengono visualizzate chiaramente. I dati in questo esempio sono del [programma USGS Earthquake Hazards](https://earthquake.usgs.gov/). Si tratta di terremoti significativi che si sono verificati negli ultimi 30 giorni.

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

Ecco l'esempio di codice completo per l'esecuzione del codice precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Personalizzare il livello mappa termica

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello mappa termica offre diverse opzioni per la personalizzazione, tra cui:

* `radius`: Definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. È possibile impostare il raggio come numero fisso o come espressione. Utilizzando un'espressione, è possibile ridimensionare il raggio in base al livello di zoom e rappresentare un'area spaziale coerente sulla mappa, ad esempio un raggio di 5 chilometri.
* `color`: Specifica la modalità di colorazione della mappa termica. Una sfumatura di colore è una funzionalità comune delle mappe termiche. È possibile ottenere l'effetto con un' `interpolate` espressione. È anche possibile usare un' `step` espressione per colorare la mappa termica, suddividendo in modo visivo la densità in intervalli simili a una mappa di contorno o di stile radar. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. 

  È possibile specificare i valori dei colori per le `heatmap-density` mappe termiche come espressione sul valore. Il colore dell'area in cui non sono stati definiti dati in corrispondenza dell'indice 0 dell'espressione "interpolazione" o del colore predefinito di un'espressione "con rientri". È possibile utilizzare questo valore per definire un colore di sfondo. Spesso, questo valore è impostato su Transparent o su un nero semi-trasparente. 
   
  Di seguito sono riportati alcuni esempi di espressioni colori:

  | Espressione colore interpolazione | Espressione colore con rientri | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25,' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50,' Yellow ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75,' Red '<br/>\] |   

- `opacity`: Specifica come opaco o trasparente il livello mappa termica.
- `intensity`: Applica un moltiplicatore al peso di ogni punto dati per aumentare l'intensità complessiva del mappa termica. Causa una differenza nel peso dei punti dati, semplificando la visualizzazione.
- `weight`: Per impostazione predefinita, tutti i punti dati hanno un peso di 1 e sono ponderati equamente. L'opzione Weight funge da moltiplicatore ed è possibile impostarla come numero o espressione. Se un numero è impostato come peso, è l'equivalenza di posizionare due volte ogni punto dati sulla mappa. Ad esempio, se il peso è 2, la densità raddoppia. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. 

  Tuttavia, se si utilizza un'espressione, il peso di ogni punto dati può essere basato sulle proprietà di ogni punto dati. Si supponga, ad esempio, che ogni punto dati rappresenti un terremoto. Il valore magnitude è un'importante metrica per ogni punto dati sismico. I terremoti si verificano sempre, ma hanno una grandezza bassa e non vengono rilevati. Usare il valore magnitude in un'espressione per assegnare il peso a ogni punto dati. Usando il valore Magnitude per assegnare il peso, si ottiene una rappresentazione migliore dell'importanza dei terremoti all'interno della mappa termica.
- `source`e `source-layer`: consentono di aggiornare l'origine dati.

Ecco uno strumento per testare le diverse opzioni del livello mappa termica.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mappa termica Zoomable coerente

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Quando si esegue lo zoom della mappa, i dati vengono aggregati insieme e il livello mappa termica è diverso. 

Utilizzare un' `zoom` espressione per ridimensionare il raggio per ogni livello di zoom, in modo che ogni punto dati copra la stessa area fisica della mappa. Questa espressione rende il livello mappa termica più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente. 

Il ridimensionamento del raggio in modo che raddoppi a ogni livello di zoom crea una mappa termica che sembra coerente in tutti i livelli di zoom. Per applicare questa scala, utilizzare `zoom` con un'espressione base `exponential interpolation` 2, con il raggio di pixel impostato per il livello di zoom minimo e un raggio scalato per il livello di `2 * Math.pow(2, minZoom - maxZoom)` zoom massimo calcolato come illustrato nell'esempio seguente. Eseguire lo zoom della mappa per vedere come la mappa termica viene ridimensionata con il livello di zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica Zoomable coerente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedi la <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mappa termica compatibile</a> con la penna con le mappe di<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando si Abilita il clustering nell'origine dati, i punti vicini tra loro vengono raggruppati come punto cluster. È possibile utilizzare il conteggio punti di ogni cluster come espressione di ponderazione per la mappa termica. Questo può ridurre significativamente il numero di punti di cui eseguire il rendering. Il numero di punti di un cluster viene archiviato in `point_count` una proprietà della funzionalità punto: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Se il raggio di clustering è solo pochi pixel, il rendering comporterebbe una piccola differenza visiva. Un raggio più grande raggruppa più punti in ogni cluster e migliora le prestazioni di mappa termica.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)
