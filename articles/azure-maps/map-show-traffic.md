---
title: Mostrare il traffico con Mappe di Azure | Microsoft Docs
description: Come visualizzare i dati sul traffico in Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b765f6f35cecfdd3183b4432954fcfb2548de046
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874871"
---
# <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In mappe di Azure sono disponibili due tipi di dati di traffico:

- Dati degli eventi imprevisti: sono costituiti da dati basati su punti e righe per operazioni quali la costruzione, le chiusure stradali e gli incidenti.
- Flussi di dati: fornisce le metriche sul flusso di traffico sulle strade. I dati del flusso di traffico vengono spesso usati per colorare le strade in base alla quantità di traffico che rallenta il flusso rispetto al limite di velocità o ad altre metriche. I dati del flusso di traffico in mappe di Azure hanno tre metriche di misurazione diverse:
    - `relative`-è relativo alla velocità del flusso libero della strada.
    - `absolute`-è la velocità assoluta di tutti i veicoli in viaggio.
    - `relative-delay`-Visualizza le aree più lente rispetto al ritardo medio previsto.

Il codice seguente illustra come visualizzare i dati sul traffico sulla mappa.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Di seguito è riportato l'esempio di codice completo per l'esecuzione delle funzionalità sopra riportate.

<br/>

<iframe height='500' scrolling='no' title='Mostrare il traffico su una mappa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Visualizzare il traffico su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opzioni di sovrapposizione del traffico

Lo strumento seguente consente di passare tra le diverse impostazioni di sovrapposizione del traffico per vedere come cambia il rendering. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni di sovrapposizione del traffico" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le opzioni di sovrimpressione del <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>traffico</a> penna per<a href='https://codepen.io/azuremaps'>@azuremaps</a>mappe di Azure () in <a href='https://codepen.io'>CodePen</a>.
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
