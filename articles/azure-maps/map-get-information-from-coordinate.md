---
title: Visualizzare le informazioni su una coordinata con Mappe di Azure | Microsoft Docs
description: Come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ada579af44d1d0b4ea08a8ae9eadbec386e44f08
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823011"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come effettuare una ricerca di indirizzi inversa che visualizza l'indirizzo di una posizione quando si fa clic sul relativo elemento popup.

Esistono due modi per eseguire una ricerca di indirizzi inversa. Un modo consiste nell'eseguire una query sull'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) usando un modulo del servizio. L'altro modo è effettuare una [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'API per trovare un indirizzo. Entrambi i modi sono esaminati di seguito.

## <a name="make-a-reverse-search-request-via-service-module"></a>Effettuare una richiesta di ricerca inversa con il modulo del servizio

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

La riga nel secondo blocco di codice crea un'istanza di servizio client.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore e un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il quarto blocco di codice aggiunge un [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per i clic del mouse. Dopo un clic del mouse, viene creata una query di ricerca con le coordinate del punto selezionato. Viene quindi usato l'endpoint [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) della mappa per recuperare l'indirizzo delle coordinate.

Per una risposta corretta, raccoglie l'indirizzo della località selezionata e definisce il contenuto del popup e la posizione tramite la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe Popup.

La modifica del cursore, un oggetto popup, e l'evento click vengono creati tutti nel [listener di eventi di caricamento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per assicurarsi che venga completato il caricamento della mappa prima di recuperare le informazioni delle coordinate.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Effettuare una richiesta di ricerca inversa con XMLHttpRequest

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Il primo blocco di codice in alto costruisce un oggetto Map. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse impostando un puntatore e un oggetto [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

Il terzo blocco di codice aggiunge un listener di eventi per i clic del mouse. Dopo un clic del mouse, invia una [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) per recuperare l'indirizzo delle coordinate selezionato. Per una risposta corretta, raccoglie l'indirizzo della località selezionata e definisce il contenuto del popup e la posizione tramite la funzione [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) della classe Popup.

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
