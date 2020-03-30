---
title: Mostrare il traffico su una mappa Mappe di Microsoft Azure
description: In questo articolo si apprenderà come visualizzare i dati sul traffico su una mappa usando Il Web SDK di Microsoft Azure Maps.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534815"
---
# <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In Mappe di Azure sono disponibili due tipi di dati sul traffico:There are two types of traffic data available in Azure Maps:

- Dati relativi agli incidenti: sono costituiti da dati basati su punti e linee per operazioni quali la costruzione, le chiusure stradali e gli incidenti.
- Dati di flusso: fornisce metriche sul flusso del traffico sulle strade. Spesso, i dati del flusso di traffico vengono utilizzati per colorare le strade. I colori si basano sulla quantità di traffico che rallenta il flusso, rispetto al limite di velocità o a un'altra metrica. I dati del flusso di traffico in Azure Maps hanno tre diverse metriche di misurazione:The traffic flow data in Azure Maps has three different metrics of measurement:
    - `relative`- è relativo alla velocità di flusso libero della strada.
    - `absolute`- è la velocità assoluta di tutti i veicoli sulla strada.
    - `relative-delay`- visualizza le aree più lente del ritardo medio previsto.

Il codice seguente mostra come visualizzare i dati sul traffico sulla mappa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Mostrare il traffico su una mappa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Visualizzare il traffico su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opzioni di sovrapposizione del traffico

Lo strumento seguente consente di passare tra le diverse impostazioni di sovrapposizione del traffico per vedere come cambia il rendering. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni di sovrapposizione del traffico" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le opzioni di <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>sovrapposizione Traffico</a> penna di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Migliorare le esperienze utente:

> [!div class="nextstepaction"]
> [Interazione della mappa con gli eventi del mouse](map-events.md)

> [!div class="nextstepaction"]
> [Compilazione di una mappa accessibile](map-accessibility.md)

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)
