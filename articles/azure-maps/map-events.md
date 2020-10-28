---
title: Gestire gli eventi della mappa | Mappe di Microsoft Azure
description: Informazioni sugli eventi che vengono generati quando gli utenti interagiscono con maps. Visualizzare un elenco di tutti gli eventi di mapping supportati. Vedere come usare Azure Maps Web SDK per gestire gli eventi.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8121ceb68bdea9332316a9508bf6f4731e05b0ba
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890786"
---
# <a name="interact-with-the-map"></a>Interazione con la mappa

Questo articolo illustra come usare la [classe degli eventi della mappa](/javascript/api/azure-maps-control/atlas.map#events). La proprietà consente di evidenziare gli eventi nella mappa e nei diversi livelli della mappa. È anche possibile evidenziare gli eventi quando si interagisce con un marcatore HTML.

## <a name="interact-with-the-map"></a>Interazione con la mappa

Provare a usare la mappa sottostante e osservare gli eventi del mouse corrispondenti evidenziati sul lato destro. È possibile fare clic sulla **scheda JS** per visualizzare e modificare il codice JavaScript. È anche possibile fare clic su **Edit in CodePen** per modificare il codice in CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - eventi del mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map - mouse events</a> (Interagire con la mappa - eventi del mouse) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagire con i livelli mappa

Nel codice seguente viene evidenziato l'evento generato quando si interagisce con il livello dei simboli. I livelli di simbolo, bolla, linea e poligono supportano lo stesso set di eventi. La mappa termica e i livelli delle tessere non supportano nessuno di questi eventi.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - Eventi del livello' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interazione con la mappa - eventi del livello</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagire con il marcatore di HTML

Il codice seguente aggiunge gli eventi della mappa JavaScript a un marcatore HTML. Inoltre evidenzia il nome degli eventi che si attivano durante l'interazione con il marcatore di HTML.

<br/>

<iframe height='500' scrolling='no' title='Interazione con la mappa - Eventi del marcatore di HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interazione con la mappa - eventi del marcatore di HTML</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nella tabella seguente sono elencati tutti gli eventi della classe Map supportati.

| Event               | Descrizione |
|---------------------|-------------|
| `boxzoomend`        | Generato al termine di un'interazione con lo zoom della finestra.|
| `boxzoomstart`      | Generato all'avvio di un'interazione con lo zoom di una finestra.|
| `click`             | Generato quando un dispositivo di puntamento viene premuto e rilasciato nello stesso punto della mappa.|
| `close`             | Generato quando il popup viene chiuso manualmente o a livello di programmazione.|
| `contextmenu`       | Generato quando si fa clic sul pulsante destro del mouse.|
| `data`              | Generato quando vengono caricati o modificati dati della mappa. |
| `dataadded`         | Generato quando vengono aggiunte forme a `DataSource`.|
| `dataremoved`       | Generato quando vengono rimosse forme da `DataSource`.|
| `datasourceupdated` | Generato quando viene aggiornato l'oggetto `DataSource`.|
| `dblclick`          | Generato quando si fa doppio clic su un dispositivo di puntamento nello stesso punto sulla mappa.|
| `drag`              | Generato ripetutamente durante un'interazione di "trascinamento per una panoramica" sulla mappa, il popup o il marcatore HTML.|
| `dragend`           | Generato quando un'interazione di "trascinamento per una panoramica" termina sulla mappa, il popup o il marcatore HTML.|
| `dragstart`         | Generato quando un'interazione di "trascinamento per una panoramica" inizia sulla mappa, il popup o il marcatore HTML.|
| `error`             | Generato quando si verifica un errore.|
| `idle`              | <p>Generato dopo il rendering dell'ultimo frame prima che la mappa passi in stato "inattivo" in questi casi:<ul><li>Non sono in corso transizioni della fotocamera.</li><li>Sono stati caricati tutte le tessere attualmente richieste.</li><li>Sono state completate tutte le animazioni di dissolvenza/transizione.</li></ul></p>|
| `keydown`           | Generato quando viene premuto un tasto.|
| `keypress`          | Generato quando viene premuto un tasto che produce un carattere tipizzabile (una chiave ANSI).|
| `keyup`             | Generato quando viene rilasciato un tasto.|
| `layeradded`        | Generato quando un livello viene aggiunto alla mappa.|
| `layerremoved`      | Generato quando un livello viene rimosso dalla mappa.|
| `load`              | Generato immediatamente dopo il download di tutte le risorse necessarie e il primo rendering visivo completo della mappa.|
| `mousedown`         | Generato quando un dispositivo di puntamento viene premuto all'interno della mappa o quando si trova sopra un elemento.|
| `mouseenter`        | Generato quando un dispositivo di puntamento viene spostato inizialmente sulla mappa o su un elemento. |
| `mouseleave`        | Generato quando un dispositivo di puntamento viene spostato fuori dalla mappa o da un elemento. |
| `mousemove`         | Generato quando un dispositivo di puntamento viene spostato all'interno della mappa o di un elemento.|
| `mouseout`          | Generato quando un dispositivo di puntamento lascia l'area canvas della mappa o un elemento.|
| `mouseover`         | Generato quando un dispositivo di puntamento viene spostato sulla mappa o su un elemento.|
| `mouseup`           | Generato quando un dispositivo di puntamento viene rilasciato all'interno della mappa o quando si trova sopra un elemento.|
| `move`              | Generato ripetutamente durante una transizione animata da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `moveend`           | Generato immediatamente dopo che la mappa completa una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `movestart`         | Generato immediatamente prima che la mappa inizi una transizione da una vista a un'altra, come risultato dell'interazione utente o dei metodi.|
| `open`              | Generato quando il popup viene aperto manualmente o a livello di programmazione.|
| `pitch`             | Generato ogni volta che l'inclinazione della mappa viene modificata in seguito all'interazione o ai metodi dell'utente.|
| `pitchend`          | Generato immediatamente dopo che l'inclinazione della mappa completa una modifica come risultato dell'interazione utente o dei metodi.|
| `pitchstart`        | Generato ogni volta che l'inclinazione della mappa avvia una modifica come risultato dell'interazione utente o dei metodi.|
| `ready`             | Generato quando vengono caricate le risorse minime obbligatorie della mappa prima che la mappa sia pronta per interagire a livello di codice.|
| `render`            | <p>Generato ogni volta che la mappa viene disegnata sullo schermo come risultato di:<ul><li>Una modifica a posizione, zoom, orientamento o inclinazione della mappa.</li><li>Una modifica allo stile della mappa.</li><li>Una modifica a un'origine `DataSource`.</li><li>Il caricamento di une tessera vettoriale, di un file GeoJSON, di un glifo o di uno sprite.</li></ul></p>|
| `resize`            | Generato immediatamente dopo il ridimensionamento della mappa.|
| `rotate`            | Generato ripetutamente durante un'interazione di "trascinamento per la rotazione".|
| `rotateend`         | Generato quando termina l'interazione un'interazione di "trascinamento per la rotazione".|
| `rotatestart`       | Generato quando inizia l'interazione di "trascinamento per la rotazione".|
| `shapechanged`      | Generato quando viene modificata una proprietà dell'oggetto Shape.|
| `sourcedata`        | Generato quando una delle origini della mappa viene caricata o modificata, incluso se una tessera appartenente a un'origine viene caricata o modificata. |
| `sourceadded`       | Generato quando viene aggiunto alla mappa un oggetto `DataSource` o `VectorTileSource`.|
| `sourceremoved`     | Generato quando viene rimosso dalla mappa un oggetto `DataSource` o `VectorTileSource`.|
| `styledata`         | Generato quando lo stile della mappa viene caricato o modificato.|
| `styleimagemissing` | Generato quando un livello tenta di caricare un'immagine dallo sprite dell'immagine che non esiste |
| `tokenacquired`     | Generato quando si ottiene un token di accesso Azure Active Directory.|
| `touchcancel`       | Generato quando `touchcancel` si verifica un evento all'interno della mappa.|
| `touchend`          | Generato quando `touchend` si verifica un evento all'interno della mappa.|
| `touchmove`         | Generato quando `touchmove` si verifica un evento all'interno della mappa.|
| `touchstart`        | Generato quando `touchstart` si verifica un evento all'interno della mappa.|
| `wheel`             | Generato quando si verifica un evento della rotellina del mouse all'interno della mappa.|
| `zoom`              | Generato ripetutamente durante una transizione animata da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|
| `zoomend`           | Generato immediatamente dopo che la mappa completa una transizione da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|
| `zoomstart`         | Generato immediatamente prima che la mappa inizi una transizione da un livello di zoom a un altro, come risultato dell'interazione utente o dei metodi.|


## <a name="next-steps"></a>Passaggi successivi

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Utilizzo del modulo dei servizi di Mappe di Azure](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Esempi di codice](/samples/browse/?products=azure-maps)