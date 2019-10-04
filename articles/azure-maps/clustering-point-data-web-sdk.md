---
title: Dati dei punti di clustering nelle mappe di Azure | Microsoft Docs
description: Come raggruppare i dati dei punti in Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 5f51c1166364a3470a1cc943e66d429c32cdc49b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839487"
---
# <a name="clustering-point-data"></a>Dati punto di clustering

Quando si visualizzano molti punti dati sulla mappa, i punti si sovrappongono, la mappa sembra disordinata e diventa difficile da visualizzare e usare. Il clustering dei dati punto può essere usato per migliorare questa esperienza utente. I dati del punto di clustering sono il processo di combinazione di dati punto vicini tra loro e che li rappresentano sulla mappa come singolo punto dati del cluster. Quando l'utente esegue lo zoom avanti sulla mappa, i cluster si suddividono nei singoli punti dati.

## <a name="enabling-clustering-on-a-data-source"></a>Abilitazione del clustering in un'origine dati

È possibile abilitare facilmente il clustering sulla `DataSource` classe impostando l' `cluster` opzione su true. Inoltre, il raggio pixel per selezionare i punti vicini da combinare in un cluster può essere impostato utilizzando `clusterRadius` e è possibile specificare un livello di zoom in cui disabilitare la logica di clustering utilizzando `clusterMaxZoom` l'opzione. Di seguito è riportato un esempio di come abilitare il clustering in un'origine dati.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Se due punti dati sono vicini, è possibile che il cluster non si interrompa, indipendentemente dalla distanza con cui l'utente esegue lo zoom. Per risolvere questo problema, è possibile impostare `clusterMaxZoom` l'opzione dell'origine dati che specifica a livello di zoom per disabilitare la logica di clustering e visualizzare semplicemente tutti gli elementi.

La `DataSource` classe dispone inoltre dei seguenti metodi correlati al clustering:

| Metodo | Tipo restituito | DESCRIZIONE |
|--------|-------------|-------------|
| getClusterChildren (clusterId: numero) | Geometria&lt;della&lt;funzionalità&gt; della matrice Promise, qualsiasi \|forma &lt;&gt;&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| getClusterExpansionZoom (clusterId: numero) | Numero&lt;promessa&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà ad espandersi o suddividere. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Geometria&lt;della&lt;funzionalità&gt; della matrice Promise, qualsiasi \|forma &lt;&gt;&gt; | Recupera tutti i punti in un cluster. Impostare l' `limit` oggetto per restituire un subset dei punti e utilizzare la `offset` pagina per scorrere i punti. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visualizzare i cluster con un livello Bubble

Un livello Bubble è un ottimo modo per eseguire il rendering dei punti del cluster, in quanto è possibile ridimensionare facilmente il raggio e modificare il colore in base al numero di punti nel cluster usando un'espressione. Quando si visualizzano i cluster usando un livello a bolle, è necessario usare anche un livello separato per il rendering dei punti dati non cluster. Spesso è anche bello poter visualizzare le dimensioni del cluster sopra le bolle. Per ottenere questo comportamento, è possibile usare un livello di simbolo con testo e nessuna icona. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering a livello Bubble di base" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la pagina relativa al clustering a <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>bolle Basic</a> a penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visualizzare i cluster usando un livello di simbolo

Per impostazione predefinita, quando si visualizzano i dati del punto usando il livello dei simboli, i simboli che si sovrappongono per la creazione di un'esperienza di pulitura verranno nascosti automaticamente, ma potrebbe non essere l'esperienza desiderata se si desidera visualizzare la densità dei punti dati sulla mappa. L'impostazione dell' `iconOptions` `true` opzione della proprietà livelli simboli su Disabilita questa esperienza, ma comporterà la visualizzazione di tutti i simboli. `allowOverlap` L'uso del clustering consente di visualizzare la densità di tutti i dati durante la creazione di una buona esperienza utente pulita. In questo esempio, i simboli personalizzati verranno usati per rappresentare i cluster e i singoli punti dati.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simboli cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Per informazioni su <a href='https://codepen.io'>CodePen</a>, vedere il <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>livello di simbolo</a> del<a href='https://codepen.io/azuremaps'>@azuremaps</a>cluster penna di Azure Maps ().
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e il livello di mappa termica

Le mappe termiche sono un ottimo modo per visualizzare la densità dei dati sulla mappa. Questa visualizzazione è in grado di gestire un numero elevato di punti dati, ma può gestire ancora più dati se i punti dati sono in cluster e le dimensioni del cluster vengono usate come peso della mappa termica. Impostare l' `weight` opzione del livello mappa termica su `['get', 'point_count']` per ottenere questo risultato. Quando il raggio del cluster è ridotto, la mappa termica sarà quasi identica a una mappa termica che usa i punti dati non cluster, ma eseguirà molto meglio. Tuttavia, minore è il raggio del cluster, più accurato sarà la mappa termica, ma con un minor vantaggio in merito alle prestazioni.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica ponderata del cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mappa termica ponderata del cluster</a> di penne da<a href='https://codepen.io/azuremaps'>@azuremaps</a>mappe di Azure () su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventi del mouse sui punti dati del cluster

Quando si verificano eventi del mouse su un livello che contiene punti dati cluster, il punto dati del cluster verrà restituito all'evento come oggetto funzionalità punto GeoJSON. Questa funzionalità del punto avrà le proprietà seguenti:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| cluster | boolean | Indica se la funzionalità rappresenta un cluster. |
| cluster_id | string | ID univoco per il cluster che può essere utilizzato con i metodi, `getClusterExpansionZoom`e `getClusterChildren` `getClusterLeaves` dell'origine dati. |
| point_count | number | Numero di punti contenuti nel cluster. |
| point_count_abbreviated | string | Stringa che abbrevia il `point_count` valore se è lungo. (ad esempio, 4.000 diventa 4K) |

Questo esempio usa un livello Bubble che esegue il rendering dei punti `getClusterExpansionZoom` `DataSource` del cluster e aggiunge un evento click che, quando viene attivato, calcola e zoom la mappa al livello di zoom successivo in corrispondenza del quale il cluster si suddividerà usando il metodo della classe e `cluster_id` proprietà del punto dati del cluster selezionato. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/moZWeV/'>cluster Pen getClusterExpansionZoom</a> da mappe di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visualizza area cluster 

I dati del punto rappresentati da un cluster vengono distribuiti in un'area. In questo esempio quando si passa il mouse su un cluster, i singoli punti dati in esso contenuti (foglie) verranno utilizzati per calcolare una struttura convessa e visualizzata sulla mappa per visualizzare l'area. Tutti i punti contenuti in un cluster possono essere recuperati dall'origine dati usando `getClusterLeaves` il metodo. Una struttura convessa è un poligono che esegue il wrapping di un set di punti come una banda elastica e può `atlas.math.getConvexHull` essere calcolato usando il metodo.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Guscio convesso area cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l'area del cluster di penna <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convessa Hull</a> di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregazione dei dati nei cluster

Spesso i cluster sono rappresentati usando un simbolo con il numero di punti all'interno del cluster, tuttavia talvolta è preferibile personalizzare ulteriormente lo stile dei cluster in base a una metrica, ad esempio il ricavo totale di tutti i punti all'interno di un cluster. Con le aggregazioni di cluster è possibile creare e popolare proprietà personalizzate utilizzando un calcolo di [espressione](data-driven-style-expressions-web-sdk.md#aggregate-expression) di aggregazione.  `clusterProperties` Le`DataSource`aggregazioni del cluster possono essere definite in un'opzione di.

Nell'esempio seguente viene utilizzata un'espressione di aggregazione per calcolare un conteggio in base alla proprietà del tipo di entità di ogni punto dati in un cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregazioni cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le aggregazioni del <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>cluster</a> Pen di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Oggetto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [spazio dei nomi Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
