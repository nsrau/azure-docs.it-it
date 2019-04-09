---
title: Aggiungere un livello per le mappe termiche a Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello per le mappe termiche alla mappa Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 93dae9a69997dd1b513d205118a112119025f4fd
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057116"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.  Ad esempio, il rendering di decine di migliaia di punti all'interno della mappa, visualizzati come simboli, copre la maggior parte dell'area di mappatura e apparirebbe sotto forma di tanti simboli coperti gli uni dagli altri; ottenere informazioni dettagliate all'interno dei dati risulterebbe quindi più difficile. Tuttavia, visualizzare questo stesso set di dati come una mappa termica semplifica la visualizzzione dei punti in cui i dati sono più densi sistemi e la densità relativa ad altre aree. Esistono molti scenari in cui le mappe termiche vengono usate. Di seguito sono riportati alcuni esempi:

* I dati sulla temperatura vengono visualizzati in genere come mappa termica in quanto fornisce approssimazioni per quanto riguarda la temperatura tra due punti di dati.
* Il rendering dei dati per i sensori di rumore come mappa termica non solo mostra l'intensità del rumore nel punto in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in un sito potrebbe non essere elevato, ma se l'area di copertura acustica di più sensori si sovrappone, è possibile che quest'area di sovrapposizione abbia livelli di rumore più elevati e quindi, sia visibile nella mappa termica.
* La visualizzazione di una traccia GPS che include la velocità sotto forma di mappa di altezza ponderata, in cui l'intensità di ogni punto dati è basata sulla velocità, è un ottimo modo per verificare rapidamente il punto di accelerazione del veicolo.

> [!TIP]
> Per impostazione predefinita, i livelli della mappa termica eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo che solo il rendering punto funzionalità dell'oggetto geometry, impostato il `filter` proprietà del livello `['==', ['geometry-type'], 'Point']` o `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se si desidera includere anche le funzionalità MultiPoint.

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come una mappa termica, è sufficiente trasferire l'origine dati in un'istanza della classe `HeatMapLayer` e aggiungerla alla mappa come mostrato di seguito.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

In questo esempio, ogni punto di calore ha un raggio di 10 pixel in tutti i livelli di zoom. Quando si aggiunge il livello mappa termica con interruzioni alla mappa, questo esempio vengono inserite sotto il livello dell'etichetta per creare un'esperienza utente migliore come le etichette sono chiaramente visibili di sopra della mappa termica. I dati di questo esempio provengono dal programma [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) e sono costituiti da punti che rappresentano i terremoti significativi avvenuti negli ultimi 30 giorni.

## <a name="customizing-the-heat-map-layer"></a>Personalizzazione del livello per le mappe termiche

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello per le mapphe termiche offre diverse opzioni per la personalizzazione;

* `radius`: Definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. Il raggio può essere impostato come un numero fisso o un'espressione. Utilizza un'espressione, è possibile ridimensionare il raggio in base al livello di zoom, che viene visualizzato per rappresentare un'area coerente spaziale sulla mappa (ad esempio, 5 miglia radius).
* `color`: Specifica come viene colorata la mappa termica. Una tavolozza dei colori della sfumatura utilizzata spesso per mappe termiche ed è possibile ottenere con un'espressione di interpolazione, ma con rientri colore tavolozze sono utili se si desidera rendere i punti più caldi eseguire il mapping anche più simile dati contour e può essere ottenuta con un'espressione di passaggio. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. I valori di colore per le mappe termiche sono specificati come espressione sul valore `heatmap-density`. Il colore in corrispondenza dell'indice 0 in un'espressione di interpolazione o il colore predefinito di un'espressione di passaggio, definisce il colore dell'area in cui è presente alcun dato e può essere usato per definire un colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. Ecco alcuni esempi di espressioni di colore;

| Espressione colore interpolazione | Espressione colore graduale | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

* `opacity`: Specifica quanto opaco o trasparente è il livello della mappa termica.
* `intensity`: Si applica un moltiplicatore per il peso di ogni punto dati per aumentare l'intensità complessivo della mappa termica e contribuisce a rendere le piccole differenze nel peso dei punti dati diventano più facili da visualizzare.
* `weight`: Per impostazione predefinita, tutti i punti dati hanno un peso pari a 1, pertanto tutti i punti dati vengono ponderati in modo equivalente. L'opzione peso funge da moltiplicatore e può essere impostata come numero o espressione. Se un numero è impostato come peso, ad esempio 2, equivarrebbe a collocare due volte ogni punto dati sulla mappa, raddoppiando così la densità. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. Tuttavia, se viene usata un'espressione, il peso di ogni punto dati può basarsi su alcune metriche nelle proprietà di ogni punto dati. Per esempio, nei dati sui terremoti, ogni punto dati rappresenta un terremoto. Una metrica importante di ogni punto dati di un terremoto è il valore della magnitudo. I terremoti si verificano continuamente, ma la maggior parte di essi hanno una bassa magnitudo e non vengono nemmeno percepiti. L'uso del valore della magnitudo in un'espressione, per assegnare il peso a ogni punto dati, consentirà una rappresentazione più fedele i terremoti più significativi all'interno della mappa termica.
* Oltre alle opzioni del livello base (zoom minimo/massimo, visualizzazione e filtro), esiste anche l'opzione `source`, se si vuole aggiornare l'origine dati, e l'opzione `source-layer`, se l'origine dati è un'origine della mappa vettoriale.

Ecco uno strumento per testare le diverse opzioni del livello per le mappe termiche.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mappa termica Ingrandibile coerente

Per impostazione predefinita, il raggio dei punti dati viene eseguito il rendering nel livello mappa termica hanno un raggio fisso in pixel per tutti i livelli di zoom. Come la mappa viene ingrandita o ridotta di aggregazioni di dati contemporaneamente e il livello mappa termica con interruzioni ha un aspetto diverso. Oggetto `zoom` espressione può essere utilizzata per ridimensionare il raggio per ogni livello di zoom in modo che ogni punto dati copre la stessa area della mappa fisica. In questo modo il livello mappa termica aspetto più statico e coerente. Ogni livello di zoom della mappa ha due volte come numero di pixel verticalmente e orizzontalmente come livello di zoom precedente, di conseguenza, il raggio la scalabilità in modo che raddoppia a ogni livello di zoom verrà creata una mappa termica che un aspetto coerenza in tutti i livelli di zoom. Questa operazione può essere eseguita utilizzando il `zoom` con una base 2 `exponential interpolation` espressione, come illustrato nell'esempio seguente. Eseguire lo zoom avanti sulla mappa per vedere come il raggio dei dati fa riferimento nella scala della mappa termica con il livello di zoom.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica Ingrandibile coerente" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mappa termica Ingrandibile coerente</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Abilitando il clustering sull'origine dati, i punti che sono vicini l'uno all'altro sono raggruppati insieme come punto in cluster. Il numero di punti di ogni cluster può essere utilizzato come espressione del peso per la mappa termica e riduce significativamente il numero di punti sui cui eseguire il rendering. Il numero di punti di un cluster verrà archiviato in un `point_count` proprietà della funzionalità punto come illustrato di seguito. 
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
> [Aggiungere un livello per i simboli](./map-add-pin.md)

