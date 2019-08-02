---
title: Aggiungere controlli mappa in Mappe di Azure | Microsoft Docs
description: Come aggiungere i controlli zoom, inclinazione, rotazione e un selettore di stile a una mappa in Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638765"
---
# <a name="add-map-controls-to-azure-maps"></a>Aggiungere controlli mappa in Mappe di Azure

Questo articolo illustra come aggiungere i controlli mappa a una mappa. Si apprenderà anche come creare una mappa con tutti i controlli e un [selettore di stile](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Aggiungere un controllo zoom

<iframe height='500' scrolling='no' title='Aggiunta di un controllo zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control</a> (Aggiunta di un controllo zoom) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice crea un oggetto map usando il meccanismo di autenticazione anonimo. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il controllo zoom consente di fare zoom avanti o indietro sulla mappa. Il secondo blocco di codice crea un oggetto Controllo zoom usando [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa. Il Controllo zoom è all'interno del **listener di eventi** della mappa per garantire il caricamento dopo che la mappa è stata caricata completamente.

## <a name="add-pitch-control"></a>Aggiungere un controllo inclinazione

<iframe height='500' scrolling='no' title='Aggiunta di un controllo inclinazione' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control</a> (Aggiunta di un controllo inclinazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice crea un oggetto map usando il meccanismo di autenticazione anonimo. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il controllo inclinazione consente di modificare l'inclinazione della mappa. Il secondo blocco di codice crea un oggetto Controllo inclinazione con [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa. Il Controllo inclinazione è all'interno del **listener di eventi** della mappa per garantire il caricamento dopo che la mappa è stata caricata completamente.

## <a name="add-compass-control"></a>Aggiungere un controllo bussola

<iframe height='500' scrolling='no' title='Aggiunta di un controllo rotazione' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control</a> (Aggiunta di un controllo rotazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice crea un oggetto map usando il meccanismo di autenticazione anonimo. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto controllo bussola usando [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) di Atlas. Aggiunge anche il controllo bussola alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Il Controllo bussola è all'interno del **listener di eventi** della mappa per garantire il caricamento dopo che la mappa è stata caricata completamente.

## <a name="a-map-with-all-controls"></a>Una mappa con tutti i controlli

<iframe height='500' scrolling='no' title='Una mappa con tutti i controlli' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls</a> (Una mappa con tutti i controlli) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice crea un oggetto map usando il meccanismo di autenticazione anonimo. Per le istruzioni è possibile vedere [creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto Controllo bussola usando [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa.

Il terzo blocco di codice crea un oggetto Controllo zoom usando [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa.

Il quarto blocco di codice crea un oggetto Controllo inclinazione con [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa.

L'ultimo blocco di codice crea un oggetto Selettore di stile usando [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) di Atlas e lo aggiunge alla mappa usando il metodo [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa. Tutti gli oggetti di controllo sono aggiunti all'interno del **listener di eventi** della mappa per garantire che vengano caricati dopo che la mappa è stata caricata completamente.

L'ordine degli oggetti di controllo nello script determina l'ordine in cui vengono visualizzati sulla mappa. Per modificare l'ordine dei controlli sulla mappa, è possibile modificarne l'ordine nello script.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Per il codice completo, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](./map-add-popup.md)
