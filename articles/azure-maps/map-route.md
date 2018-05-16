---
title: Visualizzare le indicazioni stradali con Mappe di Azure | Microsoft Docs
description: Come visualizzare le indicazioni stradali tra due posizioni su una mappa JavaScript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 9007afd1bc4d2361addc2a554fab1330174e88e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="show-directions-from-a-to-b"></a>Visualizzare le indicazioni stradali da A a B 

Questo articolo illustra come effettuare una richiesta di pianificazione percorso e visualizzare il percorso sulla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni, è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea e aggiunge i segnaposto sulla mappa per rappresentare il punto di partenza e quello di arrivo del percorso. Per le istruzioni, è possibile vedere [Aggiungere un segnaposto sulla mappa](map-add-pin.md).

Il terzo blocco di codice usa la funzione [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) della classe Map per impostare il rettangolo di selezione della mappa in base al punto di partenza e a quello di arrivo del percorso.

Il quarto blocco di codice invia un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API Route di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

L'ultimo blocco di codice analizza la risposta in arrivo. Se la risposta ha esito positivo, raccoglie le informazioni su latitudine e longitudine per ogni punto del tragitto. Crea una matrice di linee collegando ogni punto del tragitto a quello successivo. Aggiunge tutte le linee sulla mappa per eseguire il rendering della route. Per le istruzioni, è possibile vedere [add a line on the map](./map-add-shape.md#addALine) (Aggiungere una linea sulla mappa).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
