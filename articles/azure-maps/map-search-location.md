---
title: Visualizzare i risultati della ricerca con Mappe di Azure | Microsoft Docs
description: Come eseguire una richiesta di ricerca con Mappe di Azure, quindi visualizzare i risultati su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7d4eb5f9be4a6bcefe4b544d3f97a9b9391c0d81
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665778"
---
# <a name="show-search-results-on-the-map"></a>Visualizzare i risultati della ricerca sulla mappa

Questo articolo illustra come cercare la posizione di interesse e visualizzare i risultati della ricerca sulla mappa. 

## <a name="understand-the-code"></a>Informazioni sul codice

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Visualizzare i risultati della ricerca su una mappa</a> (Modulo del servizio) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa e crea un servizio client. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice usa la ricerca Fuzzy [API di ricerca Fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) per cercare il punto di interesse. L'API di ricerca Fuzzy può gestire qualsiasi combinazione di input Fuzzy. La risposta del servizio di ricerca fuzzy viene quindi analizzata in formato GeoJSON con il metodo [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse). I segnaposto vengono quindi aggiunti alla mappa per mostrare i punti di interesse sulla mappa.

L'ultimo blocco di codice aggiunge i limiti della fotocamera per la mappa usando la proprietà [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) di Mappe.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere: 

* [Azure Maps Fuzzy Search API (API di ricerca fuzzy di Mappe di Azure)](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Per altri esempi di codice da aggiungere alle mappe, vedere gli articoli seguenti: 
* [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)
* [Visualizzare le indicazioni stradali da A a B](./map-route.md)
