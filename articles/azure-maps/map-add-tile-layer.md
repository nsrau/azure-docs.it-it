---
title: Aggiungere un livello sezione a una mappa | Mappe Microsoft Azure
description: In questo articolo si apprenderà come sovrapporre un livello sezione su una mappa usando il Microsoft Azure Maps Web SDK. I livelli sezione consentono di eseguire il rendering delle immagini su una mappa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988601"
---
# <a name="add-a-tile-layer-to-a-map"></a>Aggiungere un livello riquadro a una mappa

Questo articolo illustra come sovrapporre un livello sezione sulla mappa. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Per altre informazioni sul sistema di affiancamento delle mappe di Azure, vedere [livelli di zoom e griglia dei riquadri](zoom-levels-and-tile-grid.md).

Un livello sezione viene caricato in riquadri da un server. Queste immagini possono essere pre-renderizzate o sottoposte a rendering dinamico. Le immagini con rendering preliminare vengono archiviate come qualsiasi altra immagine in un server utilizzando una convenzione di denominazione riconosciuta dal livello sezione. Le immagini sottoposte a rendering dinamico usano un servizio per caricare le immagini quasi in tempo reale. Sono supportate tre diverse convenzioni di denominazione del servizio affiancate dalla classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) di Azure Maps: 

* X, Y, notazione zoom-X è la colonna, Y è la posizione della riga del riquadro nella griglia affiancata e la notazione di zoom un valore in base al livello di zoom.
* Notazione quadkey: combina le informazioni x, y e zoom in un unico valore stringa. Questo valore stringa diventa un identificatore univoco per un singolo riquadro.
* Rettangolo di delimitazione: specificare un'immagine nel formato delle coordinate del rettangolo di delimitazione: `{west},{south},{east},{north}`. Questo formato viene comunemente usato da [Web Mapping Services (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) è un ottimo modo per visualizzare grandi set di dati sulla mappa. Non solo è possibile generare un livello sezione da un'immagine. anche i dati vettoriali possono essere visualizzati come livello sezione. Eseguendo il rendering dei dati vettoriali come livello sezione, il controllo mappa deve caricare solo i riquadri più piccoli nelle dimensioni dei file rispetto ai dati vettoriali che rappresentano. Questa tecnica viene comunemente usata per eseguire il rendering di milioni di righe di dati sulla mappa.

L'URL del riquadro passato a un livello sezione deve essere un URL http o HTTPS per una risorsa TileJSON o un modello di URL di riquadro che usa i parametri seguenti: 

* `{x}` - posizione X del riquadro. Necessita inoltre di `{y}` e `{z}`.
* `{y}` - posizione Y del riquadro. Necessita inoltre di `{x}` e `{z}`.
* `{z}` - Livello di zoom del riquadro. Necessita inoltre di `{x}` e `{y}`.
* `{quadkey}` - Identificatore del riquadro quadkey basato sulla convenzione di denominazione del sistema di riquadri di Mappe di Bing.
* `{bbox-epsg-3857}` - Una stringa  del rettangolo delimitatore nel formato `{west},{south},{east},{north}` nel sistema di riferimento spaziale EPSG 3857.
* `{subdomain}`: segnaposto per i valori del sottodominio, se specificato, verrà aggiunto il `subdomain`.

## <a name="add-a-tile-layer"></a>Aggiungere un livello per i riquadri

 In questo esempio viene illustrato come creare un livello sezione che punta a un set di riquadri. In questo esempio viene utilizzato il sistema di affiancamento zoom x, y. L'origine di questo livello riquadro è una sovrapposizione di radar meteo dall'[Iowa Environmental Mesonet dell'Iowa State University](https://mesonet.agron.iastate.edu/ogc/). Quando si visualizzano i dati radar, idealmente gli utenti visualizzano chiaramente le etichette delle città mentre esplorano la mappa. Questo comportamento può essere implementato inserendo il livello sezione al di sotto del livello di `labels`.

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
