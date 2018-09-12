---
title: Visualizzare le indicazioni stradali con Mappe di Azure | Microsoft Docs
description: Come visualizzare le indicazioni stradali tra due posizioni su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781497"
---
# <a name="show-directions-from-a-to-b"></a>Visualizzare le indicazioni stradali da A a B 

Questo articolo illustra come effettuare una richiesta di pianificazione percorso e visualizzare il percorso sulla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

La riga nel secondo blocco di codice crea un'istanza di un client del servizio.

Il terzo blocco di codice inizializza il [livello di linea spezzata](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) sulla mappa.

Il quarto blocco di codice crea e aggiunge i segnaposto sulla mappa per rappresentare il punto di partenza e quello di arrivo del percorso. Per le istruzioni, è possibile vedere [Aggiungere un segnaposto sulla mappa](map-add-pin.md).

Il successivo blocco di codice usa la funzione [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) della classe Map per impostare il rettangolo di selezione della mappa in base al punto di partenza e a quello di arrivo del percorso.

Il sesto blocco di codice crea una query di route.

L'ultimo blocco di codice esegue query sul servizio di routing di Mappe di Azure tramite il metodo [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) per ottenere una route tra il punto iniziale e quello di destinazione. La risposta viene quindi analizzata in formato GeoJSON con il metodo [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Aggiunge tutte le linee sulla mappa per eseguire il rendering della route. Per altre informazioni, è possibile vedere [Aggiungere una linea sulla mappa](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Livello di linea spezzata](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Mostrare il traffico sulla mappa](./map-show-traffic.md)
* [Interazione con la mappa - eventi del mouse](./map-events.md)
