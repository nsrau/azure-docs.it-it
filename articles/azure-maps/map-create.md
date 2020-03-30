---
title: Creare una mappa con Mappe di Azure. Mappe di Microsoft Azure
description: In questo articolo verrà illustrato come eseguire il rendering di una mappa in una pagina Web usando Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534918"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa e aggiungere un'animazione a una mappa.  

## <a name="loading-a-map"></a>Caricamento di una mappa

Per caricare una mappa, creare una nuova istanza della [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Durante l'inizializzazione della mappa, passare un ID elemento DIV per eseguire il rendering della mappa e passare un set di opzioni da utilizzare durante il caricamento della mappa. Se le informazioni di autenticazione `atlas` predefinite non sono specificate nello spazio dei nomi, queste informazioni dovranno essere specificate nelle opzioni della mappa durante il caricamento della mappa. La mappa carica diverse risorse in modo asincrono per ottenere prestazioni. Di conseguenza, dopo aver creato `ready` `load` l'istanza della mappa, associare un evento o alla mappa e quindi aggiungere altro codice che interagisce con la mappa al gestore eventi. L'evento `ready` viene generato non appena la mappa dispone di risorse sufficienti per interagire con il codice a livello di codice. L'evento `load` viene generato al termine del caricamento completo della vista mappa iniziale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carico mappa di base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la mappa di Base<a href='https://codepen.io/azuremaps'>@azuremaps</a>penna <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>caricata</a> da Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> È possibile caricare più mappe nella stessa pagina. Più mappe nella stessa pagina possono utilizzare le stesse impostazioni di autenticazione e lingua o diverse.

## <a name="show-a-single-copy-of-the-world"></a>Mostra una singola copia del mondo

Quando la mappa viene ingrandita su un ampio schermo, più copie del mondo appariranno orizzontalmente. Questa opzione è ideale per alcuni scenari, ma per altre applicazioni è opportuno visualizzare una singola copia del mondo. Questo comportamento viene implementato `renderWorldCopies` impostando `false`l'opzione maps su .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies - false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies - false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>di Azure Maps ( ) su <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opzioni mappa

Quando si crea una mappa, sono diversi tipi di opzioni che possono essere passate per personalizzare il funzionamento della mappa come elencato di seguito.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) e [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) vengono utilizzati per specificare l'area che la mappa deve visualizzare.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) vengono utilizzati per specificare come la mappa deve interagire con i servizi che alimentano la mappa.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) vengono utilizzati per specificare la mappa deve essere stile e il rendering.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) vengono utilizzati per specificare come la mappa deve raggiungere quando l'utente interagisce con la mappa. 

Queste opzioni possono essere aggiornate anche dopo `setCamera`il `setServiceOptions` `setStyle`caricamento `setUserInteraction` della mappa utilizzando le funzioni , , e . 

## <a name="controlling-the-map-camera"></a>Controllo della telecamera mappa

Esistono due modi per impostare l'area visualizzata della mappa utilizzando la fotocamera di una mappa. È possibile impostare le opzioni della fotocamera durante il caricamento della mappa. In alternativa, è `setCamera` possibile chiamare l'opzione in qualsiasi momento dopo il caricamento della mappa per aggiornare la visualizzazione mappa a livello di codice.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Impostare la fotocamera

La videocamera della mappa controlla ciò che viene visualizzato nel riquadro di visualizzazione dell'area di disegno della mappa. Le opzioni della fotocamera possono essere passate nelle opzioni `setCamera` della mappa quando vengono inizializzate o passate nella funzione maps.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

Nel codice seguente viene creato un [oggetto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) e vengono impostate le opzioni center e zoom. Le proprietà della mappa, ad esempio il centro e il livello di zoom, fanno parte di [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via (Creare una mappa con) `CameraOptions` </a>di Servizi Location Based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Impostare i limiti della fotocamera

Un riquadro di delimitazione può essere utilizzato per aggiornare la videocamera della mappa. Se il riquadro di delimitazione è stato calcolato dai dati punto, è spesso utile specificare anche un valore di spaziatura interna pixel nelle opzioni della fotocamera per tenere conto delle dimensioni dell'icona. Ciò contribuirà a garantire che i punti non cadano dal bordo della finestra della mappa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

Nel codice seguente, un [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) `new atlas.Map()`oggetto viene costruito tramite . Le proprietà della mappa, ad esempio `CameraBoundsOptions`, possono essere definite tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) della classe Map. I limiti e le proprietà relative alla spaziatura interna vengono impostati tramite `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via (Creare una mappa con) `CameraBoundsOptions` </a>di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Aggiungere un'animazione alla visualizzazione della mappa

Quando si impostano le opzioni della videocamera della mappa, è possibile impostare anche le opzioni di [animazione.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) Queste opzioni specificano il tipo di animazione e la durata necessario per spostare la videocamera.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

Nel codice seguente, il primo blocco di codice crea una mappa e imposta gli stili della mappa di immissione e zoom. Nel secondo blocco di codice viene creato un gestore eventi click per il pulsante di animazione. Quando si fa clic `setCamera` su questo pulsante, la funzione viene chiamata con alcuni valori casuali per [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un'animazione alla visualizzazione della mappa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a> (Aggiungi animazione a visualizzazione mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Provare il codice

Esaminare gli esempi di codice. È possibile modificare il codice JavaScript all'interno della **scheda JS** e visualizzare le modifiche apportate alla visualizzazione mappa nella **scheda Risultato**. È inoltre possibile fare clic su **Modifica su CodePen**, nell'angolo superiore destro e modificare il codice in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opzioni della fotocamera](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOpzioni](/javascript/api/azure-maps-control/atlas.animationoptions)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Cambiare lo stile della mappa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Aggiungere controlli alla mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
