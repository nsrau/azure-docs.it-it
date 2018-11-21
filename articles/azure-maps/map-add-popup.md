---
title: Aggiungere un popup con Mappe di Azure | Microsoft Docs
description: Come aggiungere un popup alla mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568115"
---
# <a name="add-a-popup-to-the-map"></a>Aggiungere un popup alla mappa

Questo articolo illustra come aggiungere un pop a un punto in una mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Aggiungere un popup usando Mappe di Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a> (Aggiungere un popup usando Mappe di Azure) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md). Crea anche il contenuto HTML da visualizzare nel popup.

Il secondo blocco di codice crea un oggetto di origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Un punto è una [funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) della classe [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest). Un oggetto punto con le proprietà name e description viene quindi creato e aggiunto all'origine dati.

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  Nel terzo blocco di codice viene creato un livello simbolo. L'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

Il quarto blocco di codice crea un [oggetto popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) tramite `new atlas.Popup()`. Le proprietà del popup, ad esempio position e pixelOffset, fanno parte di [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). La classe PopupOptions può essere definita nel costruttore del popup tramite la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe Popup. Viene quindi creato un listener di eventi `mouseover` per il livello simbolo.

L'ultimo blocco di codice crea una funzione che viene attivata dal listener di eventi `mouseover`. Imposta il contenuto e le proprietà del popup e aggiunge l'oggetto popup alla mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Vedere gli articoli seguenti che contengono esempi efficaci di codice completo:

> [!div class="nextstepaction"]
> [Aggiungere una forma](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere codice HTML personalizzato](./map-add-custom-html.md)
