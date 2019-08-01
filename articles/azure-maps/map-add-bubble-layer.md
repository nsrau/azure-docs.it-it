---
title: Aggiungere un livello bolle in Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello bolle alla mappa Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639033"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Aggiungere un livello bolle a una mappa

Questo articolo illustra come eseguire il rendering di un punto dati da un'origine dati come livello bolle in una mappa. I livelli bolle eseguono il rendering dei punti come cerchi sulla mappa con raggio dei pixel fisso. 

> [!TIP]
> Per impostazione predefinita i livelli bolle eseguiranno il rendering delle coordinate di tutte le geometrie in un'origine dati. Per limitare il livello in modo che esegua il rendering solo delle funzionalità di `filter` geometria dei punti, impostare `['==', ['geometry-type'], 'Point']` la `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` proprietà del livello su o se si desidera includere anche le funzionalità multipoint.

## <a name="add-a-bubble-layer"></a>Aggiungere un livello per le bolle

<iframe height='500' scrolling='no' title='Origine dati BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene definita una matrice di oggetti [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) e aggiunta a un oggetto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest).

Un [livello bolle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) esegue il rendering dei dati basati su punti di cui viene eseguito il wrapping nell'[origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come cerchi sulla mappa. L'ultimo blocco di codice crea un livello bolle e lo aggiunge alla mappa. Vedere le proprietà di un livello bolle in [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

La matrice di oggetti Point, l'origine dati e il livello bolle vengono creati e aggiunti alla mappa nella funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il cerchio venga visualizzato dopo il caricamento completo della mappa.

## <a name="show-labels-with-a-bubble-layer"></a>Mostrare etichette con un livello bolle

<iframe height='500' scrolling='no' title='Origine dati su più livelli' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il codice precedente illustra come visualizzare ed etichettare i dati sulla mappa. Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Viene quindi creato un oggetto origine dati usando la classe [origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e il punto viene aggiunto all'origine dati.

Un [livello bolle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) esegue il rendering dei dati basati su punti di cui viene eseguito il wrapping nell'[origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come cerchi sulla mappa. Il terzo blocco di codice crea un livello bolle e lo aggiunge alla mappa. Vedere le proprietà di un livello bolle in [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello simbolo alla mappa che esegue il rendering dell'etichetta di testo per la bolla. Vedere le proprietà di un livello simbolo in [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

L'origine dati e i livelli vengono creati e aggiunti alla mappa nella funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che i dati vengano visualizzati dopo il caricamento completo della mappa.

## <a name="customize-a-bubble-layer"></a>Personalizzare un livello bolle

Il livello bolle include solo poche opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello bolle' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opzioni del livello bolle</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usare espressioni di stile basate sui dati](data-driven-style-expressions-web-sdk.md)