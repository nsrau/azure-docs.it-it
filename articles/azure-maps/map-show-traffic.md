---
title: Mostrare il traffico con Mappe di Azure | Microsoft Docs
description: Come visualizzare i dati relativi al traffico su una mappa di Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600120"
---
# <a name="show-traffic-on-the-map"></a>Mostrare il traffico sulla mappa

In questo articolo viene illustrato come visualizzare le informazioni riguardanti il traffico e gli eventi imprevisti sulla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='456' scrolling='no' title='Mostrare il traffico su una mappa' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a> (Visualizzare il traffico su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](map-create.md).

Il secondo blocco di codice usa la funzione [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) della classe della mappa per eseguire il rendering dei flussi di traffico e gli eventi imprevisti sulla mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Interazione con la mappa - eventi del mouse](./map-events.md)
* [Compilazione di una mappa accessibile](./map-accessibility.md)

Per altri scenari di mapping, vedere la [pagina dell'esempio di codice](http://aka.ms/AzureMapsSamples).
