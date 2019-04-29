---
title: Visualizzare le informazioni su una coordinata con Mappe di Azure | Microsoft Docs
description: Come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769125"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come effettuare una ricerca di indirizzi inversa che visualizza l'indirizzo di una posizione quando si fa clic sul relativo elemento popup.

Esistono due modi per eseguire una ricerca di indirizzi inversa. Un modo consiste nell'eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usando un modulo del servizio. L'altro modo consiste nell'utilizzare il [API Fetch](https://fetch.spec.whatwg.org/) per effettuare una richiesta per il [API ricerca di Azure esegue il mapping inverso indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) trovare un indirizzo. Entrambi i modi sono esaminati di seguito.

## <a name="make-a-reverse-search-request-via-service-module"></a>Effettuare una richiesta di ricerca inversa con il modulo del servizio

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione da usare la chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un `SubscriptionKeyCredentialPolicy` per autenticare le richieste HTTP per le mappe di Azure con la chiave di sottoscrizione. Il `atlas.service.MapsURL.newPipeline()` accetta il `SubscriptionKeyCredential` dei criteri e consente di creare un [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) istanza. `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse in un puntatore e crea un [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) oggetto. Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il quarto blocco di codice aggiunge un clic del mouse [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Quando viene attivato, crea una query di ricerca con le coordinate del punto selezionato. Usa quindi il modulo di servizio [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) metodo di query il [Get ricerca indirizzi inversa API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate. Una raccolta di funzionalità GeoJSON dalla risposta viene quindi estratto usando la `geojson.getFeatures()` (metodo).

Il quinto blocco di codice imposta il contenuto del popup HTML per visualizzare l'indirizzo di risposta per la posizione della coordinata selezionata.

La modifica del cursore, un oggetto popup, e l'evento click vengono creati tutti nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per assicurarsi che venga completato il caricamento della mappa prima di recuperare le informazioni delle coordinate.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Effettuare una richiesta di ricerca inversa tramite l'API Fetch

Fare clic sulla mappa per effettuare una richiesta di codifica geografica inversa per quel percorso usando l'operazione di recupero.

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione da usare la chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore e un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il terzo blocco di codice aggiunge un listener di eventi per i clic del mouse. Dopo un clic del mouse, utilizza il [API Fetch](https://fetch.spec.whatwg.org/) alla query il [API ricerca di Azure esegue il mapping inverso indirizzo](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo coordinate selezionato. Per una risposta corretta, raccoglie l'indirizzo della località selezionata e definisce il contenuto del popup e la posizione tramite la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe Popup.

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
