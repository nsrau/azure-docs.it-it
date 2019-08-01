---
title: Visualizzare le informazioni su una coordinata con Mappe di Azure | Microsoft Docs
description: Come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: df0966569a753d5000414451a2b69f1e69449b2c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638674"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come effettuare una ricerca di indirizzi inversa che visualizza l'indirizzo di una posizione quando si fa clic sul relativo elemento popup.

Esistono due modi per eseguire una ricerca di indirizzi inversa. Un modo consiste nell'eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usando un modulo del servizio. In alternativa, è possibile usare l' [API fetch](https://fetch.spec.whatwg.org/) per effettuare una richiesta all'API di [ricerca di indirizzi inversi di Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per trovare un indirizzo. Entrambi i modi sono esaminati di seguito.

## <a name="make-a-reverse-search-request-via-service-module"></a>Effettuare una richiesta di ricerca inversa con il modulo del servizio

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione per usare il token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un `TokenCredential` oggetto per autenticare le richieste HTTP per le mappe di Azure con il token di accesso. Passa quindi a `atlas.service.MapsURL.newPipeline()` e `TokenCredential` crea un'istanza della [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse su un puntatore e crea un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il quarto blocco di codice aggiunge un listener di [eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)click del mouse. Quando viene attivato, viene creata una query di ricerca con le coordinate del punto selezionato. USA quindi il metodo [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) del modulo del servizio per eseguire una query sull' [API Reverse Get search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate. Una raccolta di funzionalità GeoJSON dalla risposta viene quindi estratta usando `geojson.getFeatures()` il metodo.

Il quinto blocco di codice imposta il contenuto popup HTML per visualizzare l'indirizzo di risposta per la posizione della coordinata su cui è stato fatto clic.

La modifica del cursore, un oggetto popup, e l'evento click vengono creati tutti nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per assicurarsi che venga completato il caricamento della mappa prima di recuperare le informazioni delle coordinate.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Eseguire una richiesta di ricerca inversa tramite l'API fetch

Fare clic sulla mappa per effettuare una richiesta di geocodifica inversa per tale percorso utilizzando fetch.

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione per usare il token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore e un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il terzo blocco di codice aggiunge un listener di eventi per i clic del mouse. Quando si fa clic con il mouse, viene usata l' [API fetch](https://fetch.spec.whatwg.org/) per eseguire una query sull' [API di ricerca dell'indirizzo inverso di Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate selezionate. Per una risposta corretta, raccoglie l'indirizzo della località selezionata e definisce il contenuto del popup e la posizione tramite la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe Popup.

La modifica del cursore, un oggetto popup, e l'evento click vengono creati tutti nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per assicurarsi che venga completato il caricamento della mappa prima di recuperare le informazioni delle coordinate.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)

> [!div class="nextstepaction"]
> [Visualizzare il traffico](./map-show-traffic.md)
