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
ms.openlocfilehash: 957ce60b8519ccb1e3287232f7a5459a56b25bb7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960616"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di punti di grandi dimensioni.  Ad esempio, il rendering di decine di migliaia di punti all'interno della mappa, visualizzati come simboli, copre la maggior parte dell'area di mappatura e apparirebbe sotto forma di tanti simboli coperti gli uni dagli altri; ottenere informazioni dettagliate all'interno dei dati risulterebbe quindi più difficile. Tuttavia, visualizzare questo stesso set di dati come una mappa termica semplifica la visualizzzione dei punti in cui i dati sono più densi sistemi e la densità relativa ad altre aree. Esistono molti scenari in cui le mappe termiche vengono usate. Di seguito sono riportati alcuni esempi:

* I dati sulla temperatura vengono visualizzati in genere come mappa termica in quanto fornisce approssimazioni per quanto riguarda la temperatura tra due punti di dati.
* Il rendering dei dati per i sensori di rumore come mappa termica non solo mostra l'intensità del rumore nel punto in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in un sito potrebbe non essere elevato, ma se l'area di copertura acustica di più sensori si sovrappone, è possibile che quest'area di sovrapposizione abbia livelli di rumore più elevati e quindi, sia visibile nella mappa termica.
* La visualizzazione di una traccia GPS che include la velocità sotto forma di mappa di altezza ponderata, in cui l'intensità di ogni punto dati è basata sulla velocità, è un ottimo modo per verificare rapidamente il punto di accelerazione del veicolo.

> [!TIP]
> Per impostazione predefinita, i livelli della mappa termica eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello e poter eseguire il rendering solo delle funzionalità della geometria dei punti, impostare la proprietà `filter` del livello su `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come una mappa termica, è sufficiente trasferire l'origine dati in un'istanza della classe `HeatMapLayer` e aggiungerla alla mappa come mostrato di seguito.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

In questo esempio, ogni punto di calore ha un raggio di 10 pixel in tutti i livelli di zoom. Quando si aggiunge il livello mappa termica alla mappa, questo esempio lo inserisce sotto il livello dell'etichetta. In questo modo viene creata una migliore esperienza utente, in quanto le etichette sono chiaramente visibili al di sopra della mappa termica. I dati di questo esempio provengono dal programma [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) e sono costituiti da punti che rappresentano i terremoti significativi avvenuti negli ultimi 30 giorni.

## <a name="customizing-the-heat-map-layer"></a>Personalizzazione del livello per le mappe termiche

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello per le mapphe termiche offre diverse opzioni per la personalizzazione;

* `radius`: Definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. Il raggio può essere impostato come un numero fisso o un'espressione. Mediante l'uso di un'espressione è possibile ridimensionare il raggio in base al livello di zoom che viene visualizzato tramite la rappresentazione di un'area spaziale coerente sulla mappa (ad esempio, un raggio di 8 km circa).
* `color`: Specifica come viene colorata la mappa termica. Una tavolozza dei colori gradiente è spesso usata per le mappe termiche, ma le tavolozze dei colori a più livelli sono utili anche se si desidera rendere la mappa termica più simile ai dati di contorno. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. I valori di colore per le mappe termiche sono specificati come espressione sul valore `heatmap-density`. Il colore all'indice 0 in un'espressione con gradiente o il colore predefinito di un colore graduale definisce il colore dell'area dove non sono presenti dati e che può essere usata per definire il colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. Ecco alcuni esempi di espressioni di colore;

| Espressione colore gradiente | Espressione colore graduale | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: Specifica quanto opaco o trasparente è il livello della mappa termica.
* `intensity`: Applica un moltiplicatore al peso di ciascun punto dati per aumentare l'intensità complessiva della mappa termica. In questo modo le piccole differenze di peso dei punti dati diventano più facili da visualizzare.
* `weight`: Per impostazione predefinita, tutti i punti dati hanno un peso pari a 1, pertanto tutti i punti dati vengono ponderati in modo equivalente. L'opzione peso funge da moltiplicatore e può essere impostata come numero o espressione. Se un numero è impostato come peso, ad esempio 2, equivarrebbe a collocare due volte ogni punto dati sulla mappa, raddoppiando così la densità. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. Tuttavia, se viene usata un'espressione, il peso di ogni punto dati può basarsi su alcune metriche nelle proprietà di ogni punto dati. Per esempio, nei dati sui terremoti, ogni punto dati rappresenta un terremoto. Una metrica importante di ogni punto dati di un terremoto è il valore della magnitudo. I terremoti si verificano continuamente, ma la maggior parte di essi hanno una bassa magnitudo e non vengono nemmeno percepiti. L'uso del valore della magnitudo in un'espressione, per assegnare il peso a ogni punto dati, consentirà una rappresentazione più fedele i terremoti più significativi all'interno della mappa termica.
* Oltre alle opzioni del livello base (zoom minimo/massimo, visualizzazione e filtro), esiste anche l'opzione `source`, se si vuole aggiornare l'origine dati, e l'opzione `source-layer`, se l'origine dati è un'origine della mappa vettoriale.

Ecco uno strumento per testare le diverse opzioni del livello per le mappe termiche.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Abilitando il clustering sull'origine dati, i punti che sono vicini l'uno all'altro sono raggruppati insieme come punto in cluster. Il numero di punti di ogni cluster può essere utilizzato come espressione del peso per la mappa termica e riduce significativamente il numero di punti sui cui eseguire il rendering. Il numero di punti di un cluster è memorizzato in una `point_count property` della funzione punto, come mostrato di seguito. 
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

