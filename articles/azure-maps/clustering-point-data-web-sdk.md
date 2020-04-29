---
title: Dati del punto di clustering su una mappa | Mappe Microsoft Azure
description: In questo articolo si apprenderà come raggruppare i dati dei punti e come eseguirne il rendering in una mappa usando il Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804572"
---
# <a name="clustering-point-data"></a>Dati punto di clustering

Quando si visualizzano molti punti dati sulla mappa, i punti dati possono sovrapporsi tra loro. La sovrapposizione può causare che la mappa diventi illeggibile e difficile da utilizzare. Il clustering dei dati dei punti è la combinazione di dati dei punti vicini tra loro e rappresentati sulla mappa come singolo punto dati in cluster. Quando l'utente ingrandisce la mappa, i cluster si scompongono nei singoli punti dati. Quando si lavora con un numero elevato di punti dati, utilizzare i processi di clustering per migliorare l'esperienza utente.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Abilitazione del clustering in un'origine dati

Abilitare il clustering nella `DataSource` classe impostando l' `cluster` opzione su true. Impostare `ClusterRadius` per selezionare i punti vicini e combinarli in un cluster. Il valore di `ClusterRadius` è in pixel. Consente `clusterMaxZoom` di specificare un livello di zoom in cui disabilitare la logica di clustering. Di seguito è riportato un esempio di come abilitare il clustering in un'origine dati.

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
> Se due punti dati sono vicini, è possibile che il cluster non si interrompa, indipendentemente dalla distanza con cui l'utente esegue lo zoom. Per risolvere questo problema, è possibile impostare `clusterMaxZoom` l'opzione per disabilitare la logica di clustering e visualizzare semplicemente tutti gli elementi.

Di seguito sono riportati i metodi `DataSource` aggiuntivi offerti dalla classe per il clustering:

| Metodo | Tipo restituito | Descrizione |
|--------|-------------|-------------|
| getClusterChildren (clusterId: numero) | Geometria&lt;della&lt;funzionalità&lt;della matrice Promise, qualsiasi&gt; \| forma&gt;&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| getClusterExpansionZoom (clusterId: numero) | Promise&lt;number&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà a espandersi o scomporsi. |
| getClusterLeaves (clusterId: numero, limite: numero, offset: numero) | Geometria&lt;della&lt;funzionalità&lt;della matrice Promise, qualsiasi&gt; \| forma&gt;&gt; | Recupera tutti i punti in un cluster. Impostare `limit` per restituire un subset dei punti e usare `offset` per spostarsi attraverso i punti. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visualizzare i cluster con un livello Bubble

Un livello Bubble è un ottimo modo per eseguire il rendering di punti cluster. Usare le espressioni per ridimensionare il raggio e modificare il colore in base al numero di punti nel cluster. Se si visualizzano i cluster usando un livello a bolle, è necessario usare un livello separato per eseguire il rendering dei punti dati non cluster.

Per visualizzare le dimensioni del cluster nella parte superiore della bolla, usare un livello di simbolo con testo e non usare un'icona.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering a livello Bubble di base" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la pagina relativa al <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering a bolle Basic</a> a penna<a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visualizzare i cluster usando un livello di simbolo

Quando si visualizzano i punti dati, il livello dei simboli nasconde automaticamente i simboli che si sovrappongono per garantire un'interfaccia utente più pulita. Questo comportamento predefinito potrebbe essere indesiderato se si desidera visualizzare la densità dei punti dati sulla mappa. Tuttavia, queste impostazioni possono essere modificate. Per visualizzare tutti i simboli, impostare `allowOverlap` l'opzione della proprietà livelli `iconOptions` simboli su `true`. 

Usare il clustering per mostrare la densità dei punti dati mantenendo un'interfaccia utente pulita. Nell'esempio seguente viene illustrato come aggiungere simboli personalizzati e rappresentare i cluster e i singoli punti dati utilizzando il livello dei simboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simboli cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Per informazioni su <a href='https://codepen.io'>CodePen</a>, vedere il <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>livello di simbolo</a> del<a href='https://codepen.io/azuremaps'>@azuremaps</a>cluster penna di Azure Maps ().
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e il livello di mappa termica

Le mappe termiche sono un ottimo modo per visualizzare la densità dei dati sulla mappa. Questo metodo di visualizzazione può gestire un numero elevato di punti dati autonomamente. Se i punti dati sono in cluster e le dimensioni del cluster vengono usate come peso della mappa termica, la mappa termica può gestire ancora più dati. Per ottenere questa opzione, impostare l' `weight` opzione del livello mappa termica su `['get', 'point_count']`. Quando il raggio del cluster è ridotto, la mappa termica sarà quasi identica a una mappa termica che usa i punti dati non in cluster, ma sarà molto migliore. Tuttavia, minore è il raggio del cluster, più accurato sarà la mappa termica, ma con un minor numero di vantaggi in merito alle prestazioni.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica ponderata del cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mappa termica ponderata del cluster</a> di penne da<a href='https://codepen.io/azuremaps'>@azuremaps</a>mappe di Azure () su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventi del mouse sui punti dati del cluster

Quando si verificano eventi del mouse su un livello che contiene punti dati del cluster, il punto dati del cluster torna all'evento come oggetto funzionalità punto GeoJSON. Questa funzionalità del punto avrà le proprietà seguenti:

| Nome proprietà             | Type    | Descrizione   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se la funzionalità rappresenta un cluster. |
| `cluster_id`              | stringa  | ID univoco per il cluster che può essere usato con i metodi `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves` della classe DataSource. |
| `point_count`             | d'acquisto  | Numero di punti contenuti nel cluster.  |
| `point_count_abbreviated` | stringa  | Stringa che abbrevia il `point_count` valore se è lungo. Ad esempio, 4.000 diventa 4K.  |

Questo esempio utilizza un livello Bubble che esegue il rendering di punti del cluster e aggiunge un evento click. Quando si attiva l'evento click, il codice calcola e zoom la mappa fino al livello di zoom successivo, in corrispondenza del quale il cluster si interrompe. Questa funzionalità viene implementata utilizzando `getClusterExpansionZoom` il metodo della `DataSource` classe e la `cluster_id` proprietà del punto dati del cluster selezionato.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/moZWeV/'>cluster Pen getClusterExpansionZoom</a> da mappe di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visualizza area cluster 

I dati del punto rappresentati da un cluster vengono distribuiti in un'area. In questo esempio quando si passa il mouse su un cluster, si verificano due comportamenti principali. Per prima cosa, i singoli punti dati contenuti nel cluster verranno usati per calcolare una struttura convessa. Quindi, la struttura convessa verrà visualizzata sulla mappa per visualizzare un'area.  Una struttura convessa è un poligono che esegue il wrapping di un set di punti come una banda elastica e può `atlas.math.getConvexHull` essere calcolato usando il metodo. Tutti i punti contenuti in un cluster possono essere recuperati dall'origine dati usando `getClusterLeaves` il metodo.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Guscio convesso area cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l'area del cluster di penna <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convessa Hull</a> di Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregazione dei dati nei cluster

Spesso i cluster sono rappresentati usando un simbolo con il numero di punti all'interno del cluster. Tuttavia, a volte è opportuno personalizzare lo stile dei cluster con metriche aggiuntive. Con le aggregazioni del cluster, è possibile creare e popolare proprietà personalizzate utilizzando un calcolo dell' [espressione di aggregazione](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Le `DataSource`aggregazioni del cluster possono essere `clusterProperties` definite in un'opzione di.

Nell'esempio seguente viene utilizzata un'espressione di aggregazione. Il codice calcola un conteggio in base alla proprietà del tipo di entità di ogni punto dati in un cluster. Quando un utente fa clic su un cluster, viene visualizzata una finestra popup con informazioni aggiuntive sul cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregazioni cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>aggregazioni del cluster</a> Pen di Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Classe origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Oggetto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [spazio dei nomi Atlas. Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Aggiungere un livello Bubble](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello di simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
