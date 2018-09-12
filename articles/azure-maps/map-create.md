---
title: Creare una mappa con Mappe di Azure | Microsoft Docs
description: Come creare una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5d48e2e7316f33a565fc61a769a29c00834eed5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343370"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa.  

## <a name="understand-the-code"></a>Informazioni sul codice

Esistono due modi per costruire una mappa. È possibile impostare la fotocamera della mappa specificando il punto centrale e il livello di zoom o impostare i limiti della fotocamera della mappa specificando il punto di delimitazione sudoccidentale e il punto di delimitazione nordorientale.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Impostazione della fotocamera

<iframe height='310' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via CameraOptions</a> (Creare una mappa tramite CameraOptions) di Servizi Location Based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente viene creato un [oggetto mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tramite `new atlas.Map()`. Le proprietà della mappa, ad esempio il centro e il livello di zoom, fanno parte di [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). La classe CameraOptions può essere definita nel costruttore della mappa o tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) della classe Map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Impostazione dei limiti della fotocamera

<iframe height='310' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via CameraBoundsOptions</a> (Creare una mappa tramite CameraBoundsOptions) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente viene costruito un [oggetto mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tramite `new atlas.Map()`. Le proprietà della mappa, ad esempio il rettangolo di selezione, fanno parte di [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). La classe CameraBoundsOptions può essere definita tramite la funzione [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) della classe Map.

## <a name="try-out-the-code"></a>Provare il codice 

Esaminare il codice di esempio precedente. È possibile modificare il codice JavaScript nella scheda JS a sinistra e visualizzare le modifiche apportate alla visualizzazione della mappa nella scheda Result (Risultato) a destra. È anche possibile fare clic sul pulsante "Edit on CodePen" (Modifica in CodePen) e modificare il codice in CodePen. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Scegliere uno stile mappa](choose-map-style.md)
* [Aggiungere controlli mappa](map-add-controls.md)
    

