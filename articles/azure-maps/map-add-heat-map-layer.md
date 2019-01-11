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
ms.openlocfilehash: cbf134640f981056c0996ffc6768ebc1381ce2ac
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995151"
---
# <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Le mappe termiche, note anche come mappe di densità dei punti, sono un tipo di visualizzazione dei dati utilizzate per rappresentare la densità dei dati tramite una gamma di colori. Vengono spesso usate per visualizzare le "aree sensibili" dei dati su una mappa e sono un ottimo modo per eseguire il rendering di set di dati di grandi dimensioni.  Ad esempio, il rendering di decine di migliaia di punti all'interno della mappa visualizzati come simboli coprono la maggior parte dell'area della mappa e apparirebbero come tanti simboli coperti gli uni dagli altri, rendendo difficile ottenere informazioni dettagliate dei dati. Tuttavia, visualizzare questo stesso set di dati come una mappa termica semplifica la visualizzzione dei punti in cui i dati sono più densi sistemi e la densità relativa ad altre aree. Esistono molti scenari in cui le mappe termiche vengono usate. Di seguito sono riportati alcuni esempi:

* I dati sulla temperatura vengono visualizzati in genere come mappa termica in quanto fornisce approssimazioni per quanto riguarda la temperatura tra due punti di dati.
* Il rendering dei dati per i sensori di rumore come mappa termica non solo mostra l'intensità del rumore nel punto in cui si trova il sensore, ma può anche fornire informazioni sulla dissipazione su una distanza. Il livello di rumore in un sito può non essere elevato, ma se l'area di copertura acustica di più sensori si sovrappone, è possibile che quest'area di sovrapposizione abbia livelli di rumore più elevati, e quindi sia visibile nella mappa termica.
* La visualizzazione di una traccia GPS che include la velocità come una mappa dell'altezza del peso in cui l'intensità di ogni punto di dati è basata sulla velocità è un ottimo modo per verificare rapidamente il punto di accelerazione del veicolo.

> [!TIP]
> Per impostazione predefinita i livelli bolle eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello e poter eseguire il rendering solo delle funzionalità della geometria dei punti, impostare la proprietà `filter` del livello su `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Aggiungere un livello per le mappe termiche

Per eseguire il rendering di un'origine dati di punti come mappa termica è sufficiente passare l'origine dati in un'istanza della classe HeatMapLayer e aggiungerla alla mappa come mostrato qui.

<br/>

<iframe height='500' scrolling='no' title='Livello semplice per le mappe termiche' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Livello semplice per le mappe termiche</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

In questo esempio, ogni punto di calore ha un raggio di 10 pixel in tutti i livelli di zoom. Quando si aggiunge il livello mappa termica alla mappa, questo esempio lo inserisce sotto il livello dell'etichetta. In questo modo viene creata una migliore esperienza utente, in quanto le etichette sono chiaramente visibili al di sopra della mappa termica. I dati di questo esempio provengono dal programma [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) e sono costituiti da punti che rappresentano i terremoti significativi avvenuti negli ultimi 30 giorni.

## <a name="customizing-the-heat-map-layer"></a>Personalizzazione del livello per le mappe termiche

Nell'esempio precedente viene personalizzata la mappa termica impostando le opzioni di radius e opacità. Il livello per le mapphe termiche offre diverse opzioni per la personalizzazione;

* `radius`: Definisce un raggio di pixel in cui eseguire il rendering di ogni punto dati. Il raggio può essere impostato come un numero fisso o un'espressione. Impostando il raggio come espressione che imposta il raggio in base al livello di zoom si possono ottenere mappe di calore che sembrano avere un raggio che rappresenta un'area spaziale coerente sulla mappa.
* `color`: Specifica come viene colorata la mappa termica. Una tavolozza dei colori gradiente è spesso usata per le mappe termiche, ma le tavolozze dei colori a più livelli sono utili anche se si desidera rendere la mappa termica più simile ai dati di contorno. Queste tavolozze di colori definiscono i colori dal valore di densità minima a quello massimo. I valori di colore per le mappe termiche sono specificati come espressione sul valore `heatmap-density`. Il colore all'indice 0 in un'espressione gradiente o il colore predefinito di un colore graduale definisce il colore dell'area dove non ci sono dati, o il colore di sfondo. Molti preferiscono impostare questo valore su un nero trasparente o semitrasparente. Ecco alcuni esempi di espressioni di colore;

| Espressione colore gradiente | Espressione colore graduale | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolate',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['linear'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'purple',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'step',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\['heatmap-density'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'navy',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00, 'red'<br/>\] |   

* `opacity`: Specifica quanto opaco o trasparente è il livello della mappa termica.
* `intensity`: Applica un moltiplicatore al peso di ciascun punto dati per aumentare l'intensità complessiva della mappa termica. In questo modo le piccole differenze di peso dei punti dati diventano più facili da visualizzare.
* `weight`: Per impostazione predefinita, tutti i punti dati hanno un peso pari a 1, pertanto tutti i punti dati vengono ponderati in modo equivalente. L'opzione peso funge da moltiplicatore e può essere impostata come numero o espressione. Se un numero è impostato come numero, ad esempio 2, equivarrebbe a mettere due volte ogni punto dati sulla mappa, raddoppiando così la densità. Impostando l'opzione peso su un numero, la mappa termica viene renderizzata in modo simile all'utilizzo dell'opzione intensità. Tuttavia, se viene utilizzata un'espressione, il peso di ogni punto dati può essere basato su diverse e basato su alcune metriche nelle proprietà del punto. Prendendo i dati dei terremoti come esempio, ogni punto dati rappresenta un terremoto e una metrica importante per ogni terremoto è la sua magnitudo. I terremoti si verificano continuamente, ma la maggior parte di essi hanno una bassa magnitudo e non vengono nemmeno percepiti. Utilizzando il valore della magnitudo in un'espressione per assegnare l'opzione peso consentirà di rappresentare meglio i terremoti più significativi all'interno della mappa termica.
* Oltre alle opzioni del livello base; zoom min/max, visibile e filtro, esiste anche l'opzione `source` se si desidera aggiornare l'origine dati e l'opzione `source-layer` se l'origine dati è una fonte vettoriale.

Ecco uno strumento per testare le diverse opzioni del livello per le mappe termiche.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello per le mappe termiche' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opzioni del livello per le mappe termiche</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Abilitando il clustering sull'origine dati, i punti che sono vicini l'uno all'altro sono raggruppati insieme come punto in cluster. Il numero di punti di ogni cluster può essere utilizzato come espressione del peso per la mappa termica e riduce significativamente il numero di punti sui cui eseguire il rendering. Il numero di punti di un cluster è memorizzato in una proprietà point_count della funzione punto come mostrato di seguito. 
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

