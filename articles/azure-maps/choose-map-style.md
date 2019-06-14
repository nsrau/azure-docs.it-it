---
title: Funzionalità degli stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Informazioni sulle funzionalità relative agli stili delle Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60795891"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Scegliere uno stili delle mappe in Mappe di Azure

Mappe di Azure ha quattro stili delle mappe diversi tra cui scegliere. Per altre informazioni sugli stili delle mappe vedere [Stili delle mappe supportati da Mappe di Azure](./supported-map-styles.md). Questo articolo illustra come usare le funzionalità correlate allo stile per impostare uno stile al caricamento della mappa, impostare un nuovo stile e usare il controllo di selezione dello stile.

## <a name="set-style-on-map-load"></a>Impostare lo stile al caricamento della mappa

<iframe height='500' scrolling='no' title='Impostazione dello stile al caricamento della mappa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> (Impostazione dello stile al caricamento della mappa) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il blocco di codice riportato sopra imposta la chiave di sottoscrizione e crea un oggetto Mappa con lo stile impostato su grayscale_dark. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

## <a name="update-the-style"></a>Aggiornare lo stile

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il blocco di codice riportato sopra imposta la chiave di sottoscrizione e crea un oggetto Mappa senza preimpostare lo stile. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo blocco di codice usa il metodo [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa per impostare lo stile della mappa sul satellite.

## <a name="add-the-style-picker"></a>Aggiungere il selettore di stile

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice imposta la chiave di sottoscrizione e crea un oggetto Mappa con lo stile mappa preimpostato su grayscale_dark. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo blocco di codice costruisce un selettore di stile usando il costruttore [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) dell'atlante.

Un selettore di stile consente di selezionare lo stile per la mappa. Il terzo blocco di codice aggiunge il selettore di stile alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa. Il selettore di stile è all'interno del **listener di eventi** della mappa per garantire il caricamento dopo che la mappa è stata caricata completamente.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Aggiungere il controllo alle mappe:

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](./map-add-controls.md)

Aggiungere un segnaposto mappa:

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](./map-add-pin.md)
