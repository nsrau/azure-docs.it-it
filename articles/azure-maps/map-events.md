---
title: Gestire gli eventi della mappa Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come creare una mappa interattiva di Web SDK con eventi mappa tramite Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534899"
---
# <a name="interact-with-the-map"></a>Interazione con la mappa

In questo articolo viene illustrato come utilizzare la [classe di eventi map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). La proprietà evidenzia gli eventi sulla mappa e su diversi layer della mappa. È inoltre possibile evidenziare gli eventi quando si interagisce con un marcatore HTML.

## <a name="interact-with-the-map"></a>Interazione con la mappa

Gioca con la mappa qui sotto e vedi gli eventi del mouse corrispondenti evidenziati a destra. È possibile fare clic sulla **scheda JS** per visualizzare e modificare il codice JavaScript. È anche possibile fare clic su **Modifica su CodePen** per modificare il codice su CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - eventi del mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interact with the map - mouse events</a> (Interagire con la mappa - eventi del mouse) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagire con i livelli mappa

Il codice seguente evidenzia l'evento generato durante l'interazione con il layer di simboli. Il layer simbolo, bolla, linea e poligono supporta tutti lo stesso set di eventi. La mappa termica e i layer di piastrelle non supportano nessuno di questi eventi.

<br/>

<iframe height='600' scrolling='no' title='Interazione con la mappa - Eventi del livello' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interazione con la mappa - eventi del livello</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagire con il marcatore di HTML

Il codice seguente aggiunge eventi di mappa Javascript a un marcatore HTML. Inoltre evidenzia il nome degli eventi che si attivano durante l'interazione con il marcatore di HTML.

<br/>

<iframe height='500' scrolling='no' title='Interazione con la mappa - Eventi del marcatore di HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interazione con la mappa - eventi del marcatore di HTML</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nella tabella seguente sono elencati tutti gli eventi della classe mappa supportati.

| Event               | Descrizione |
|---------------------|-------------|
| `boxzoomend`        | Generato quando termina un'interazione di "zoom scatola".|
| `boxzoomstart`      | Generato quando inizia un'interazione di "zoom scatola".|
| `click`             | Generato quando un dispositivo di puntamento viene premuto e rilasciato nello stesso punto della mappa.|
| `close`             | Generato quando il popup viene chiuso manualmente o programmaticamente.|
| `contextmenu`       | Generato quando si fa clic sul pulsante destro del mouse.|
| `data`              | Generato quando i dati della mappa vengono caricati o modificati. |
| `dataadded`         | Generato quando le forme `DataSource`vengono aggiunte al file .|
| `dataremoved`       | Generato quando le forme `DataSource`vengono rimosse dal file .|
| `datasourceupdated` | Generato quando `DataSource` l'oggetto viene aggiornato.|
| `dblclick`          | Generato quando si fa clic due volte su un dispositivo di puntamento nello stesso punto della mappa.|
| `drag`              | Generato ripetutamente durante un'interazione di "trascinamento per eseguire la panoramica" sulla mappa, popup o marcatore HTML.|
| `dragend`           | Generato quando un'interazione "trascina per eseguire la panoramica" termina sulla mappa, sul popup o sul marcatore HTML.|
| `dragstart`         | Generato quando un'interazione di "trascinamento per eseguire la panoramica" inizia sulla mappa, popup o marcatore HTML.|
| `error`             | Generato quando si verifica un errore.|
| `idle`              | <p>Generato dopo l'ultimo fotogramma sottoposto a rendering prima che la mappa entri in uno stato "inattivo":<ul><li>Non sono in corso transizioni della fotocamera.</li><li>Tutti i riquadri attualmente richiesti sono stati caricati.</li><li>Tutte le animazioni di dissolvenza/transizione sono state completate.</li></ul></p>|
| `keydown`           | Generato quando si preme un tasto.|
| `keypress`          | Generato quando viene premuto un tasto che produce un carattere caratterizzabile (un tasto ANSI).|
| `keyup`             | Generato quando viene rilasciato un tasto.|
| `layeradded`        | Generato quando un layer viene aggiunto alla mappa.|
| `layerremoved`      | Generato quando un layer viene rimosso dalla mappa.|
| `load`              | Generato immediatamente dopo il download di tutte le risorse necessarie e il primo rendering visivamente completo della mappa.|
| `mousedown`         | Generato quando un dispositivo di puntamento viene premuto all'interno della mappa o quando si trova sopra un elemento.|
| `mouseenter`        | Generato quando un dispositivo di puntamento viene inizialmente spostato sulla mappa o su un elemento. |
| `mouseleave`        | Generato quando un dispositivo di puntamento viene spostato dalla mappa o da un elemento. |
| `mousemove`         | Generato quando un dispositivo di puntamento viene spostato all'interno della mappa o di un elemento.|
| `mouseout`          | Generato quando un dispositivo punto lascia la tela della mappa il nostro lascia un elemento.|
| `mouseover`         | Generato quando un dispositivo di puntamento viene spostato sulla mappa o su un elemento.|
| `mouseup`           | Generato quando un dispositivo di puntamento viene rilasciato all'interno della mappa o quando si trova sopra un elemento.|
| `move`              | Generato ripetutamente durante una transizione animata da una visualizzazione a un'altra, come risultato dell'interazione dell'utente o dei metodi.|
| `moveend`           | Generato subito dopo il completamento di una transizione da una visualizzazione a un'altra, come risultato dell'interazione dell'utente o dei metodi.|
| `movestart`         | Generato appena prima che la mappa inizi una transizione da una visualizzazione all'altra, come risultato dell'interazione dell'utente o dei metodi.|
| `open`              | Generato quando il popup viene aperto manualmente o programmaticamente.|
| `pitch`             | Generato ogni volta che il passo (inclinazione) della mappa cambia come risultato dell'interazione dell'utente o dei metodi.|
| `pitchend`          | Generato immediatamente dopo che il passo (inclinazione) della mappa termina la modifica come risultato dell'interazione dell'utente o dei metodi.|
| `pitchstart`        | Generato ogni volta che il passo (inclinazione) della mappa inizia una modifica come risultato dell'interazione dell'utente o dei metodi.|
| `ready`             | Generato quando vengono caricate le risorse mappa minime necessarie prima che la mappa sia pronta per l'interazione a livello di codice.|
| `render`            | <p>Generato ogni volta che la mappa viene disegnata sullo schermo, come risultato di:<ul><li>Modifica della posizione, dello zoom, dell'altezza o del cuscinetto della mappa.</li><li>Modifica dello stile della mappa.</li><li>Una modifica `DataSource` a un'origine.</li><li>Caricamento di un riquadro vettoriale, di un file GeoJSON, di un glifo o di uno sprite.</li></ul></p>|
| `resize`            | Generato immediatamente dopo il redimensionato della mappa.|
| `rotate`            | Generato ripetutamente durante un'interazione di "trascina per ruotare".|
| `rotateend`         | Generato quando un'interazione "trascina per ruotare" termina.|
| `rotatestart`       | Generato quando inizia un'interazione "trascina per ruotare".|
| `shapechanged`      | Generato quando viene modificata una proprietà dell'oggetto forma.|
| `sourcedata`        | Generato quando una delle origini della mappa viene caricata o modificata, anche se un riquadro appartenente a un'origine viene caricato o modificato. |
| `sourceadded`       | Generato quando `DataSource` `VectorTileSource` un oggetto o viene aggiunto alla mappa.|
| `sourceremoved`     | Generato quando `DataSource` `VectorTileSource` un o viene rimosso dalla mappa.|
| `styledata`         | Generato quando lo stile della mappa viene caricato o modificato.|
| `styleimagemissing` | Generato quando un livello tenta di caricare un'immagine dallo sprite dell'immagine che non esiste |
| `tokenacquired`     | Generato quando viene ottenuto un token di accesso AAD.|
| `touchcancel`       | Generato quando si verifica un evento touchcancel all'interno della mappa.|
| `touchend`          | Generato quando si verifica un evento touchend all'interno della mappa.|
| `touchmove`         | Generato quando si verifica un evento touchmove all'interno della mappa.|
| `touchstart`        | Generato quando si verifica un evento touchstart all'interno della mappa.|
| `wheel`             | Generato quando si verifica un evento della rotellina del mouse all'interno della mappa.|
| `zoom`              | Generato ripetutamente durante una transizione animata da un livello di zoom a un altro, come risultato dell'interazione dell'utente o dei metodi.|
| `zoomend`           | Generato subito dopo che la mappa completa una transizione da un livello di zoom a un altro, come risultato dell'interazione dell'utente o dei metodi.|
| `zoomstart`         | Generato appena prima che la mappa inizi una transizione da un livello di zoom a un altro, come risultato dell'interazione dell'utente o dei metodi.|


## <a name="next-steps"></a>Passaggi successivi

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Uso del modulo Servizi mappe di AzureUsing the Azure Maps Services module](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
