---
title: Aggiunta di controlli a una mappa Mappe di Microsoft Azure
description: Come aggiungere il controllo zoom, il controllo passo, il controllo di rotazione e una selezione stile a una mappa in Microsoft Azure Maps.How to add zoom control, pitch control, rotate control and a style picker to a map in Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334570"
---
# <a name="add-controls-to-a-map"></a>Aggiungere controlli a una mappa

In questo articolo viene illustrato come aggiungere controlli a una mappa. Si apprenderà inoltre come creare una mappa con tutti i controlli e un selettore di [stile.](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

## <a name="add-zoom-control"></a>Aggiungere un controllo zoom

Un controllo zoom aggiunge pulsanti per ingrandire e ridurre la mappa. Nell'esempio di codice riportato di seguito viene creata un'istanza della classe [ ](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e la aggiunge l'angolo inferiore destro della mappa.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control</a> (Aggiunta di un controllo zoom) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Aggiungere un controllo inclinazione

Un controllo passo aggiunge pulsanti per inclinare il passo per mappare rispetto all'orizzonte. Nell'esempio di codice riportato di seguito viene creata un'istanza della classe [PitchControl.](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Aggiunge il PitchControl all'angolo superiore destro della mappa.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo inclinazione' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control</a> (Aggiunta di un controllo inclinazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Aggiungere un controllo bussola

Un controllo bussola aggiunge un pulsante per ruotare la mappa. Nell'esempio di codice riportato di seguito viene creata un'istanza della classe [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) e aggiunta nell'angolo inferiore sinistro della mappa.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Di seguito è riportato l'esempio di codice in esecuzione completo della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo rotazione' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control</a> (Aggiunta di un controllo rotazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Una mappa con tutti i controlli

Più controlli possono essere inseriti in una matrice e aggiunti alla mappa tutti contemporaneamente e posizionati nella stessa area della mappa per semplificare lo sviluppo. Di seguito vengono aggiunti i controlli di spostamento standard alla mappa utilizzando questo approccio.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

L'esempio di codice seguente aggiunge i controlli di zoom, bussola, passo e selezione stile all'angolo superiore destro della mappa. Si noti come si impilano automaticamente. L'ordine degli oggetti di controllo nello script determina l'ordine in cui vengono visualizzati sulla mappa. Per modificare l'ordine dei controlli sulla mappa, è possibile modificarne l'ordine nella matrice.

<br/>

<iframe height='500' scrolling='no' title='Una mappa con tutti i controlli' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls</a> (Una mappa con tutti i controlli) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il controllo selezione stile è definito dalla classe [StyleControl.The](/javascript/api/azure-maps-control/atlas.control.stylecontrol) style picker control is defined by the StyleControl class. Per ulteriori informazioni sull'utilizzo del controllo selezione stile, consultate [Scegliere uno stile mappa.](choose-map-style.md)

## <a name="customize-controls"></a>Personalizzare i controlli

Ecco uno strumento per testare le varie opzioni per la personalizzazione dei controlli.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni di controllo di navigazione" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere le opzioni del <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>controllo</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>Navigazione penna di Mappe di Azure ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Se si desidera creare controlli di spostamento personalizzati, `atlas.Control` creare una classe che si estende dalla classe o creare un elemento HTML e posizionarlo sopra il div della mappa. Fare in modo che `setCamera` questo controllo dell'interfaccia utente chiami la funzione maps per spostare la mappa. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Controllo bussola](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Per il codice completo, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Aggiungere un segnaposto](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Aggiungere un popup](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](map-add-shape.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello bolla](map-add-bubble-layer.md)

