---
title: Aggiungere un livello per le mappe termiche a Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello mappa termica a Azure Maps Web SDK.
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

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.  Ad esempio, il rendering di decine di migliaia di punti all'interno della vista mappa come simboli, copre la maggior parte dell'area della mappa e comporterebbe la sovrapposizione di molti simboli, rendendo difficile ottenere informazioni approfondite sui dati. Tuttavia, visualizzare questo stesso set di dati come una mappa termica semplifica la visualizzzione dei punti in cui i dati sono più densi sistemi e la densità relativa ad altre aree. Esistono molti scenari in cui le mappe termiche vengono usate. Di seguito sono riportati alcuni esempi:

- I dati sulla temperatura vengono visualizzati in genere come mappa termica in quanto fornisce approssimazioni per quanto riguarda la temperatura tra due punti di dati.
- Il rendering dei dati per i sensori di rumore come mappa termica non solo mostra l'intensità del rumore nel punto in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in un sito potrebbe non essere elevato, ma se l'area di copertura acustica di più sensori si sovrappone, è possibile che quest'area di sovrapposizione abbia livelli di rumore più elevati e quindi, sia visibile nella mappa termica.
- Visualizzazione di una traccia GPS che include la velocità come mappa con altezza ponderata in cui l'intensità di ogni punto dati è basata sulla velocità è un ottimo modo per vedere dove il veicolo è stato accelerato.

> [!TIP]
> Per impostazione predefinita, i livelli della mappa termica eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo da eseguire il rendering solo delle funzionalità di geometria dei punti, impostare la proprietà `filter` del livello su `['==', ['geometry-type'], 'Point']` o `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se si desidera includere anche le funzionalità MultiPoint.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come una mappa termica, è sufficiente trasferire l'origine dati in un'istanza della classe `HeatMapLayer` e aggiungerla alla mappa come mostrato di seguito.

Nel codice seguente, ogni punto di riscaldamento ha un raggio di 10 pixel a tutti i livelli di zoom. Quando si aggiunge il livello mappa termica alla mappa, questo esempio lo inserisce sotto il livello etichetta per creare un'esperienza utente migliore perché le etichette sono chiaramente visibili sopra la mappa termica. I dati in questo esempio vengono originati dal [programma dei rischi sismici USGS](https://earthquake.usgs.gov/) e rappresentano terremoti significativi che si sono verificati negli ultimi 30 giorni.

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

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Personalizzazione del livello per le mappe termiche

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello per le mapphe termiche offre diverse opzioni per la personalizzazione;

* `radius`: definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. Il raggio può essere impostato come un numero fisso o un'espressione. Usando un'espressione, è possibile ridimensionare il raggio in base al livello di zoom, che sembra rappresentare un'area spaziale coerente sulla mappa, ad esempio un raggio di 5 chilometri.
* `color`: specifica la modalità di colorazione della mappa termica. Una sfumatura di colore viene spesso usata per le mappe termiche e può essere realizzata con un'espressione `interpolate`. L'uso di un'espressione `step` per colorare la mappa termica suddivide la densità in modo visivo in intervalli più simili a una mappa di contorno o di stile radar. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. I valori di colore per le mappe termiche sono specificati come espressione sul valore `heatmap-density`. Il colore in corrispondenza dell'indice 0 in un'espressione di interpolazione o del colore predefinito di un'espressione di passaggio, definisce il colore dell'area in cui non sono presenti dati e può essere utilizzato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. Ecco alcuni esempi di espressioni di colore;

| Espressione colore interpolazione | Espressione colore graduale | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25,' verde ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50,' Yellow ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75,' Red '<br/>\] | 

- `opacity`: specifica come opaco o trasparente il livello mappa termica.
- `intensity`: applica un moltiplicatore al peso di ogni punto dati per aumentare l'intensità complessiva del mappa termica e contribuisce a rendere più semplice la visualizzazione delle differenze minime nel peso dei punti dati.
- `weight`: per impostazione predefinita, tutti i punti dati hanno un peso di 1, pertanto tutti i punti dati vengono ponderati equamente. L'opzione peso funge da moltiplicatore e può essere impostata come numero o espressione. Se un numero è impostato come peso, ad esempio 2, equivarrebbe a collocare due volte ogni punto dati sulla mappa, raddoppiando così la densità. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. Tuttavia, se viene utilizzata un'espressione, il peso di ogni punto dati può essere basato sulle proprietà di ogni punto dati. Per esempio, nei dati sui terremoti, ogni punto dati rappresenta un terremoto. Una metrica importante di ogni punto dati di un terremoto è il valore della magnitudo. I terremoti si verificano continuamente, ma la maggior parte di essi hanno una bassa magnitudo e non vengono nemmeno percepiti. L'uso del valore magnitude in un'espressione per assegnare il peso a ogni punto dati consentirà la rappresentazione migliore dei terremoti più significativi all'interno della mappa termica.
- Oltre alle opzioni del livello base (zoom minimo/massimo, visualizzazione e filtro), esiste anche l'opzione `source`, se si vuole aggiornare l'origine dati, e l'opzione `source-layer`, se l'origine dati è un'origine della mappa vettoriale.

Ecco uno strumento per testare le diverse opzioni del livello per le mappe termiche.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mappa termica Zoomable coerente

Per impostazione predefinita, i raggi dei punti dati di cui viene eseguito il rendering nel livello mappa termica hanno un raggio fisso di pixel per tutti i livelli di zoom. Quando la mappa viene ingrandita, i dati vengono aggregati insieme e il livello mappa termica ha un aspetto diverso. È possibile utilizzare un'espressione `zoom` per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copra la stessa area fisica della mappa. In questo modo il livello mappa termica risulta più statico e coerente. Ogni livello di zoom della mappa ha una doppia quantità di pixel verticale e orizzontale pari al livello di zoom precedente. Il ridimensionamento del raggio in modo che raddoppi a ogni livello di zoom creerà una mappa termica che risulti coerente in tutti i livelli di zoom. Questa operazione può essere eseguita usando il `zoom` con un'espressione `exponential interpolation` base 2, come illustrato nell'esempio seguente. Eseguire lo zoom della mappa per vedere come la mappa termica viene ridimensionata con il livello di zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica Zoomable coerente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedi la <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mappa termica con zoom coerenti</a> con la penna con mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Abilitando il clustering sull'origine dati, i punti che sono vicini l'uno all'altro sono raggruppati insieme come punto in cluster. Il numero di punti di ogni cluster può essere utilizzato come espressione del peso per la mappa termica e riduce significativamente il numero di punti sui cui eseguire il rendering. Il numero di punti di un cluster viene archiviato in una proprietà `point_count` della funzionalità punto, come illustrato di seguito. 
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
> [Creare un'origine dati](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)