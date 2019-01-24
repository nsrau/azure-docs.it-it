---
title: Aggiungere un livello riquadro in Mappe di Azure | Microsoft Docs
description: Come aggiungere un livello riquadro alla mappa Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8e18822fff852a82b367acfa45e3d55048b4e54c
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232601"
---
# <a name="add-a-tile-layer-to-a-map"></a>Aggiungere un livello riquadro a una mappa

Questo articolo illustra come sovrapporre un livello riquadro sulla mappa. I livelli riquadro consentono di sovrapporre immagini sopra i riquadri mappa di base in Mappe di Azure. Altre informazioni sul sistema di riquadri di Mappe di Azure sono reperibili nella documentazione [Livelli di Zoom e griglia riquadri](zoom-levels-and-tile-grid.md).

Un livello riquadro viene caricato in riquadri da un server. Queste immagini possono essere sottoposte a pre-rendering e archiviate come qualsiasi altra immagine in un server utilizzando una convenzione di denominazione che il livello riquadro comprenda o un servizio dinamico che generi le immagini immediatamente. Ci sono tre diverse convenzioni di denominazione dei servizi di riquadri supportate dalla classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) di Mappe di Azure; 

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

 Questo esempio illustra come creare un livello riquadro che punta a una serie di riquadri che usano il sistema di riquadri x, y e zoom. L'origine di questo livello riquadro è una sovrapposizione di radar meteo dall'[Iowa Environmental Mesonet dell'Iowa State University](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Livello riquadro usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere il livello riquadro <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>dell'elemento Pen usando X, Y e Z</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice, un [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) viene creato passando un URL formattato a un servizio di riquadri, la dimensione del riquadro e un'opacità per renderlo semitrasparente. Inoltre, quando si aggiunge il livello riquadro alla mappa, viene aggiunto al di sotto del livello `labels` in modo che le etichette sono ancora chiaramente visibili.

## <a name="customize-a-tile-layer"></a>Personalizzare un livello riquadro

Il livello riquadro ha solo un numero finito di opzioni di stile. Lo strumento seguente consente di provarle.

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