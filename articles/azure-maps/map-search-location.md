---
title: Visualizzare i risultati della ricerca con Mappe di Azure | Microsoft Docs
description: Come eseguire una richiesta di ricerca con Mappe di Azure, quindi visualizzare i risultati su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599882"
---
# <a name="show-search-results-on-the-map"></a>Visualizzare i risultati della ricerca sulla mappa

Questo articolo illustra come effettuare una richiesta di ricerca e visualizzare i risultati della ricerca sulla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Visualizzare i risultati della ricerca su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni, è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea e aggiunge un livello di segnaposto di ricerca sulla mappa. Per le istruzioni, è possibile vedere [Aggiungere un segnaposto sulla mappa](./map-add-pin.md).

Il terzo blocco di codice invia un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API di ricerca fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

L'ultimo blocco di codice analizza la risposta in arrivo. Se la risposta ha esito positivo, raccoglie le informazioni su latitudine e longitudine per ogni posizione restituita. Aggiunge tutti i punti delle posizioni alla mappa come segnaposto e regola i limiti della mappa per eseguire il rendering di tutti i segnaposto.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 

* [Azure Maps Fuzzy Search API (API di ricerca fuzzy di Mappe di Azure)](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)
* [Visualizzare le indicazioni stradali da A a B](./map-route.md)
