---
title: Aggiunta di un layer di sezioni a una mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come sovrapporre un layer di sezioni su una mappa usando Microsoft Azure Maps Web SDK. I livelli affiancati consentono di eseguire il rendering delle immagini su una mappa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988601"
---
# <a name="add-a-tile-layer-to-a-map"></a>Aggiungere un livello riquadro a una mappa

In questo articolo viene illustrato come sovrapporre un layer Tile sulla mappa. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Per altre informazioni sul sistema di affiancamento di Azure Maps, vedere Livelli di [zoom e griglia dei riquadri.](zoom-levels-and-tile-grid.md)

Un layer Tile viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate o renderizzate dinamicamente. Le immagini sottoposte a rendering preliminare vengono archiviate come qualsiasi altra immagine in un server usando una convenzione di denominazione che il livello di riquadro riconosce. Le immagini renderizzate in modo dinamico usano un servizio per caricare le immagini vicino al tempo reale. Esistono tre diverse convenzioni di denominazione del servizio di tipo riquadro supportate dalla classe TileLayer di Azure:There are three different tile service [namingconventions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) supported by Azure Maps TileLayer class: 

* X, Y, notazione zoom - X è la colonna, Y è la posizione di riga del riquadro nella griglia del riquadro e la notazione di zoom un valore basato sul livello di zoom.
* Notazione quadkey - Combina le informazioni x, y e zoom in un singolo valore stringa. Questo valore stringa diventa un identificatore univoco per un singolo riquadro.
* Riquadro di delimitazione: specificare un'immagine nel formato delle coordinate del riquadro di delimitazione: `{west},{south},{east},{north}`. Questo formato viene comunemente utilizzato da [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello di riquadro da un'immagine, ma anche i dati vettoriali possono essere sottoposti a rendering anche come livello di riquadro. Eseguendo il rendering dei dati vettoriali come layer di sezioni, il controllo mappa deve solo caricare i riquadri di dimensioni inferiori rispetto ai dati vettoriali che rappresentano. Questa tecnica viene comunemente utilizzata per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL del riquadro passato in un livello Tile deve essere un URL http o https a una risorsa TileJSON o a un modello di URL di riquadro che usa i parametri seguenti: 

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}`- Un segnaposto per i valori `subdomain` del sottodominio, se specificato verrà aggiunto l'oggetto .

## <a name="add-a-tile-layer"></a>Aggiungere un livello per i riquadri

 In questo esempio viene illustrato come creare un layer di sezioni che punta a un set di riquadri. Questo esempio usa il sistema di astatura x, y, zoom. L'origine di questo livello riquadro è una sovrapposizione di radar meteo dall'[Iowa Environmental Mesonet dell'Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Quando visualizzano i dati radar, idealmente gli utenti vedono chiaramente le etichette delle città mentre navigano nella mappa. Questo comportamento può essere implementato inserendo il `labels` livello di riquadro sotto il livello.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Livello riquadro usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il livello riquadro <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>dell'elemento Pen usando X, Y e Z</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizzare un livello riquadro

La classe di livello tile ha molte opzioni di stile. Lo strumento seguente consente di provarle.

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
> [Aggiungere un livello per le immagini](./map-add-image-layer.md)
