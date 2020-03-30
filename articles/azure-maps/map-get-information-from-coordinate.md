---
title: Mostrare informazioni su una coordinata su una mappa Mappe di Microsoft Azure
description: Informazioni su come visualizzare informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371428"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come effettuare una ricerca di indirizzi inversa che visualizza l'indirizzo di una posizione quando si fa clic sul relativo elemento popup.

Esistono due modi per eseguire una ricerca di indirizzi inversa. Un modo consiste nell'eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usando un modulo del servizio. L'altro modo consiste nell'usare [l'API Fetch](https://fetch.spec.whatwg.org/) per effettuare una richiesta all'API Ricerca [indirizzo inverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) di Azure Maps per trovare un indirizzo. Entrambi i modi sono esaminati di seguito.

## <a name="make-a-reverse-search-request-via-service-module"></a>Effettuare una richiesta di ricerca inversa con il modulo del servizio

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco costruisce un oggetto mappa e imposta il meccanismo di autenticazione per utilizzare il token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco `TokenCredential` di codice crea un per autenticare le richieste HTTP a Mappe di Azure con il token di accesso. Passa quindi `TokenCredential` l'istanza di a e crea `atlas.service.MapsURL.newPipeline()` [un'istanza Pipeline.It](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) then passes the to to and creates a Pipeline instance. `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse a un puntatore e crea un oggetto [popup.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il quarto blocco di codice aggiunge un listener di [eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)clic del mouse . Quando viene attivato, crea una query di ricerca con le coordinate del punto su cui si è fatto clic. Viene quindi utilizzato il metodo [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)per eseguire una query [sull'API inversa Get Search Address](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per l'indirizzo delle coordinate. Una raccolta di entità geografiche `geojson.getFeatures()` GeoJSON viene quindi estratta utilizzando il metodo della risposta.

Il quinto blocco di codice imposta il contenuto popup HTML per visualizzare l'indirizzo di risposta per la posizione della coordinata su cui si è fatto clic.

La modifica del cursore, dell'oggetto popup e dell'evento click vengono tutti creati nel [listener dell'evento di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)della mappa. Questa struttura di codice garantisce che la mappa venga caricata completamente prima di recuperare le informazioni sulle coordinate.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Effettuare una richiesta di ricerca inversa tramite l'API Fetch

Fare clic sulla mappa per effettuare una richiesta di geocodifica inversa per tale posizione utilizzando fetch.

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco di codice costruisce un oggetto mappa e imposta il meccanismo di autenticazione per utilizzare il token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse su un puntatore. Crea un'istanza di un oggetto [popup.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il terzo blocco di codice aggiunge un listener di eventi per i clic del mouse. Al clic del mouse, viene [usata l'API Fetch](https://fetch.spec.whatwg.org/) per eseguire una query sull'API [Ricerca indirizzo inverso](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) di Azure Maps per l'indirizzo delle coordinate su cui si è fatto clic. Per una risposta corretta, raccoglie l'indirizzo per la posizione su cui si è fatto clic. Definisce il contenuto popup e la posizione utilizzando la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe popup.

La modifica del cursore, dell'oggetto popup e dell'evento click vengono tutti creati nel [listener dell'evento di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)della mappa. Questa struttura di codice garantisce che la mappa venga caricata completamente prima di recuperare le informazioni sulle coordinate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di ricerca](how-to-use-best-practices-for-search.md)

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
