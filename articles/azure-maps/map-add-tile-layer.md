---
title: Aggiungere un livello riquadro in Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello sezione a Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3f047ec1aced55038384cbe29bd3a4b8a948dce9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976445"
---
# <a name="add-a-tile-layer-to-a-map"></a>Aggiungere un livello riquadro a una mappa

Questo articolo illustra come sovrapporre un livello riquadro sulla mappa. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Altre informazioni sul sistema di riquadri di Mappe di Azure sono reperibili nella documentazione [Livelli di Zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere sottoposte a pre-rendering e archiviate come qualsiasi altra immagine in un server utilizzando una convenzione di denominazione che il livello riquadro comprenda o un servizio dinamico che generi le immagini immediatamente. Sono supportate tre diverse convenzioni di denominazione del servizio affiancate dalla classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) di Azure Maps: 

* Notazione zoom di X, Y: in base al livello di zoom, x è la posizione nella colonna e y è la posizione nella riga del riquadro nella griglia dei riquadri.
* Notazione Quadkey: combinazione delle informazioni x, y e zoom in un singolo valore stringa che sia un identificatore univoco per un riquadro.
* Rettangolo di selezione: le coordinate del rettangolo di selezione possono essere utilizzate per specificare un'immagine nel formato `{west},{south},{east},{north}` comunemente utilizzata dai [ servizi Web di Mapping (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello riquadro da un'immagine, ma anche i dati vettoriali possono essere sottoposti a rendering come livello riquadro. Sottoponendo a rendering i dati vettoriali come livello riquadro, il controllo della mappa deve solo caricare i riquadri che possono essere molto più piccoli in termini di dimensione del file rispetto ai dati vettoriali che rappresentano. Molti utenti usano questa tecnica per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL di riquadro passato in un livello riquadro deve essere un URL http/https indirizzato a una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti: 

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}` - Un segnaposto in cui verranno aggiunti i valori di sottodominio se specificati.

## <a name="add-a-tile-layer"></a>Aggiungere un livello per i riquadri

 Questo esempio illustra come creare un livello riquadro che punta a una serie di riquadri che usano il sistema di riquadri x, y e zoom. L'origine di questo livello riquadro è una sovrapposizione di radar meteo dall'[Iowa Environmental Mesonet dell'Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Quando si visualizzano i dati radar idealmente, gli utenti possono visualizzare chiaramente le etichette delle città mentre esplorano la mappa. a tale scopo, inserire il livello sezione al di `labels` sotto del livello.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Livello riquadro usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il livello riquadro <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>dell'elemento Pen usando X, Y e Z</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizzare un livello riquadro

La classe del livello sezione presenta molte opzioni di stile. Lo strumento seguente consente di provarle.

<br/>

<iframe height='700' scrolling='no' title='Opzioni del livello riquadro' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere le <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opzioni del livello riquadro</a> dell'elemento Pen con Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un livello immagine](./map-add-image-layer.md)
