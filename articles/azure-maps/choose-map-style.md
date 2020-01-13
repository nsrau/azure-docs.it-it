---
title: Funzionalità dello stile della mappa | Mappe Microsoft Azure
description: In questo articolo vengono fornite informazioni sulle funzionalità correlate allo stile disponibili in Microsoft Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b92e03c4e5346dd39eaba84cfeeedb93e418678c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911784"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Scegliere uno stili delle mappe in Mappe di Azure

Molti degli [stili di mappa supportati in mappe di Azure](./supported-map-styles.md) sono disponibili in Web SDK. Questo articolo illustra come usare le funzionalità correlate allo stile per impostare uno stile al caricamento della mappa, impostare un nuovo stile e usare il controllo di selezione dello stile.

## <a name="set-style-on-map-load"></a>Impostare lo stile al caricamento della mappa

Nel codice seguente, l'opzione `style` della mappa è impostata su `grayscale_dark` all'inizializzazione.

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
> Per impostazione predefinita, il controllo selezione stile elenca tutti gli stili disponibili quando si usa il piano tariffario s0 di mappe di Azure per impostazione predefinita. Se si desidera ridurre il numero di stili in questo elenco, passare una matrice degli stili che si desidera visualizzare nell'elenco nell'opzione `mapStyle` della selezione dello stile. Se si utilizza S1 e si desidera visualizzare tutti gli stili disponibili, impostare l'opzione `mapStyles` della selezione stile su `"all"`.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Aggiungere i controlli alle mappe:

> [!div class="nextstepaction"]
> [Aggiungere controlli mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](map-add-pin.md)
