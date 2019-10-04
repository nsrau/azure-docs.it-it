---
title: Funzionalità degli stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Informazioni sulle funzionalità relative agli stili delle Mappe di Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881975"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Scegliere uno stili delle mappe in Mappe di Azure

Molti degli [stili di mappa supportati in mappe di Azure](./supported-map-styles.md) sono disponibili in Web SDK. Questo articolo illustra come usare le funzionalità correlate allo stile per impostare uno stile al caricamento della mappa, impostare un nuovo stile e usare il controllo di selezione dello stile.

## <a name="set-style-on-map-load"></a>Impostare lo stile al caricamento della mappa

Nel codice seguente, l' `style` opzione della mappa è impostata su all' `grayscale_dark` inizializzazione.

<br/>

<iframe height='500' scrolling='no' title='Impostazione dello stile al caricamento della mappa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Setting the style on map load</a> (Impostazione dello stile al caricamento della mappa) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Aggiornare lo stile

Nel codice riportato di seguito, dopo il caricamento di un'istanza della mappa, lo stile della mappa viene aggiornato da `road` a `satellite` utilizzando la funzione [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della mappa.

<br/>

<iframe height='500' scrolling='no' title='Aggiornamento dello stile' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Updating the style</a> (Aggiornamento dello stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Aggiungere il selettore di stile

Il codice seguente aggiunge un [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) alla mappa in modo che l'utente possa passare facilmente tra i diversi stili della mappa. 

<br/>

<iframe height='500' scrolling='no' title='Aggiunta del selettore di stile' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adding the style picker</a> (Aggiunta del selettore di stile) di Pen dalle Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Per impostazione predefinita, il controllo selezione stile elenca tutti gli stili disponibili quando si usa il piano tariffario s0 di mappe di Azure per impostazione predefinita. Se si desidera ridurre il numero di stili in questo elenco, passare una matrice degli stili che si desidera visualizzare nell'elenco `mapStyle` nell'opzione della selezione dello stile. Se si utilizza S1 e si desidera visualizzare tutti gli stili disponibili, impostare l' `mapStyles` opzione dello stile selezione su. `"all"`

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Aggiungere i controlli alle mappe:

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](map-add-pin.md)
