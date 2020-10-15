---
title: Clustering dei dati dei punti su una mappa | Mappe di Microsoft Azure
description: Informazioni su come raggruppare i dati dei punti nelle mappe. Vedere come usare Azure Maps Web SDK per eseguire il clustering dei dati, rispondere agli eventi del mouse del cluster e visualizzare le aggregazioni del cluster.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 6d0cb0c6a9cc3080ec4b2fbd2c4b707a52a88319
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285112"
---
# <a name="clustering-point-data"></a>Clustering dei dati dei punti

Quando si visualizzano molti punti dati sulla mappa, questi possono sovrapporsi l'uno all'altro. La sovrapposizione può rendere la mappa illeggibile e difficile da utilizzare. Il clustering dei dati dei punti è la combinazione di dati dei punti vicini tra loro e rappresentati sulla mappa come singolo punto dati in cluster. Quando l'utente ingrandisce la mappa, i cluster si scompongono nei singoli punti dati. Quando si lavora con un numero elevato di punti dati, è possibile implementare i processi di clustering per migliorare l'esperienza utente.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Abilitazione del clustering su un'origine dati

Abilitare il clustering nella classe `DataSource` impostando l'opzione `cluster` su true. Impostare `ClusterRadius` per selezionare i punti vicini e combinarli in un cluster. Il valore di `ClusterRadius` è indicato in pixel. Utilizzare `clusterMaxZoom` per specificare un livello di zoom su cui disabilitare la logica di clustering. Di seguito è riportato un esempio che illustra come abilitare il clustering in un'origine dati.

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
> Se due punti dati sono vicini, è possibile che il cluster non si scomponga mai, indipendentemente dal livello di ingrandimento selezionato dall'utente. Per risolvere questo problema, è possibile impostare l'opzione `clusterMaxZoom` sulla disabilitazione della logica di clustering per fare in modo che vengano visualizzati tutti gli elementi.

Di seguito sono riportati i metodi aggiuntivi forniti dalla classe `DataSource` per il clustering:

| Metodo | Tipo restituito | Descrizione |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;number&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà a espandersi o scomporsi. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;Array&lt;Feature&lt;Geometry, any&gt; \| Shape&gt;&gt; | Recupera tutti i punti in un cluster. Impostare `limit` per restituire un subset dei punti e usare `offset` per spostarsi attraverso i punti. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visualizzare i cluster con un livello bolle

Il livello bolle è particolarmente indicato per il rendering dei punti in cluster. Usare le espressioni per ridimensionare il raggio e modificare il colore in base al numero di punti nel cluster. Se si sceglie di visualizzare i cluster tramite un livello bolle, è necessario usare un livello separato per eseguire il rendering dei punti dati non in cluster.

Per visualizzare le dimensioni del cluster nella parte superiore della bolla, usare un livello simbolo con testo, senza nessuna icona.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering del livello bolle di base" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering del livello bolle di base</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visualizzare i cluster con un livello simbolo

Quando si visualizzano i punti dati, il livello simbolo nasconde automaticamente i simboli che si sovrappongono per un'interfaccia utente più pulita. Questo comportamento predefinito potrebbe non essere desiderato se si intende visualizzare la densità dei punti dati sulla mappa. Tuttavia, è possibile modificare queste impostazioni. Per visualizzare tutti i simboli, impostare l'opzione `allowOverlap` della proprietà `iconOptions` dei livelli simbolo su `true`. 

Usare il clustering per mostrare la densità dei punti dati mantenendo al contempo un'interfaccia utente pulita. Nell'esempio seguente viene illustrato come aggiungere simboli personalizzati e rappresentare i cluster e i singoli punti dati usando il livello simbolo.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simbolo in cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>livello simbolo in cluster</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e livello mappe termiche

Le mappe termiche rappresentano un ottimo modo per visualizzare la densità dei dati sulla mappa. Questo metodo di visualizzazione può gestire autonomamente un numero elevato di punti dati. Se i punti dati sono raggruppati nel cluster e le dimensioni del cluster vengono usate come peso della mappa termica, quest'ultima può gestire ancora più dati. A questo scopo, impostare l'opzione `weight` del livello mappa termica su `['get', 'point_count']`. Se il raggio del cluster è ridotto, la mappa termica sarà quasi identica a una mappa termica che usa i punti dati non in cluster, ma offrirà prestazioni nettamente migliori. Tuttavia, minore è il raggio del cluster, più accurata sarà la mappa termica, ma con meno vantaggi in termini di prestazioni.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica ponderata con cluster" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mappa termica ponderata con cluster</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventi del mouse sui punti dati in cluster

Quando si verificano eventi del mouse su un livello contenente punti dati in cluster, questi ultimi vengono restituiti all'evento sotto forma di oggetti della funzionalità punto GeoJSON. Questa funzionalità punto disporrà delle proprietà seguenti:

| Nome proprietà             | Type    | Descrizione   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se la funzionalità rappresenta un cluster. |
| `cluster_id`              | string  | ID univoco per il cluster che può essere usato con i metodi `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves` della classe DataSource. |
| `point_count`             | d'acquisto  | Numero di punti contenuti nel cluster.  |
| `point_count_abbreviated` | string  | Stringa che abbrevia il valore `point_count` se necessario. Ad esempio, 4.000 diventa 4K.  |

In questo esempio un livello bolle esegue il rendering dei punti del cluster e aggiunge un evento Click. Quando l'evento Click si attiva, il codice calcola e ingrandisce la mappa fino al livello di zoom successivo, in corrispondenza del quale il cluster si scompone. Questa funzionalità viene implementata tramite il metodo `getClusterExpansionZoom` della classe `DataSource` e la proprietà `cluster_id` del punto dati in cluster selezionato.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="getClusterExpansionZoom del cluster" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom del cluster</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visualizzare l'area del cluster 

I dati dei punti rappresentati da un cluster sono distribuiti in un'area. In questo esempio, quando si passa il mouse su un cluster, si verificano due comportamenti principali. Per prima cosa, i singoli punti dati contenuti nel cluster verranno usati per calcolare un hull convesso. Quindi, l'hull convesso verrà visualizzato sulla mappa per mostrare un'area.  Un hull convesso è un poligono che esegue il wrapping di un set di punti come se fosse una banda elastica e può essere calcolato usando il metodo `atlas.math.getConvexHull`. Tutti i punti contenuti in un cluster possono essere recuperati dall'origine dati tramite il metodo `getClusterLeaves`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Hull convesso dell'area del cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l'<a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>hull convesso dell'area del cluster</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregazione dei dati nei cluster

Spesso i cluster sono rappresentati da un simbolo in cui è indicato il numero di punti che contengono. Tuttavia, a volte è opportuno personalizzare lo stile dei cluster con metriche aggiuntive. Con gli aggregati del cluster, è possibile creare e popolare proprietà personalizzate tramite il calcolo di un'[espressione di aggregazione](data-driven-style-expressions-web-sdk.md#aggregate-expression).  Gli aggregati del cluster possono essere definiti nell'opzione `clusterProperties` di `DataSource`.

Nell'esempio seguente viene utilizzata un'espressione di aggregazione. Il codice calcola un numero in base alla proprietà del tipo di entità di ogni punto dati in un cluster. Quando un utente fa clic su un cluster, viene visualizzata una finestra popup con informazioni aggiuntive su quest'ultimo.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregati del cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>aggregati del cluster</a> dell'elemento Penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Classe DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [Oggetto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [Spazio dei nomi atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
