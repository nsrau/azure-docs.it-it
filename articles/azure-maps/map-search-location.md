---
title: Visualizzare i risultati della ricerca con Mappe di Azure | Microsoft Docs
description: Come eseguire una richiesta di ricerca con Mappe di Azure, quindi visualizzare i risultati su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 031085b3048d0ffc92dd5a35b4054903088b4858
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824337"
---
# <a name="show-search-results-on-the-map"></a>Visualizzare i risultati della ricerca sulla mappa

Questo articolo illustra come cercare la posizione di interesse e visualizzare i risultati della ricerca sulla mappa.

Esistono due modi per eseguire la ricerca di una posizione di interesse. Uno consiste nell'usare un modulo del servizio per effettuare una richiesta di ricerca. L'altro consiste nell'inviare una richiesta di ricerca tramite [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API di ricerca fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Entrambe le modalità vengono descritte di seguito.

## <a name="make-a-search-request-via-service-module"></a>Effettuare una richiesta di ricerca tramite il modulo del servizio

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Visualizzare i risultati della ricerca su una mappa</a> (Modulo del servizio) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco costruisce un oggetto mappa e inizializza il servizio client. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice usa il metodo [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) nel [modulo del servizio](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). Consente di eseguire una ricerca di testo in formato libero tramite l'[API REST di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) per cercare un punto di interesse. L'API di ricerca Fuzzy può gestire qualsiasi combinazione di input Fuzzy. La risposta del servizio di ricerca fuzzy viene quindi analizzata in formato GeoJSON con il metodo [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse). 

Il terzo blocco di codice crea un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e aggiunge i risultati della ricerca in tale oggetto. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  Viene quindi creato un livello simbolo e l'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

L'ultimo blocco di codice regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

La richiesta di ricerca, l'origine dati, il livello simbolo e i limiti della fotocamera vengono creati e impostati all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per garantire che i risultati vengano visualizzati dopo il completo caricamento della mappa.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Effettuare una richiesta di ricerca tramite XMLHttpRequest

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Visualizzare i risultati della ricerca su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice invia un oggetto [XMLHttpRequest](https://xhr.spec.whatwg.org/) all'[API di ricerca fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). L'API di ricerca fuzzy può gestire qualsiasi combinazione di input fuzzy. 

Il terzo blocco di codice analizza la risposta della ricerca e archivia i risultati in una matrice per calcolare i limiti. Restituisce quindi i risultati della ricerca.

Il quarto blocco di codice crea un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e aggiunge i risultati della ricerca in tale oggetto. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. Viene quindi creato un livello simbolo e l'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

L'ultimo blocco di codice crea un oggetto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) usando la matrice dei risultati e quindi regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa. Esegue quindi il rendering dei segnaposto dei risultati.

La richiesta di ricerca, l'origine dati, il livello simbolo e i limiti della fotocamera vengono impostati all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per garantire che i risultati vengano visualizzati dopo il completo caricamento della mappa.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla **ricerca fuzzy**:

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API (API di ricerca fuzzy di Mappe di Azure)](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](./map-route.md)
