---
title: Gestire gli eventi con le mappe di Azure | Microsoft Docs
description: Come creare una mappa di SDK Web interattiva con eventi di mapping
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 99ef5aa0ee8fa542b0aa807cc536ebfbee369e10
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484291"
---
# <a name="interact-with-the-map"></a>Interazione con la mappa

Questo articolo illustra come usare la proprietà [eventi della classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) per evidenziare gli eventi sulla mappa e su livelli diversi della mappa. Viene inoltre illustrato come usare la proprietà degli eventi della classe mappa per evidenziare gli eventi quando si interagisce con un marcatore di HTML.

## <a name="interact-with-the-map"></a>Interazione con la mappa

Giocare con la mappa riportata di seguito e visualizzare gli eventi del mouse corrispondenti evidenziati a destra. È possibile fare clic sulla **scheda JS** per visualizzare e modificare il codice JavaScript. È anche possibile fare clic sul pulsante **Edit on CodePen** (Modifica in CodePen) e modificare il codice in CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - eventi del mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map - mouse events</a> (Interagire con la mappa - eventi del mouse) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagire con i livelli mappa

Il codice seguente evidenzia il nome degli eventi che vengono generati durante l'interazione con il livello dei simboli. Il livello Symbol, Bubble, line e poligono supporta lo stesso set di eventi. I livelli mappa termica e riquadro non supportano alcuno di questi eventi.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - Eventi del livello' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interazione con la mappa - eventi del livello</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagire con il marcatore di HTML

Il codice seguente aggiunge gli eventi della mappa JavaScript a un marcatore HTML. Inoltre evidenzia il nome degli eventi che si attivano durante l'interazione con il marcatore di HTML.

<br/>

<iframe height='500' scrolling='no' title='Interazione con la mappa - Eventi del marcatore di HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interazione con la mappa - eventi del marcatore di HTML</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nella tabella seguente sono elencati tutti gli eventi della classe Map supportati.

| Evento               | DESCRIZIONE |
|---------------------|-------------|
| `boxzoomend`        | Generato al termine di un'interazione con lo zoom della finestra.|
| `boxzoomstart`      | Generato quando viene avviata l'interazione con lo zoom di una finestra.|
| `click`             | Generato quando un dispositivo di puntamento viene premuto e rilasciato nello stesso punto sulla mappa.|
| `close`             | Generato quando il popup viene chiuso manualmente o a livello.|
| `contextmenu`       | Generato quando si fa clic sul pulsante destro del mouse.|
| `data`              | Generato quando vengono caricati o modificati dati della mappa. |
| `dataadded`         | Generato quando vengono aggiunte forme al `DataSource`.|
| `dataremoved`       | Generato quando le forme vengono rimosse dal `DataSource`.|
| `datasourceupdated` | Generato quando viene aggiornato l'oggetto `DataSource`.|
| `dblclick`          | Generato quando si fa doppio clic su un dispositivo di puntamento nello stesso punto sulla mappa.|
| `drag`              | Generato ripetutamente durante un'interazione di "trascinamento della panoramica" sulla mappa, il popup o il marcatore HTML.|
| `dragend`           | Generato quando un'interazione "trascina a Pan" termina sulla mappa, il popup o il marcatore HTML.|
| `dragstart`         | Generato quando viene avviata l'interazione "trascina a Pan" sulla mappa, il popup o il marcatore HTML.|
| `error`             | Generato quando si verifica un errore.|
| `idle`              | <p>Generato dopo il rendering dell'ultimo frame prima che la mappa entri in uno stato "inattivo":<ul><li>Non sono in corso transizioni della fotocamera.</li><li>Sono stati caricati tutti i riquadri attualmente richiesti.</li><li>Tutte le animazioni di dissolvenza/transizione sono state completate.</li></ul></p>|
| `keydown`           | Generato quando si preme un tasto.|
| `keypress`          | Generato quando viene premuto un tasto che produce un carattere tipizzabili (una chiave ANSI).|
| `keyup`             | Generato quando viene rilasciato un tasto.|
| `layeradded`        | Generato quando un livello viene aggiunto alla mappa.|
| `layerremoved`      | Generato quando un livello viene rimosso dalla mappa.|
| `load`              | Generato immediatamente dopo il download di tutte le risorse necessarie e il primo rendering visivo completo della mappa si è verificato.|
| `mousedown`         | Generato quando un dispositivo di puntamento viene premuto all'interno della mappa o quando si trova sopra un elemento.|
| `mouseenter`        | Generato quando un dispositivo di puntamento viene spostato inizialmente sulla mappa o su un elemento. |
| `mouseleave`        | Generato quando un dispositivo di puntamento viene spostato fuori dalla mappa o da un elemento. |
| `mousemove`         | Generato quando un dispositivo di puntamento viene spostato all'interno della mappa o di un elemento.|
| `mouseout`          | Generato quando un dispositivo punto lascia l'area di disegno della mappa lascia un elemento.|
| `mouseover`         | Generato quando un dispositivo di puntamento viene spostato sulla mappa o su un elemento.|
| `mouseup`           | Generato quando un dispositivo di puntamento viene rilasciato all'interno della mappa o quando si trova sopra un elemento.|
| `move`              | Generato ripetutamente durante una transizione animata da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `moveend`           | Generato immediatamente dopo che la mappa completa una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `movestart`         | Generato immediatamente prima che la mappa inizi una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `open`              | Generato quando il popup viene aperto manualmente o a livello.|
| `pitch`             | Generato ogni volta che il passo della mappa (inclinazione) cambia in seguito all'interazione o ai metodi dell'utente.|
| `pitchend`          | Generato immediatamente dopo che il passo della mappa (Tilt) viene modificato come risultato dell'interazione utente o dei metodi.|
| `pitchstart`        | Generato ogni volta che il passo della mappa (Tilt) inizia una modifica come risultato dell'interazione utente o dei metodi.|
| `ready`             | Generato quando vengono caricate le risorse minime obbligatorie della mappa prima che la mappa sia pronta per interagire a livello di codice.|
| `render`            | <p>Generato ogni volta che la mappa viene disegnata sullo schermo, come risultato di:<ul><li>Una modifica alla posizione, allo zoom, al passo o al cuscinetto della mappa.</li><li>Modifica dello stile della mappa.</li><li>Una modifica a un'origine `DataSource`.</li><li>Il caricamento di un riquadro vettoriale, di un file GeoJSON, di un glifo o di uno sprite.</li></ul></p>|
| `resize`            | Generato immediatamente dopo il ridimensionamento della mappa.|
| `rotate`            | Generato ripetutamente durante un'interazione "trascina per ruotare".|
| `rotateend`         | Generato quando termina l'interazione con il trascinamento della rotazione.|
| `rotatestart`       | Generato quando viene avviata l'interazione "trascina per ruotare".|
| `shapechanged`      | Generato quando viene modificata una proprietà dell'oggetto Shape.|
| `sourcedata`        | Generato quando una delle origini della mappa viene caricata o modificata, incluso se un riquadro appartenente a un'origine viene caricato o modificato. |
| `sourceadded`       | Generato quando un `DataSource` o un `VectorTileSource` viene aggiunto alla mappa.|
| `sourceremoved`     | Generato quando un `DataSource` o un `VectorTileSource` viene rimosso dalla mappa.|
| `styledata`         | Generato quando lo stile della mappa viene caricato o modificato.|
| `styleimagemissing` | Generato quando un livello tenta di caricare un'immagine dallo sprite dell'immagine che non esiste |
| `tokenacquired`     | Generato quando si ottiene un token di accesso di AAD.|
| `touchcancel`       | Generato quando si verifica un evento touchcancel all'interno della mappa.|
| `touchend`          | Generato quando si verifica un evento touchend all'interno della mappa.|
| `touchmove`         | Generato quando si verifica un evento TouchMove all'interno della mappa.|
| `touchstart`        | Generato quando si verifica un evento touchstart all'interno della mappa.|
| `wheel`             | Generato quando si verifica un evento della rotellina del mouse all'interno della mappa.|
| `zoom`              | Generato ripetutamente durante una transizione animata da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|
| `zoomend`           | Generato immediatamente dopo che la mappa completa una transizione da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|
| `zoomstart`         | Generato immediatamente prima che la mappa inizi una transizione da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|


## <a name="next-steps"></a>Passaggi successivi

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Uso del modulo servizi di mappe di Azure](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
