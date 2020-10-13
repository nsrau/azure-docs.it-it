---
title: Visualizzare i risultati della ricerca in una mappa | Mappe di Microsoft Azure
description: In questo articolo si apprenderà come eseguire una richiesta di ricerca usando Microsoft Azure Maps Web SDK e come visualizzare i risultati sulla mappa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be6d508da15b7c403259bd66c86c3b3e72ff2f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089274"
---
# <a name="show-search-results-on-the-map"></a>Visualizzare i risultati della ricerca sulla mappa

Questo articolo illustra come cercare la posizione di interesse e visualizzare i risultati della ricerca sulla mappa.

Esistono due modi per eseguire la ricerca di una posizione di interesse. Uno consiste nell'usare un modulo del servizio per effettuare una richiesta di ricerca. L'altro consiste nell'eseguire una richiesta di ricerca all'[API di ricerca fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) tramite l'[API di recupero](https://fetch.spec.whatwg.org/). Entrambe le modalità vengono descritte di seguito.

## <a name="make-a-search-request-via-service-module"></a>Effettuare una richiesta di ricerca tramite il modulo del servizio

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>Visualizzare i risultati della ricerca su una mappa</a> (Modulo del servizio) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco costruisce un oggetto mappa e imposta il meccanismo di autenticazione per l'uso del token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un oggetto `TokenCredential` per autenticare le richieste HTTP a Mappe di Azure con il token di accesso. Passa quindi `TokenCredential` a `atlas.service.MapsURL.newPipeline()` e crea un'istanza di [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline). `searchURL` rappresenta un URL per le operazioni di [ricerca](https://docs.microsoft.com/rest/api/maps/search) di Mappe di Azure.

Il terzo blocco di codice crea un oggetto origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) e aggiunge i risultati della ricerca in tale oggetto. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) come simboli sulla mappa.  Viene quindi creato un livello simbolo. L'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

Il quarto blocco di codice usa il metodo [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) nel [modulo del servizio](how-to-use-services-module.md). Consente di eseguire una ricerca di testo in formato libero tramite l'[API REST GET di ricerca fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) per cercare un punto di interesse. Le richieste GET all'API di ricerca fuzzy possono gestire qualsiasi combinazione di input fuzzy. Dalla risposta viene quindi estratta con il metodo `geojson.getFeatures()` una raccolta di caratteristiche GeoJSON che viene aggiunta all'origine dati determinando così automaticamente il rendering dei dati nella mappa tramite il livello simboli.

L'ultimo blocco di codice regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

La richiesta di ricerca, l'origine dati, il livello simbolo e i limiti della fotocamera si trovano all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) della mappa. È necessario assicurarsi che i risultati vengano visualizzati dopo il caricamento completo della mappa.


## <a name="make-a-search-request-via-fetch-api"></a>Effettuare una richiesta di ricerca con l'API di recupero

<iframe height='500' scrolling='no' title='Visualizzare i risultati della ricerca su una mappa' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> (Visualizzare i risultati della ricerca su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto mappa. Imposta il meccanismo di autenticazione per usare il token di accesso. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un URL per eseguire una richiesta di ricerca. Crea anche due matrici per archiviare i limiti e i segnaposto dei risultati della ricerca.

Il terzo blocco di codice usa l'[API di recupero](https://fetch.spec.whatwg.org/). L'[API di recupero](https://fetch.spec.whatwg.org/) viene usata per effettuare una richiesta all'[API di ricerca fuzzy di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) per cercare i punti di interesse. L'API di ricerca fuzzy può gestire qualsiasi combinazione di input fuzzy. Gestisce e analizza quindi la risposta alla ricerca e aggiunge i segnaposto dei risultati alla matrice searchPins.

Il quarto blocco di codice crea un oggetto di origine dati usando la classe [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource). Nel codice i risultati della ricerca vengono aggiunti all'oggetto di origine. Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource) come simboli sulla mappa. Viene quindi creato un livello simbolo. L'origine dati viene aggiunta al livello simbolo, che viene quindi aggiunto alla mappa.

L'ultimo blocco di codice crea un oggetto [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox). Usa la matrice di risultati e quindi regola i limiti della fotocamera per la mappa usando la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa. Esegue quindi il rendering dei segnaposto dei risultati.

La richiesta di ricerca, l'origine dati, il livello simbolo e i limiti della fotocamera vengono impostati all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#events) della mappa per garantire che i risultati vengano visualizzati dopo il caricamento completo della mappa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per l'uso del servizio di ricerca](how-to-use-best-practices-for-search.md)

Ulteriori informazioni sulla **ricerca fuzzy**:

> [!div class="nextstepaction"]
> [Azure Maps Fuzzy Search API (API di ricerca fuzzy di Mappe di Azure)](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Ottenere informazioni su una coordinata](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Visualizzare le indicazioni stradali da A a B](map-route.md)
