---
title: Aggiungere simboli e marcatori con Mappe di Azure | Microsoft Docs
description: Come aggiungere simboli e marcatori a una mappa JavaScript
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d80d9a4d39a3f21539e5e6e498fc52df213a19
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248813"
---
# <a name="add-symbols-and-markers-to-a-map"></a>Aggiungere simboli e marcatori a una mappa

Questo articolo illustra come aggiungere simboli e marcatori a una mappa usando un'origine dati.

## <a name="add-a-symbol-marker"></a>Aggiungere un simbolo

<iframe height='500' scrolling='no' title='Modifica della posizione della puntina' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Switch pin location</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un punto viene quindi creato e aggiunto all'origine dati. Un punto è una caratteristica ([Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)) della classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest).

Il terzo blocco di codice crea un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) e aggiorna le coordinate del punto al clic del mouse usando il metodo [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) della classe Shape.

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  L'origine dati, il listener dell'evento click e il livello simbolo vengono creati e aggiunti alla mappa all'interno della funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il punto venga visualizzato dopo il caricamento completo della mappa.

## <a name="add-a-custom-symbol"></a>Aggiungere un simbolo personalizzato

<iframe height='500' scrolling='no' title='Origine dati HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiunge un [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) alla mappa usando la proprietà [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) della classe [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). L'HtmlMarker viene aggiunto alla mappa all'interno della funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che venga visualizzato dopo il caricamento completo della mappa.

## <a name="add-bubble-markers"></a>Aggiungere marcatori a bolle

<iframe height='500' scrolling='no' title='Origine dati BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene definita una matrice di posizioni e viene creato un oggetto [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest). Viene quindi creato un oggetto origine dati con la classe[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e l'oggetto MultiPoint viene aggiunto all'origine dati.

Un [livello bolle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) esegue il rendering dei dati basati su punti di cui viene eseguito il wrapping nell'[origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come cerchi sulla mappa. L'ultimo blocco di codice crea un livello bolle e lo aggiunge alla mappa. Vedere le proprietà di un livello bolle in [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest).

L'oggetto MultiPoint, l'origine dati e il livello bolle vengono creati e aggiunti alla mappa nella funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che il cerchio venga visualizzato dopo il caricamento completo della mappa.

## <a name="add-bubble-markers-with-label"></a>Aggiungere marcatori a bolle con etichetta

<iframe height='500' scrolling='no' title='Origine dati su più livelli' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il Pen <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il codice precedente illustra come visualizzare ed etichettare i dati sulla mappa. Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Viene quindi creato un oggetto origine dati usando la classe [origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e il punto viene aggiunto all'origine dati.

Un [livello bolle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) esegue il rendering dei dati basati su punti di cui viene eseguito il wrapping nell'[origine dati](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come cerchi sulla mappa. Il terzo blocco di codice crea un livello bolle e lo aggiunge alla mappa. Vedere le proprietà di un livello bolle in [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest).

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello simbolo alla mappa che esegue il rendering dell'etichetta di testo per la bolla. Vedere le proprietà di un livello simbolo in [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.symbollayeroptions?view=azure-iot-typescript-latest).

L'origine dati e i livelli vengono creati e aggiunti alla mappa nella funzione [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per assicurarsi che i dati vengano visualizzati dopo il caricamento completo della mappa.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un popup](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Aggiungere una forma](./map-add-shape.md)