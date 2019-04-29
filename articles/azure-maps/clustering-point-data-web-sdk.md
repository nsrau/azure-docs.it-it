---
title: Clustering di dati di punti nelle mappe di Azure | Microsoft Docs
description: Come punto il cluster in Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60795940"
---
# <a name="clustering-point-data"></a>Clustering dei punti dati

Quando la visualizzazione numero di punti dati sulla mappa, i punti di sovrappongano tra loro, mappa Cerca risulti troppo affollata e diventa difficile visualizzare e usare. Clustering dei punti dati può essere usato per migliorare questa esperienza utente. Il clustering di punto dati è il processo di combinazione di punto dati vicini tra loro e che li rappresenta sulla mappa come un singolo cluster punto dati. Quando l'utente esegue lo zoom nella mappa, i cluster di suddividono nei singoli punti dati.

## <a name="enabling-clustering-on-a-data-source"></a>Abilitazione del clustering in un'origine dati

Clustering può facilmente essere abilitato per il `DataSource` classe impostando il `cluster` opzione su true. Inoltre, il raggio di pixel da selezionare nelle vicinanze punti da combinare in un cluster può essere impostato utilizzando il `clusterRadius` e un livello di zoom può essere specificato in corrispondenza del quale disabilitare il clustering per la logica usando il `clusterMaxZoom` opzione. Di seguito è riportato un esempio relativo all'abilitazione del clustering in un'origine dati.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Se i due punti dati sono vicine a terra, è possibile che il cluster non verrà mai suddividere, indipendentemente dal modo in cui Chiudi l'utente esegue lo zoom avanti. Per risolvere questo problema, è possibile impostare il `clusterMaxZoom` opzione dell'origine dati che specifica il livello di zoom per disabilitare il clustering per la logica e semplicemente visualizzare tutti gli elementi.

Il `DataSource` classe dispone inoltre dei metodi seguenti relativi al clustering:

| Metodo | Tipo restituito | DESCRIZIONE |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Oggetto Promise&lt;caratteristica&lt;Geometry, qualsiasi&gt; \| forma&gt; | Recupera gli elementi figlio del cluster specificato a livello di zoom avanti. Questi elementi figlio possono essere una combinazione di forme e subclusters. Il subclusters sarà funzionalità con proprietà corrispondenti ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Oggetto Promise&lt;numero&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster verrà avviare espandendo o suddividere. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Oggetto Promise&lt;caratteristica&lt;Geometry, qualsiasi&gt; \| forma&gt; | Recupera tutti i punti in un cluster. Impostare il `limit` per restituire un sottoinsieme dei punti, usare il `offset` possibile spostarsi tra i punti. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visualizzare i cluster con un livello a bolle

Un livello a bolle è un ottimo modo per eseguire il rendering di punti di cluster come si può facilmente aumentare il raggio e modificare il colore che disporle in base al numero di punti nel cluster usando un'espressione. Quando si visualizzano i cluster tramite un livello a bolle, è anche consigliabile usare un livello separato per il rendering dei punti dati non in cluster. Spesso è utile anche essere in grado di visualizzare le dimensioni del cluster all'inizio le bolle. Un livello di simboli con testo e l'icona non è utilizzabile per ottenere questo comportamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello a bolle di base di clustering" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering livello a bolle di base</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visualizzare i cluster con un livello di simboli

Quando i dati del punto usando il livello di simboli, per impostazione predefinita che nasconde automaticamente la visualizzazione di simboli che si sovrappongono tra loro per creare un'esperienza più chiara, tuttavia questo potrebbe non essere l'esperienza desiderata se si desidera visualizzare la densità di dati punta sulla mappa. Impostando il `allowOverlap` opzione dei livelli di simbolo `iconOptions` proprietà `true` disabilita questa esperienza, ma comporterà tutti i simboli visualizzati. Utilizzo del clustering consente di visualizzare la densità di tutti i dati durante la creazione di un'esperienza utente pulita nice. In questo esempio, simboli personalizzati da utilizzare per rappresentare i cluster e i singoli punti dati.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello di simbolo del cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>livello cluster simbolo</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e i punti più caldi esegue il mapping di livello

Mappe termiche sono un ottimo modo per visualizzare la densità dei dati sulla mappa. Questa visualizzazione può gestire un numero elevato di punti dati di per sé, ma può gestire ulteriormente i dati se i punti dati sono raggruppati e le dimensioni del cluster viene usata come il peso della mappa termica. Impostare il `weight` opzione del livello mappa termica per `['get', 'point_count']` per ottenere questo risultato. Se il raggio di cluster è ridotto, la mappa termica avrà un aspetto pressoché identica a una mappa termica usando i punti dati non in cluster ma offre prestazioni notevolmente migliori. Tuttavia, minore è il raggio di cluster, il più accurato la mappa termica verrà ma con meno di un'aggregazione traggono vantaggio.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica ponderata del cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>Cluster ponderati mappa termica</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventi del mouse sui punti dati in cluster

Quando si verificano gli eventi del mouse su un livello che contengono punti dati in cluster, il punto dati del cluster verrà restituito all'evento come un oggetto funzione di punto GeoJSON. Questa funzionalità punto sarà disponibili le proprietà seguenti:

| Nome proprietà | Type | DESCRIZIONE |
|---------------|------|-------------|
| cluster | boolean | Indica se funzionalità rappresenta un cluster. |
| cluster_id | string | Un ID univoco per il cluster che può essere usato con l'origine dati `getClusterExpansionZoom`, `getClusterChildren`, e `getClusterLeaves` metodi. |
| point_count | number | Il numero di punti contenuti nel cluster. |
| point_count_abbreviated | string | Stringa che abbrevia il valore point_count se è long. (ad esempio, 4.000 diventa 4K) |

L'esempio seguente accetta un livello bolle che esegue il rendering dei punti di cluster e aggiunge un evento click che quando viene attivato, calcolare, e lo zoom sulla mappa per il livello di zoom avanti in corrispondenza del quale il cluster verrà interrotta distanti tra loro usando il `getClusterExpansionZoom` metodo del `DataSource` classe e il `cluster_id` proprietà dell'oggetto selezionato in cluster punto dati. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/moZWeV/'>Cluster getClusterExpansionZoom</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visualizzare l'area del cluster 

I dati di punti che rappresenta un cluster viene distribuiti su un'area. In questo esempio quando il mouse su un cluster, i dati di singoli punti che contiene (foglie) verranno usati per calcolare una struttura convessa e visualizzati sulla mappa per visualizzare l'area. Tutti i punti contenuti in un cluster possono essere recuperati dall'origine dati utilizzando il `getClusterLeaves` (metodo). Una struttura convessa è un poligono che esegue il wrapping di un set di punti, ad esempio una banda di database elastici e può essere calcolato usando la `atlas.math.getConvexHull` (metodo).

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Struttura convessa di area del cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>struttura convessa di area del Cluster</a> dalle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Oggetto di DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [spazio dei nomi Atlas.Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
