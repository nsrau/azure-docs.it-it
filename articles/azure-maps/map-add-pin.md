---
title: Aggiungere un segnaposto con Mappe di Azure | Microsoft Docs
description: Come aggiungere un segnaposto a una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0dafb09e1704e8e446b034975f0c25a740050599
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382585"
---
# <a name="add-pins-to-the-map"></a>Aggiungere i segnaposto alla mappa

Questo articolo illustra come aggiungere un segnaposto alla mappa.  

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Aggiungere un segnaposto a una mappa' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Add a pin to a map</a> (Aggiungere un segnaposto a una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni, è possibile vedere [Creare una mappa](./map-create.md).

Nel secondo blocco di codice un segnaposto viene creato e aggiunto alla mappa. Un segnaposto è una classe [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) di [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) con [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) come proprietà di Feature. Usare `new atlas.data.Feature(new atlas.data.Point())` per creare un segnaposto e definirne le proprietà. Un livello di segnaposto è una matrice di segnaposto. Usare la funzione [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) della classe Map per aggiungere un livello di segnaposto alla mappa e definire le proprietà del livello di segnaposto. Vedere le proprietà di un livello di segnaposto in [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Aggiungere un popup](./map-add-popup.md)
* [Aggiungere una forma](./map-add-shape.md)

