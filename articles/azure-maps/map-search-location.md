---
title: Visualizzare i risultati della ricerca con Mappe di Azure | Microsoft Docs
description: Come eseguire una richiesta di ricerca con Mappe di Azure, quindi visualizzare i risultati su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271595"
---
# <a name="show-search-results-on-the-map"></a>Visualizzare i risultati della ricerca sulla mappa

Questo articolo illustra come cercare la posizione di interesse e visualizzare i risultati della ricerca sulla mappa.

Esistono due modi per eseguire la ricerca di una posizione di interesse. Uno consiste nell'usare un modulo del servizio per effettuare una richiesta di ricerca. L'altro consiste nel effettuare una richiesta di ricerca per [API di ricerca Fuzzy di mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) tramite il [recuperare API](https://fetch.spec.whatwg.org/). Entrambe le modalità vengono descritte di seguito.

## <a name="make-a-search-request-via-service-module"></a>Effettuare una richiesta di ricerca tramite il modulo del servizio

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Visualizzare i risultati della ricerca su una mappa</a> (Modulo del servizio) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione da usare la chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un `SubscriptionKeyCredentialPolicy` per autenticare le richieste HTTP per le mappe di Azure con la chiave di sottoscrizione. Il `atlas.service.MapsURL.newPipeline()` accetta il `SubscriptionKeyCredential` dei criteri e consente di creare un [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) istanza. `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice crea un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e aggiunge i risultati della ricerca in tale oggetto. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa.  Viene quindi creato un livello simbolo e l'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

Il quarto blocco di codice Usa il [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metodo nella [modulo servizio](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). Consente di eseguire una ricerca di testo in formato libero tramite il [API rest Get Search Fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) da cercare per punto di interesse. Get Search Fuzzy API può gestire qualsiasi combinazione di input fuzzy. Dalla risposta viene quindi estratta con il metodo `geojson.getFeatures()` una raccolta di caratteristiche GeoJSON che viene aggiunta all'origine dati determinando così automaticamente il rendering dei dati nella mappa tramite il livello simboli.

L'ultimo blocco di codice regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

La ricerca richiesta, vengono creati nell'origine dati e il livello di simboli e i limiti della fotocamera e all'interno della mappa impostandoli pronti [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) per garantire che i risultati vengono visualizzati dopo che la mappa è stato caricato completamente.


## <a name="make-a-search-request-via-fetch-api"></a>Effettuare una richiesta di ricerca tramite l'API Fetch

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Visualizzare i risultati della ricerca su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente, il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione da usare la chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un URL per effettuare una richiesta di ricerca per. Crea inoltre due matrici per archiviare i limiti e i pin dei risultati della ricerca.

Il terzo blocco di codice Usa il [API Fetch](https://fetch.spec.whatwg.org/) a cui inviare una richiesta [API di ricerca Fuzzy di mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) per cercare i punti di interesse. L'API di ricerca fuzzy può gestire qualsiasi combinazione di input fuzzy. Quindi gestisce e analizza la risposta della ricerca e aggiunge i pin risultato alla matrice searchPins.

Il quarto blocco di codice crea un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) e aggiunge i risultati della ricerca in tale oggetto. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. Viene quindi creato un livello simbolo e l'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

L'ultimo blocco di codice crea un oggetto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) usando la matrice dei risultati e quindi regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa. Quindi eseguito il rendering i pin di risultato.

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
