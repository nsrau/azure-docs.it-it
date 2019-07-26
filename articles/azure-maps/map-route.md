---
title: Visualizzare le indicazioni stradali con Mappe di Azure | Microsoft Docs
description: Come visualizzare le indicazioni stradali tra due posizioni su una mappa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 9e9469e012f2e6b54d9bb84858a9dfc970873b4c
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476797"
---
# <a name="show-directions-from-a-to-b"></a>Visualizzare le indicazioni stradali da A a B

Questo articolo illustra come effettuare una richiesta di pianificazione percorso e visualizzare il percorso sulla mappa.

È possibile eseguire questa operazione in due modi. Il primo modo consiste nell'eseguire una query all'[API di pianificazione percorso di Mappe di Azure](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) tramite un modulo del servizio. Il secondo modo consiste nell'usare l' [API fetch](https://fetch.spec.whatwg.org/) per eseguire una richiesta di ricerca all' [API route di Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Entrambe le modalità vengono descritte di seguito.

## <a name="query-the-route-via-service-module"></a>Eseguire una query del percorso tramite il modulo del servizio

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa (Modulo del servizio)) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione per l'uso della chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea un `SubscriptionKeyCredentialPolicy` oggetto per autenticare le richieste HTTP per le mappe di Azure con la chiave di sottoscrizione. `atlas.service.MapsURL.newPipeline()` acquisisce il criterio `SubscriptionKeyCredential` e crea un'istanza [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `routeURL` rappresenta un URL per le operazioni di [pianificazione del percorso](https://docs.microsoft.com/rest/api/maps/route) di Mappe di Azure.

Il terzo blocco di codice crea e aggiunge un oggetto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) alla mappa.

Il quarto blocco di codice crea oggetti [punti](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) di inizio e di fine e li aggiunge all'oggetto DataSource.

Una linea è una [Funzionalità](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) di LineString. [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) esegue il rendering degli oggetti linea con wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come linee sulla mappa. Il quarto blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. Il quinto blocco di codice crea e aggiunge un livello di simbolo alla mappa.

Il sesto blocco di codice esegue una query sul servizio di routing di Azure Maps, che fa parte del [modulo del servizio](how-to-use-services-module.md). Il metodo [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) di RouteUrl viene usato per ottenere una route tra i punti di inizio e di fine. Una raccolta di funzionalità GeoJSON dalla risposta viene quindi estratta usando `geojson.getFeatures()` il metodo e viene aggiunta all'origine dati. Viene quindi eseguito il rendering della risposta come un itinerario sulla mappa. Per altre informazioni sull'aggiunta di una linea alla mappa, vedere [Aggiungere una linea](./map-add-shape.md#addALine).

L'ultimo blocco di codice imposta i limiti della mappa utilizzando [la proprietà SetProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

La query per l'itinerario, l'origine dati, il livello simbolo e il livello linea e i limiti della fotocamera vengono creati e impostati all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per garantire che i risultati vengono visualizzati dopo il completo caricamento della mappa.

## <a name="query-the-route-via-fetch-api"></a>Eseguire query sulla route tramite l'API fetch

<iframe height='500' scrolling='no' title='Visualizzare le indicazioni stradali da A a B su una mappa' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Visualizzare l'elemento Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a> (Visualizzare le indicazioni stradali da A a B su una mappa) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nel codice precedente il primo blocco di codice costruisce un oggetto map e imposta il meccanismo di autenticazione per l'uso della chiave di sottoscrizione. Per le istruzioni è possibile vedere [Creare una mappa](./map-create.md).

Il secondo blocco di codice crea e aggiunge un oggetto [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) alla mappa.

Il terzo blocco di codice crea i punti di inizio e di destinazione per l'itinerario e li aggiunge all'origine dati. Per istruzioni sull'uso di [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest), è possibile vedere [Aggiungere i segnaposto alla mappa](map-add-pin.md).

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) esegue il rendering degli oggetti linea con wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come linee sulla mappa. Il quarto blocco di codice crea e aggiunge un livello linea alla mappa. Vedere le proprietà di un livello linea in [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

Un [livello simbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa testo o icone per il rendering dei dati basati su punti di cui viene eseguito il wrapping in [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) come simboli sulla mappa. Il quinto blocco di codice crea e aggiunge un livello di simbolo alla mappa. Vedere le proprietà di un livello simbolo in [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Il blocco di codice successivo crea i punti `SouthWest` e `NorthEast` dai punti di inizio e di destinazione e imposta i limiti della mappa tramite la proprietà [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) della mappa.

L'ultimo blocco di codice usa l' [API fetch](https://fetch.spec.whatwg.org/) per eseguire una richiesta di ricerca all' [API route di Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). La risposta viene quindi analizzata. Se la risposta ha avuto esito positivo, le informazioni di latitudine e longitudine vengono usate per creare una linea di matrice connettendo tali punti. I dati della riga vengono quindi aggiunti all'origine dati per eseguire il rendering della route sulla mappa. Per le istruzioni, è possibile vedere [add a line on the map](./map-add-shape.md#addALine) (Aggiungere una linea sulla mappa).

La query per l'itinerario, l'origine dati, il livello simbolo e il livello linea e i limiti della fotocamera vengono creati e impostati all'interno del [listener di eventi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) della mappa per garantire che i risultati vengono visualizzati dopo il completo caricamento della mappa.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi e sui metodi usati in questo articolo, vedere:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Per esempi di codice completi, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Mostrare il traffico sulla mappa](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interazione con la mappa - eventi del mouse](./map-events.md)
