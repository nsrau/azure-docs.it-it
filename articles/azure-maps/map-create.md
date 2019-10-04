---
title: Creare una mappa con Mappe di Azure | Microsoft Docs
description: Come creare una mappa con Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976127"
---
# <a name="create-a-map"></a>Creare una mappa

Questo articolo illustra come creare una mappa e aggiungere un'animazione a una mappa.  

## <a name="loading-a-map"></a>Caricamento di una mappa

Per caricare una mappa, creare una nuova istanza della [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Quando si inizializza la mappa, viene passato un ID elemento DIV per il rendering della mappa e un set di opzioni da usare durante il caricamento della mappa. Se lo `atlas` spazio dei nomi non specifica le informazioni di autenticazione predefinite, è necessario specificare queste informazioni nelle opzioni della mappa quando si carica la mappa. La mappa carica diverse risorse in modo asincrono per le prestazioni. Di conseguenza, dopo aver creato l'istanza della mappa, `ready` alleghi un evento o `load` alla mappa e quindi Aggiungi il codice aggiuntivo che interagisce con la mappa in quel gestore eventi. L' `ready` evento viene generato non appena la mappa dispone di risorse sufficienti caricate per interagire a livello di codice. L' `load` evento viene generato al termine del caricamento completo della visualizzazione mappa iniziale. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Caricamento mappa di base" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>caricamento della mappa di base</a> di Pen da<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () in <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> È possibile caricare più mappe nella stessa pagina e ognuna di esse può usare le stesse impostazioni di autenticazione e lingua diverse.

## <a name="show-a-single-copy-of-the-world"></a>Mostra un'unica copia del mondo

Quando si esegue lo zoom avanti della mappa in un ampio schermo, più copie del mondo verranno visualizzate orizzontalmente. Questo è ideale per la maggior parte degli scenari, ma per alcune applicazioni potrebbe essere auspicabile visualizzare una sola copia del mondo. Questa operazione può essere eseguita impostando l' `renderWorldCopies` opzione Maps `false`su.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Controllo della fotocamera della mappa

È possibile impostare l'area visualizzata della mappa utilizzando la fotocamera in due modi. È possibile impostare le opzioni della fotocamera, ad esempio centra e zoom, durante il caricamento della mappa oppure `setCamera` chiamare l'opzione in qualsiasi momento dopo il caricamento della mappa per aggiornare la vista mappa a livello di codice.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Impostare la fotocamera

Nel codice seguente viene creato un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) e sono impostate le opzioni Center e zoom. Le proprietà della mappa, ad esempio il centro e il livello di zoom, fanno parte del [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Create a map via (Creare una mappa con) `CameraOptions` </a>di Servizi Location Based di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Impostare i limiti della fotocamera

Nel codice seguente, un [oggetto map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) viene costruito tramite `new atlas.Map()`. Le proprietà della mappa, ad esempio `CameraBoundsOptions`, possono essere definite tramite la funzione [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) della classe Map. I limiti e le proprietà relative alla spaziatura interna vengono impostati tramite `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Creare una mappa tramite CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Create a map via (Creare una mappa con) `CameraBoundsOptions` </a>di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Aggiungere un animazione alla visualizzazione della mappa

Nel codice seguente il primo blocco di codice crea una mappa e imposta i valori di stile, centra e zoom della mappa. Nel secondo blocco di codice viene creato un gestore dell'evento click per il pulsante animate. Quando si fa clic su questo pulsante, viene chiamata la funzione secamera con alcuni valori casuali per [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Aggiungere un animazione alla visualizzazione della mappa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Provare il codice

Esaminare il codice di esempio precedente. È possibile modificare il codice JavaScript nella **scheda JS** a sinistra e visualizzare le modifiche apportate alla visualizzazione della mappa nella **scheda Result** (Risultato) a destra. È anche possibile fare clic sul pulsante **Edit on CodePen** (Modifica in CodePen) e modificare il codice in CodePen.

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