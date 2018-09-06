---
title: Aggiungere codice HTML personalizzato in Mappe di Azure | Microsoft Docs
description: Come aggiungere codice HTML personalizzato a una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5060839be244f55700b40735e598964a0b7b6709
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382046"
---
# <a name="add-custom-html-to-the-map"></a>Aggiungere codice HTML personalizzato alla mappa

Questo articolo illustra come aggiungere un codice HTML personalizzato, ad esempio un file di immagine, alla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='466' scrolling='no' title='Aggiungere codice HTML personalizzato a una mappa - PNG' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Add custom html to a map - png</a> (Aggiungere codice HTML personalizzato a una mappa - PNG) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni, è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un elemento HTML da un'immagine.

L'ultimo blocco di codice usa la funzione [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) della classe Map per aggiungere l'immagine alla posizione specificata della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Visualizzare i risultati della ricerca](./map-search-location.md)
* [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)

