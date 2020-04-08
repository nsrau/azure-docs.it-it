---
title: Dati punto di clustering su una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come eseguire il clustering dei dati dei punti ed eseguirne il rendering su una mappa usando Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804572"
---
# <a name="clustering-point-data"></a>Dati punto di clustering

Quando si visualizzano molti punti dati sulla mappa, i punti dati possono sovrapporsi l'uno all'altro. La sovrapposizione può causare la mappa può diventare illeggibile e difficile da usare. Il clustering dei dati dei punti è la combinazione di dati dei punti vicini tra loro e rappresentati sulla mappa come singolo punto dati in cluster. Quando l'utente ingrandisce la mappa, i cluster si scompongono nei singoli punti dati. Quando si lavora con un numero elevato di punti dati, usare i processi di clustering per migliorare l'esperienza utente.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Abilitazione del clustering in un'origine datiEnabling clustering on a data source

Abilitare il `DataSource` clustering nella `cluster` classe impostando l'opzione su true. Set `ClusterRadius` per selezionare i punti vicini e combinarli in un cluster. Il valore `ClusterRadius` di è in pixel. Utilizzare `clusterMaxZoom` per specificare un livello di zoom in corrispondenza del quale disabilitare la logica di clustering. Ecco un esempio di come abilitare il clustering in un'origine dati.

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
> Se due punti dati sono vicini sul terreno, è possibile che il cluster non si scompaia mai, indipendentemente dalla vicinanza dello zoom avanti dell'utente. Per risolvere questo problema, `clusterMaxZoom` è possibile impostare l'opzione per disabilitare la logica di clustering e visualizzare semplicemente tutto.

Di seguito sono `DataSource` riportati i metodi aggiuntivi forniti dalla classe per il clustering:Here are additional methods that the class provides for clustering:

| Metodo | Tipo restituito | Descrizione |
|--------|-------------|-------------|
| getClusterChildren(clusterId: numero) | &lt;Geometria&lt;&lt;della feature&gt; \| serie promessa, qualsiasi forma&gt;&gt; | Recupera gli elementi figlio del cluster specificato al livello di zoom successivo. Questi elementi figlio possono essere costituiti da una combinazione di forme e sottocluster. I sottocluster saranno funzionalità con proprietà corrispondenti a ClusteredProperties. |
| getClusterExpansion-oom(clusterId: numero) | Promise&lt;number&gt; | Calcola un livello di zoom in corrispondenza del quale il cluster inizierà a espandersi o scomporsi. |
| getClusterLeaves(clusterId: numero, limite: numero, offset: numero) | &lt;Geometria&lt;&lt;della feature&gt; \| serie promessa, qualsiasi forma&gt;&gt; | Recupera tutti i punti in un cluster. Impostare `limit` per restituire un subset dei punti e usare `offset` per spostarsi attraverso i punti. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visualizzare i cluster utilizzando un livello bolla

Un livello bolla è un ottimo modo per eseguire il rendering dei punti cluster. Utilizzare le espressioni per scalare il raggio e modificare il colore in base al numero di punti nel cluster. Se si visualizzano i cluster utilizzando un livello bolla, è necessario utilizzare un livello separato per eseguire il rendering dei punti dati non cluster.

Per visualizzare le dimensioni del cluster sopra la bolla, utilizzare un layer di simboli con testo e non utilizzare un'icona.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Clustering di strato di bolle di base" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>clustering</a> di bolle Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Basic di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visualizzare i cluster utilizzando un layer di simboli

Quando si visualizzano i punti dati, il layer dei simboli nasconde automaticamente i simboli che si sovrappongono per garantire un'interfaccia utente più pulita. Questo comportamento predefinito potrebbe essere indesiderato se si desidera visualizzare la densità dei punti dati sulla mappa. Tuttavia, queste impostazioni possono essere modificate. Per visualizzare tutti i `allowOverlap` simboli, impostare l'opzione della proprietà Livelli simbolo `iconOptions` su `true`. 

Utilizzare il clustering per mostrare la densità dei punti dati mantenendo un'interfaccia utente pulita. L'esempio seguente mostra come aggiungere simboli personalizzati e rappresentare cluster e singoli punti dati utilizzando il layer dei simboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Livello simbolo cluster" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>layer Pen Clustered</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Symbol di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering e il layer delle mappe termiche

Le mappe termiche sono un ottimo modo per visualizzare la densità dei dati sulla mappa. Questo metodo di visualizzazione può gestire un numero elevato di punti dati da solo. Se i punti dati sono raggruppati e la dimensione del cluster viene utilizzata come peso della mappa termica, la mappa termica può gestire ancora più dati. Per ottenere questa opzione, impostare l'opzione `weight` del layer della mappa termica su . `['get', 'point_count']` Quando il raggio del cluster è ridotto, la mappa termica sarà quasi identica a una mappa termica utilizzando i punti dati non raggruppati, ma funzionerà molto meglio. Tuttavia, minore è il raggio del cluster, più accurata sarà la mappa termica, ma con meno vantaggi in termini di prestazioni.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mappa termica ponderata a grappolo" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>mappa termica ponderata</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Cluster penna di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Eventi del mouse su punti dati cluster

Quando gli eventi del mouse si verificano su un layer che contiene punti dati cluster, il punto dati del cluster torna all'evento come oggetto feature punto GeoJSON. Questa feature punto avrà le seguenti proprietà:

| Nome proprietà             | Type    | Descrizione   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Indica se la funzionalità rappresenta un cluster. |
| `cluster_id`              | string  | ID univoco per il cluster che può essere usato con i metodi `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves` della classe DataSource. |
| `point_count`             | d'acquisto  | Numero di punti contenuti nel cluster.  |
| `point_count_abbreviated` | string  | Stringa che abbrevia `point_count` il valore se è lungo. Ad esempio, 4.000 diventa 4K.  |

Questo esempio accetta un livello a bolle che esegue il rendering dei punti del cluster e aggiunge un evento click. Quando l'evento click viene attivato, il codice calcola e ingrandisce la mappa al livello di zoom successivo, in corrispondenza del quale il cluster si divide. Questa funzionalità viene `getClusterExpansionZoom` implementata `DataSource` utilizzando il `cluster_id` metodo della classe e la proprietà del punto dati cluster su cui è stato fatto clic.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster getClusterExpansion zoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il cluster di penna <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansion zoom</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visualizza l'area del cluster 

I dati punto che un cluster rappresenta viene distribuito su un'area. In questo esempio quando il mouse viene posizionato su un cluster, si verificano due comportamenti principali. In primo luogo, i singoli punti dati contenuti nel cluster verranno utilizzati per calcolare uno scafo convessa. Quindi, lo scafo convessa verrà visualizzato sulla mappa per mostrare un'area.  Uno scafo convesso è un poligono che avvolge un insieme di `atlas.math.getConvexHull` punti come una fascia elastica e può essere calcolato utilizzando il metodo. Tutti i punti contenuti in un cluster possono `getClusterLeaves` essere recuperati dall'origine dati utilizzando il metodo .

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Scafo convesso ad area cluster" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere l'area Cluster di pen <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convex hull</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregazione dei dati nei cluster

Spesso i cluster vengono rappresentati utilizzando un simbolo con il numero di punti all'interno del cluster. Tuttavia, a volte è opportuno personalizzare lo stile dei cluster con metriche aggiuntive. Con le aggregazioni cluster, le proprietà personalizzate possono essere create e popolate utilizzando un calcolo [dell'espressione di aggregazione.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Le aggregazioni del `clusterProperties` cluster possono `DataSource`essere definite nell'opzione dell'opzione .

Nell'esempio seguente viene utilizzata un'espressione di aggregazione. Il codice calcola un conteggio in base alla proprietà del tipo di entità di ogni punto dati in un cluster. Quando un utente fa clic su un cluster, viene visualizzato un popup con informazioni aggiuntive sul cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregati di cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere Le aggregazioni del cluster<a href='https://codepen.io/azuremaps'>@azuremaps</a>di <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>penne</a> di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Classe origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Oggetto DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [spazio dei nomi atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Aggiungere un livello bolla](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello simbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello mappa termica](map-add-heat-map-layer.md)
