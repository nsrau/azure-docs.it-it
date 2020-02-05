---
title: Creare una mappa con mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come eseguire il rendering di una mappa in una pagina Web usando il Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988584"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa e aggiungere un'animazione a una mappa.  

## <a name="loading-a-map"></a>Caricamento di una mappa

Per caricare una mappa, creare una nuova istanza della [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Quando si inizializza la mappa, passare un ID elemento DIV per eseguire il rendering della mappa e passare un set di opzioni da usare durante il caricamento della mappa. Se le informazioni di autenticazione predefinite non sono specificate nello spazio dei nomi `atlas`, è necessario specificare queste informazioni nelle opzioni della mappa quando si carica la mappa. La mappa carica diverse risorse in modo asincrono per le prestazioni. Di conseguenza, dopo aver creato l'istanza della mappa, associare un evento `ready` o `load` alla mappa, quindi aggiungere il codice aggiuntivo che interagisce con la mappa al gestore eventi. L'evento `ready` viene attivato non appena la mappa dispone di un numero sufficiente di risorse caricate per interagire a livello di codice. L'evento `load` viene generato al termine del caricamento completo della visualizzazione mappa iniziale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Caricamento mappa di base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>caricamento della mappa di base</a> di Pen da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> È possibile caricare più mappe nella stessa pagina. Più mappe nella stessa pagina possono utilizzare le stesse impostazioni di autenticazione e lingua.

## <a name="show-a-single-copy-of-the-world"></a>Mostra un'unica copia del mondo

Quando si esegue lo zoom avanti della mappa in un ampio schermo, più copie del mondo verranno visualizzate orizzontalmente. Questa opzione è ideale per alcuni scenari, ma per altre applicazioni è auspicabile visualizzare una singola copia del mondo. Questo comportamento viene implementato impostando l'opzione Maps `renderWorldCopies` su `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Controllo della fotocamera della mappa

Esistono due modi per impostare l'area visualizzata della mappa utilizzando la fotocamera di una mappa. È possibile impostare le opzioni della fotocamera durante il caricamento della mappa. In alternativa, è possibile chiamare l'opzione `setCamera` in qualsiasi momento dopo il caricamento della mappa per aggiornare a livello di codice la vista mappa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Impostare la fotocamera

Nel codice seguente viene creato un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) e sono impostate le opzioni Center e zoom. Le proprietà della mappa, ad esempio il livello Center e il livello di zoom, fanno parte del [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa alla <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>creazione di una mappa tramite `CameraOptions` </a>da servizi location based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Impostare i limiti della fotocamera

Nel codice seguente viene creato un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tramite `new atlas.Map()`. Le proprietà della mappa, ad esempio `CameraBoundsOptions`, possono essere definite tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della classe Map. I limiti e le proprietà relative alla spaziatura interna vengono impostati tramite `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere la pagina relativa alla <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>creazione di una mappa tramite `CameraBoundsOptions` </a>di mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Aggiungere un'animazione alla visualizzazione della mappa

Nel codice seguente il primo blocco di codice crea una mappa e imposta gli stili della mappa di immissione e zoom. Nel secondo blocco di codice viene creato un gestore dell'evento click per il pulsante animate. Quando si fa clic su questo pulsante, la funzione `setCamera` viene chiamata con alcuni valori casuali per [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un'animazione alla visualizzazione della mappa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a> (Aggiungi animazione a visualizzazione mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Provare il codice

Esaminare gli esempi di codice. È possibile modificare il codice JavaScript all'interno della **scheda JS** e visualizzare le modifiche alla visualizzazione mappa nella **scheda risultato**. È anche possibile fare clic **su modifica in CodePen**, nell'angolo in alto a destra e modificare il codice in CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Vedere gli esempi di codice per aggiungere funzionalità all'app:

> [!div class="nextstepaction"]
> [Modificare lo stile della mappa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Aggiungere controlli alla mappa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Esempi di codice](https://docs.microsoft.com/samples/browse/?products=azure-maps)
