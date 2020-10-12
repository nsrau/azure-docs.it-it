---
title: Aggiungere controlli a una mappa | Mappe Microsoft Azure
description: Come aggiungere un controllo zoom, un controllo pitch, una rotazione e una selezione stile a una mappa in Microsoft Azure maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 08a3b72bb137713721345ecb52cf4e1bb7afcdc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310765"
---
# <a name="add-controls-to-a-map"></a>Aggiungere controlli a una mappa

Questo articolo illustra come aggiungere controlli a una mappa. Si apprenderà anche come creare una mappa con tutti i controlli e un [selettore di stile](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Aggiungere un controllo zoom

Un controllo zoom aggiunge pulsanti per lo zoom avanti e indietro della mappa. Nell'esempio di codice seguente viene creata un'istanza della classe [zoomcontrol](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e viene aggiunto l'angolo inferiore destro della mappa.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adding a zoom control</a> (Aggiunta di un controllo zoom) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Aggiungere un controllo inclinazione

Un controllo pitch aggiunge i pulsanti per inclinare il pitch per eseguire il mapping rispetto all'orizzonte. Nell'esempio di codice seguente viene creata un'istanza della classe [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Aggiunge PitchControl all'angolo superiore destro della mappa.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo inclinazione' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adding a pitch control</a> (Aggiunta di un controllo inclinazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Aggiungere un controllo bussola

Un controllo Compass aggiunge un pulsante per la rotazione della mappa. L'esempio di codice seguente crea un'istanza della classe del [controllo Compass](/javascript/api/azure-maps-control/atlas.control.compasscontrol) e la aggiunge all'angolo inferiore sinistro della mappa.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Di seguito è riportato l'esempio di codice completo e funzionante della funzionalità precedente.

<br/>

<iframe height='500' scrolling='no' title='Aggiunta di un controllo rotazione' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adding a rotate control</a> (Aggiunta di un controllo rotazione) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Una mappa con tutti i controlli

È possibile inserire più controlli in una matrice e aggiungerli contemporaneamente alla mappa e posizionarli nella stessa area della mappa per semplificare lo sviluppo. Nell'esempio seguente vengono aggiunti i controlli di navigazione standard alla mappa utilizzando questo approccio.

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

Nell'esempio di codice seguente vengono aggiunti i controlli zoom, Compass, pitch e selettore stile all'angolo superiore destro della mappa. Si noti come vengono automaticamente impilati. L'ordine degli oggetti di controllo nello script determina l'ordine in cui vengono visualizzati sulla mappa. Per modificare l'ordine dei controlli sulla mappa, è possibile modificarne l'ordine nella matrice.

<br/>

<iframe height='500' scrolling='no' title='Una mappa con tutti i controlli' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>A map with all the controls</a> (Una mappa con tutti i controlli) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il controllo selezione stile è definito dalla classe [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Per ulteriori informazioni sull'utilizzo del controllo selezione stile, vedere [scegliere uno stile mappa](choose-map-style.md).

## <a name="customize-controls"></a>Personalizzare i controlli

Ecco uno strumento che consente di testare le varie opzioni per la personalizzazione dei controlli.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opzioni di controllo di navigazione" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere le <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Opzioni di controllo di navigazione</a> per le penne da mappe di Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Se si desidera creare controlli di navigazione personalizzati, creare una classe che si estende dalla `atlas.Control` classe oppure creare un elemento HTML e posizionarlo sopra il div della mappa. Fare in modo che il controllo dell'interfaccia utente chiami la `setCamera` funzione Maps per spostare la mappa. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Controllo Compass](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

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
> [Aggiungere un livello per le bolle](map-add-bubble-layer.md)

