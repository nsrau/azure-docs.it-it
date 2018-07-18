---
title: Visualizzare le informazioni su una coordinata con Mappe di Azure | Microsoft Docs
description: Come visualizzare le informazioni su un indirizzo sulla mappa quando un utente seleziona una coordinata
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3caae47f7f8f5f9c917e3a59513e6cd33cdcaeae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600494"
---
# <a name="get-information-from-a-coordinate"></a>Ottenere informazioni su una coordinata

Questo articolo illustra come eseguire una ricerca di indirizzi inversa e, al clic del mouse, come visualizzare l'indirizzo della posizione che ha ricevuto il clic in un popup. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Ottenere informazioni su una coordinata' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a> (Ottenere informazioni su una coordinata) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni, è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice aggiorna lo stile del cursore del mouse modificandolo in un puntatore.

Il terzo blocco di codice crea un popup. Per le istruzioni, è possibile vedere [Aggiungere un popup sulla mappa](./map-add-popup.md).

L'ultimo blocco di codice aggiunge un listener di eventi per i clic del mouse. Quando un utente fa clic con il mouse, invia un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API di ricerca di indirizzi inversa di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Se la risposta ha esito positivo, raccoglie l'indirizzo della località che ha ricevuto il clic e definisce il contenuto del popup e la posizione tramite la funzione [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) della classe Popup

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 
* [Reverse address search (Ricerca di indirizzi inversa)](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Visualizzare le indicazioni stradali da A a B](./map-route.md)
* [Visualizzare il traffico](./map-show-traffic.md)
