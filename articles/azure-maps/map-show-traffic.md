---
title: Mostrare il traffico con Mappe di Azure | Microsoft Docs
description: Come visualizzare i dati relativi al traffico su una mappa di Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638625"
---
# <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In questo articolo viene illustrato come visualizzare le informazioni riguardanti il traffico e gli eventi imprevisti sulla mappa.

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='456' scrolling='no' title='Mostrare il traffico su una mappa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Visualizzare il traffico su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](map-create.md).

Il secondo blocco di codice usa la funzione [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) all'interno della funzione del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per eseguire il rendering dei flussi di traffico e gli eventi imprevisti sulla mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)

Migliorare le esperienze utente:

> [!div class="nextstepaction"]
> [Interazione della mappa con gli eventi del mouse](./map-events.md)

> [!div class="nextstepaction"]
> [Compilazione di una mappa accessibile](./map-accessibility.md)
