---
title: Creare una mappa con Mappe di Azure | Microsoft Docs
description: Come creare una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97b94cf54454a83510c5be2cf0d71281dbf5b004
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424240"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa e aggiungere un'animazione a una mappa.  

## <a name="understand-the-code"></a>Informazioni sul codice

Esistono due modi per costruire una mappa. È possibile impostare la fotocamera della mappa specificando il punto centrale e il livello di zoom oppure impostare i limiti della fotocamera della mappa specificando i punti di delimitazione sudoccidentale e nordorientale.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Impostare la fotocamera

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via (Creare una mappa con) `CameraOptions` </a>di Servizi Location Based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente viene creato un [oggetto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tramite `new atlas.Map()` e vengono impostati il centro e lo zoom. Le proprietà della mappa, ad esempio il centro e il livello di zoom, fanno parte di [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Impostare i limiti della fotocamera

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via (Creare una mappa con) `CameraBoundsOptions` </a>di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente viene costruito un [oggetto mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) usando `new atlas.Map()`. Le proprietà della mappa, ad esempio `CameraBoundsOptions`, possono essere definite tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) della classe Map. I limiti e le proprietà relative alla spaziatura interna vengono impostati tramite `setCamera`.

### <a name="animate-map-view"></a>Aggiungere un'animazione alla visualizzazione della mappa

<iframe height='500' scrolling='no' title='Aggiungere un'animazione alla visualizzazione della mappa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a> (Aggiungi animazione a visualizzazione mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice nel codice precedente crea un [oggetto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tramite `new atlas.Map()`. Le proprietà della mappa, ad esempio il centro e il livello di zoom, fanno parte di [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` può essere definita nel costruttore della mappa o usando la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) della classe Map. Lo [stile della mappa](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) è impostato su `road`.

Il secondo blocco di codice crea una funzione di animazione della mappa, che aggiunge un'animazione di modifica nella visualizzazione della mappa definendo [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera). La funzione viene attivata con il pulsante per l'animazione della mappa in modo da generare un livello di zoom casuale a ogni clic.

## <a name="try-out-the-code"></a>Provare il codice

Esaminare il codice di esempio precedente. È possibile modificare il codice JavaScript nella **scheda JS** a sinistra e visualizzare le modifiche apportate alla visualizzazione della mappa nella **scheda Result** (Risultato) a destra. È anche possibile fare clic sul pulsante **Edit on CodePen** (Modifica in CodePen) e modificare il codice in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](map-add-controls.md)
