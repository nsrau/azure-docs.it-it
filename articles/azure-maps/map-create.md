---
title: Creare una mappa con mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come eseguire il rendering di una mappa in una pagina Web usando il Microsoft Azure Maps Web SDK.
author: Philmea
ms.author: philmea
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97eb1ebb61e5ff78ed918fded8107f5775b533c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83124024"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa e aggiungere un'animazione a una mappa.  

## <a name="loading-a-map"></a>Caricamento di una mappa

Per caricare una mappa, creare una nuova istanza della [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Quando si inizializza la mappa, passare un ID elemento DIV per eseguire il rendering della mappa e passare un set di opzioni da usare durante il caricamento della mappa. Se lo spazio dei nomi non specifica le informazioni di autenticazione predefinite `atlas` , è necessario specificare queste informazioni nelle opzioni della mappa quando si carica la mappa. La mappa carica diverse risorse in modo asincrono per le prestazioni. Di conseguenza, dopo aver creato l'istanza della mappa, associare un `ready` `load` evento o alla mappa e quindi aggiungere il codice aggiuntivo che interagisce con la mappa al gestore eventi. L' `ready` evento viene generato non appena la mappa dispone di risorse sufficienti caricate per interagire a livello di codice. L' `load` evento viene generato al termine del caricamento completo della visualizzazione mappa iniziale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Caricamento mappa di base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>caricamento della mappa di base</a> di Pen da Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> È possibile caricare più mappe nella stessa pagina. Più mappe nella stessa pagina possono utilizzare le stesse impostazioni di autenticazione e lingua.

## <a name="show-a-single-copy-of-the-world"></a>Mostra un'unica copia del mondo

Quando si esegue lo zoom avanti della mappa in un ampio schermo, più copie del mondo verranno visualizzate orizzontalmente. Questa opzione è ideale per alcuni scenari, ma per altre applicazioni è auspicabile visualizzare una singola copia del mondo. Questo comportamento viene implementato impostando l' `renderWorldCopies` opzione Maps su `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> di Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opzioni mappa

Quando si crea una mappa, sono disponibili diversi tipi di opzioni che è possibile passare per personalizzare il modo in cui la mappa funziona come indicato di seguito.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) e [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) vengono usati per specificare l'area in cui deve essere visualizzata la mappa.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) vengono utilizzati per specificare il modo in cui la mappa deve interagire con i servizi che alimentano la mappa.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) vengono usati per specificare che la mappa deve essere con stile e sottoposta a rendering.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) vengono utilizzati per specificare il modo in cui la mappa deve raggiungere quando l'utente interagisce con la mappa. 

Queste opzioni possono essere aggiornate anche dopo che la mappa è stata caricata usando le `setCamera` funzioni,, `setServiceOptions` `setStyle` e `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>Controllo della fotocamera della mappa

Esistono due modi per impostare l'area visualizzata della mappa utilizzando la fotocamera di una mappa. È possibile impostare le opzioni della fotocamera durante il caricamento della mappa. In alternativa, è possibile chiamare l' `setCamera` opzione in qualsiasi momento dopo il caricamento della mappa per aggiornare a livello di codice la vista mappa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Impostare la fotocamera

La fotocamera mappa controlla gli elementi visualizzati nel viewport dell'area di disegno della mappa. Le opzioni della fotocamera possono essere passate nelle opzioni della mappa quando vengono inizializzate o passate nella `setCamera` funzione maps.

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

Nel codice seguente viene creato un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) e sono impostate le opzioni Center e zoom. Le proprietà della mappa, ad esempio il livello Center e il livello di zoom, fanno parte del [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via (Creare una mappa con) `CameraOptions` </a>di Servizi Location Based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Impostare i limiti della fotocamera

È possibile utilizzare un rettangolo di delimitazione per aggiornare la fotocamera della mappa. Se il rettangolo di delimitazione è stato calcolato dai dati del punto, è spesso utile specificare anche un valore di spaziatura in pixel nelle opzioni della fotocamera per tenere conto delle dimensioni dell'icona. Ciò consentirà di garantire che i punti non cadano sul bordo del viewport mappa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

Nel codice seguente, un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) viene costruito tramite `new atlas.Map()` . Le proprietà della mappa, ad esempio `CameraBoundsOptions`, possono essere definite tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) della classe Map. I limiti e le proprietà relative alla spaziatura interna vengono impostati tramite `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via (Creare una mappa con) `CameraBoundsOptions` </a>di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Aggiungere un'animazione alla visualizzazione della mappa

Quando si configurano le opzioni della fotocamera della mappa, è possibile impostare anche le [Opzioni di animazione](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) . Queste opzioni specificano il tipo di animazione e la durata da eseguire per spostare la fotocamera.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

Nel codice seguente il primo blocco di codice crea una mappa e imposta gli stili della mappa di immissione e zoom. Nel secondo blocco di codice viene creato un gestore dell'evento click per il pulsante animate. Quando si fa clic su questo pulsante, la `setCamera` funzione viene chiamata con alcuni valori casuali per [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un'animazione alla visualizzazione della mappa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a> (Aggiungi animazione a visualizzazione mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Provare il codice

Esaminare gli esempi di codice. È possibile modificare il codice JavaScript all'interno della **scheda JS** e visualizzare le modifiche alla visualizzazione mappa nella **scheda risultato**. È anche possibile fare clic **su modifica in CodePen**, nell'angolo in alto a destra e modificare il codice in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Cambiare lo stile della mappa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Aggiungere controlli alla mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
