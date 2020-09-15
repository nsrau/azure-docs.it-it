---
title: Visualizzare le informazioni su una coordinata in una mappa | Mappe di Microsoft Azure
description: Informazioni su come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 0026b3714be4cf0a59d348f6708a3840aaddf11b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90085332"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come effettuare una ricerca di indirizzi inversa che visualizza l'indirizzo di una posizione quando si fa clic sul relativo elemento popup.

Esistono due modi per eseguire una ricerca di indirizzi inversa. Un modo consiste nell'eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usando un modulo del servizio. L'altro consiste nell'usare l'[API di recupero](https://fetch.spec.whatwg.org/) per effettuare una richiesta all'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per trovare un indirizzo. Entrambi i modi sono esaminati di seguito.

## <a name="make-a-reverse-search-request-via-service-module"></a>Effettuare una richiesta di ricerca inversa con il modulo del servizio

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco costruisce un oggetto mappa e imposta il meccanismo di autenticazione per l'uso del token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto `TokenCredential` per autenticare le richieste HTTP a Mappe di Azure con il token di accesso. Passa quindi `TokenCredential` a `atlas.service.MapsURL.newPipeline()` e crea un'istanza di [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline). `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore e crea un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il quarto blocco di codice aggiunge un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) per un clic del mouse. Quando viene attivato, crea una query di ricerca con le coordinate del punto selezionato. Usa quindi il metodo [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) per eseguire una query sull'[API GET di ricerca di indirizzi inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate. Dalla risposta viene quindi estratta una raccolta di funzionalità GeoJSON con il metodo `geojson.getFeatures()`.

Il quinto blocco di codice imposta il contenuto popup HTML per visualizzare l'indirizzo di risposta per la posizione della coordinata selezionata.

La modifica del cursore, l'oggetto popup e l'evento Click vengono tutti creati nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) della mappa. Questa struttura del codice assicura il caricamento completo della mappa prima del recupero delle informazioni sulle coordinate.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Effettuare una richiesta di ricerca inversa con l'API di recupero

Fare clic sulla mappa per effettuare una richiesta di codifica geografica inversa per tale percorso usando il recupero.

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa e imposta il meccanismo di autenticazione per l'uso del token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore. Crea un'istanza di un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il terzo blocco di codice aggiunge un listener di eventi per i clic del mouse. Dopo un clic del mouse, usa l'[API di recupero](https://fetch.spec.whatwg.org/) per eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate selezionato. Per una risposta con esito positivo, raccoglie l'indirizzo per la posizione selezionata. Definisce la posizione e il contenuto popup usando la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup#setoptions-popupoptions-) della classe popup.

La modifica del cursore, l'oggetto popup e l'evento Click vengono tutti creati nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) della mappa. Questa struttura del codice assicura il caricamento completo della mappa prima del recupero delle informazioni sulle coordinate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di ricerca](how-to-use-best-practices-for-search.md)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)

> [!div class="nextstepaction"]
> [Visualizzare il traffico](./map-show-traffic.md)
