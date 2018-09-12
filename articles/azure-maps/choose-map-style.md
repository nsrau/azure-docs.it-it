---
title: Funzionalità degli stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Informazioni sulle funzionalità relative agli stili delle Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666872"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Scegliere uno stili delle mappe in Mappe di Azure
Mappe di Azure ha quattro stili delle mappe diversi tra cui scegliere. Per altre informazioni sugli stili delle mappe vedere [Stili delle mappe supportati da Mappe di Azure](./supported-map-styles.md). Questo articolo illustra come usare le funzionalità correlate allo stile per impostare uno stile al caricamento della mappa, impostare un nuovo stile e usare il controllo di selezione dello stile.

## <a name="setting-style-on-map-load"></a>Impostazione dello stile al caricamento della mappa

<iframe height='500' scrolling='no' title='Impostazione dello stile al caricamento della mappa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> (Impostazione dello stile al caricamento della mappa) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il codice precedente crea un oggetto mappa con lo stile impostato sulla scala di grigi. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

## <a name="updating-the-style"></a>Aggiornamento dello stile

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice nel codice precedente crea un oggetto mappa senza preimpostare lo stile. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo clock di codice usa il metodo [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) della mappa per impostare lo stile della mappa sul satellite.

## <a name="adding-the-style-picker"></a>Aggiunta del selettore di stile

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice nel codice precedente crea un oggetto mappa senza preimpostare lo stile. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo blocco di codice costruisce un selettore di stile usando il costruttore [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) dell'atlante.

Un selettore di stile consente di selezionare lo stile per la mappa. Il terzo blocco di codice aggiunge il selettore di stile alla mappa usando il metodo [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlante](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:
* [Aggiungere controlli mappa](./map-add-controls.md)
* [Aggiungere un segnaposto](./map-add-pin.md)
