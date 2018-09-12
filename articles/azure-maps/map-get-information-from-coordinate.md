---
title: Visualizzare le informazioni su una coordinata con Mappe di Azure | Microsoft Docs
description: Come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667482"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come eseguire una ricerca di indirizzi inversa e, al clic del mouse, come visualizzare l'indirizzo della posizione che ha ricevuto il clic in un popup.

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata (Modulo del servizio)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Ottenere informazioni su una coordinata</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

La riga nel secondo blocco di codice crea un'istanza di un client del servizio.

Il terzo blocco di codice aggiorna lo stile del cursore del mouse modificandolo in un puntatore.

Il quarto blocco di codice crea una finestra popup. Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

L'ultimo blocco di codice aggiunge un listener di eventi per i clic del mouse. Dopo un clic del mouse, viene creata una query di ricerca con le coordinate del punto selezionato. Quindi viene usato l'endpoint [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) della mappa per la query dell'indirizzo delle coordinate.

Se la risposta ha esito positivo, raccoglie l'indirizzo della località che selezionata e definisce il contenuto del popup e la posizione tramite la funzione [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) della classe Popup.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Reverse address search (Ricerca di indirizzi inversa)](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti:
* [Visualizzare le indicazioni stradali da A a B](./map-route.md)
* [Visualizzare il traffico](./map-show-traffic.md)
