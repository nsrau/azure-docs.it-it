---
title: Aggiungere una forma con Mappe di Azure | Microsoft Docs
description: Come aggiungere una forma a una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 801cbd6367e0843e43121b3582971a437984e863
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247709"
---
# <a name="add-a-shape-to-a-map"></a>Aggiungere una forma a una mappa

Questo articolo illustra come aggiungere una [Forma](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) alla mappa e aggiornare le proprietà di una forma esistente sulla mappa.

<a id="addALine"></a>

## <a name="add-a-line"></a>Aggiungere una linea

<iframe height='500' scrolling='no' title='Aggiungere una linea a una mappa' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Aggiungere una linea a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Una linea è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) di LineString. Quindi crea ed esegue il wrapping di una linea nell'oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma) usando `new atlas.Shape(new atlas.data.Feature((new atlas.data.LineString()))` e lo aggiunge all'origine dati.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) esegue il rendering degli oggetti linea sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). L'ultimo blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello linea vengono creati e aggiunti alla mappa nella funzione [listener eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che la linea venga visualizzata dopo il caricamento completo della mappa.

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Aggiungere un cerchio

<iframe height='500' scrolling='no' title='Aggiungere un cerchio a una mappa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Aggiungere un cerchio a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un cerchio è un [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) del [Punto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Quindi crea ed esegue il wrapping di un cerchio nell'oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma) usando `new atlas.Shape(new atlas.data.Feature(new atlas.data.Point()))` e lo aggiunge all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa nella funzione [listener eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il cerchio venga visualizzato dopo il caricamento completo della mappa.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Aggiungere un poligono

Esistono due modi diversi per aggiungere un poligono alla mappa. Entrambi vengono illustrati negli esempi seguenti.

### <a name="use-polygon-layer"></a>Usare il livello poligono 

<iframe height='500' scrolling='no' title='Aggiungere un poligono a una mappa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Aggiungere un poligono a una mappa</a> con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) della classe [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) (Poligono). `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` viene usato per creare un poligono con coordinate ordinate sottoposte a wrapping in un oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma). L'oggetto shape viene quindi aggiunto all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. L'ultimo blocco di codice crea e aggiunge un livello poligono alla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati e il livello poligono vengono creati e aggiunti alla mappa nella funzione [listener eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il poligono venga visualizzato dopo il caricamento completo della mappa.

### <a name="use-polygon-and-line-layer"></a>Usare il livello poligono e linea

<iframe height='500' scrolling='no' title='Livello poligono e linea per aggiungere un poligono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon and line layer to add polygon</a> (Livello poligono e linea per aggiungere un poligono) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). [Poligono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) di [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) (Poligono). `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` viene usato per creare un poligono con coordinate ordinate sottoposte a wrapping in un oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma). L'oggetto shape viene quindi aggiunto all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). Un [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) è una matrice di linee. Vedere le proprietà di un livello linea in [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest). Il terzo blocco di codice crea i livelli poligono e linea.

L'ultimo blocco di codice aggiunge i livelli poligono e linea alla mappa. L'origine dati e i livelli vengono creati e aggiunti alla mappa nella funzione [listener eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il poligono venga visualizzato dopo il caricamento completo della mappa.

## <a name="update-a-shape"></a>Aggiornare una forma

Una classe Shape (Forma) esegue il wrapping di una [Geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) o [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e facilita il loro aggiornamento e la loro gestione.
`new Shape(data: Feature<data.Geometry, any>)` costruisce un oggetto forma e lo inizializza con la funzionalità specificata.

<br>

<iframe height='500' scrolling='no' title='Aggiornare le proprietà delle forme' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Update shape properties</a> (Aggiornare le proprietà delle forme) con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Un punto è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) della classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) (Punto). Il secondo blocco di codice inizializza il valore del raggio per l'elemento del dispositivo di scorrimento HTML e quindi costruisce ed esegue il wrapping di un oggetto punto in un oggetto classe [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) (Forma).

Il terzo blocco di codice crea una funzione che prende il valore dall'elemento del dispositivo di scorrimento dell'intervallo HTML e modifica il valore del raggio usando il metodo [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) della classe shape.

Nel quarto blocco di codice viene creato un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Il punto viene quindi aggiunto all'origine dati.

Un [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) esegue il rendering dei dati sottoposti a wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sulla mappa. Il terzo blocco di codice crea un livello poligono. Vedere le proprietà di un livello poligono in [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). L'origine dati, il click event listener e il livello poligono vengono creati e aggiunti alla mappa nella funzione [listener eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che il punto venga visualizzato dopo il caricamento completo della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere codice HTML personalizzato](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Visualizzare i risultati della ricerca](./map-search-location.md)
